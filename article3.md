---
layout: post
title: When is synchronous asynchronous?
---

When is synchronous asynchronous?
---------------------------------

Sat, 01/26/2013 - 00:42

The official documentation says that the .Net File.Copy and File.Move methods are synchronous and I have been using it as such for quite some time in a piece of code like this:

	foreach (var filenameInFileList in fileList)
	{
		File.Move(originalFilename, originalFilename+temporaryExtension);
   		var strm = new FileStream(originalFilename+temporaryExtension, FileMode.Open, FileAccess.Read, FileShare.None);
    	...
	}

The aim being to ensure the file is no longer writeable by the source system whilst this application reads from it. Which was working fine in a heavy production environment. So imagine my surprise (and horror) when I decided to do some load testing on a dev machine by iterating through a few hundred small files only to start getting file lock errors. After further investigation, it turns out that whilst .Net is happy that the move operation is complete - and therefore can safely move on to the next operation, in fact the OS is still busy managing the files on disk, therefore holding the occasional lock (about once in every 40 files).

The solution, in this case, then is to allow locks to be held on the file when reading it.
e.g.

	foreach (var filenameInFileList in fileList)
	{
    	File.Move(originalFilename, originalFilename+temporaryExtension);
    	var strm = new FileStream(originalFilename+temporaryExtension, FileMode.Open, FileAccess.Read, FileShare.Read);
    	...
	}

Now we can fire through the files without issue.

Update: Thu, 01/31/2013 - 16:46


Someone has pointed out that possibly a virus scanner running on the test machine may be the cause of the lock being held on the file.