---
layout: post
title: SQL Server Extended Events and deadlock investigation
---

SQL Server Extended Events and deadlock investigation
-----------------------------------------------------
Tue, 07/17/2012 - 23:53

SQL2008 Introduced SQL Extended Events (XE) as a replacement for the profiler events. The profiler events still work in 2008, and I presume (but haven't tested) 2012 but already they don't seem to provide full functionality. In one recent case I cam across, a within process deadlock, there was no deadlock information captured in the profiler traces even though they were running correctly.

Instead of running continuous traces and traceflags to capture deadlocks, SQL2008 provides a set of default extended events that captures and keeps deadlock information continuously - they are on be default so you don't need to enable anything. The deadlock information only clears out when the SQL server service restarts (I suspect there is also a cleanup process that kicks in at some point - more investigation on that needed). This data is stored in XML format that can be easily queried as per the following:

	SELECT b.event.value('(@timestamp)[1]', 'datetime') as Timestamp, CAST(b.event.value('(data/value)[1]', 'varchar(max)') as xml) as Graph XML
	FROM( SELECT CAST(target_data AS XML ) AS td FROM sys .dm_xe_session_targets st
	INNER JOIN sys .dm_xe_sessions s ON s.address = st.event_session_address
	WHERE name = 'system_health') AS a
	CROSS APPLY a.nodes ('//RingBufferTarget/event' ) AS b ( event)
	WHERE b.event.value ('@name', 'varchar(20)') = 'xml_deadlock_report'
	ORDER BY b.event.value('(@timestamp)[1]', 'datetime') desc

This will return the Timestamp of the deadlock and an XML of the deadlock graph. In the server in question in this case, the query took some time, possibly because there are 151 deadlocks recorded.

The output XML looks like the following:

	<deadlock> <victim-list /> <process-list> <process id="process4bace08" taskpriority="0" logused="10000" waittime="224" schedulerid="10" kpid="588" status="suspended" spid="185" sbid="0" ecid="1" priority="0" trancount="0" lastbatchstarted="2012-08-06T20:36:00.417" lastbatchcompleted="2012-08-06T20:36:00.417"clientapp="SQLAgent - TSQL JobStep (Job 0x3D72154594D1904ABDC5C0758B8A3617 : Step 1)" hostname="405770-SQLCLUS2" hostpid="3960" isolationlevel="read committed (2)" xactid="7572801025" currentdb="5" lockTimeout="4294967295" clientoption1="673185824" clientoption2="128056"> <executionStack> <frame procname="" line="198" stmtstart="15884" stmtend="20186" sqlhandle="0x030005000a91d23713c6430194a000000100000000000000" />

To decode a sql handle use:

	SELECT * FROM fn_get_sql(0x030005000a91d23713c6430194a000000100000000000000)

SQL2012 will introduce graphical tools for Extended Events - so that is more to look forward to once I get involved in one of those. There is an open source project for SQL2008 but in this case it wouldn't work (probably because of the aformentioned query time).
Full details of the Extended Events is available here: http://msdn.microsoft.com/en-us/library/dd822788.aspx -- this post is the tip of a very large and rather cool iceburg.
