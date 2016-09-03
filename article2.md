---
layout: post
title: 10 Lines of code per day
---

10 Lines of code per day
------------------------
Thu, 01/24/2013 - 00:44

After installing Atlassian's FishEye code browse/search tool (and its peer review partner, Crucible), I decided to run a few numbers over the last 12 months of activity. Because we use Mercurial rather than Git, FishEye doesn't give lines of code per commit - so I did some random sampling and guestimation of the average commit. I then summed the number of commits across all projects and divided by the number of developers (fudging that number a bit to take minor committers into account - ie. summing up a bunch single digit committers into one person). After all that I ended up with a number of between 10-12 lines of code per developer per day. This is across all languages (Java, SQL, C#, JavaScript), all products and all development teams (3 continents worth).

Wondering if my vague calculations had any bearing on the real world I handed the question to Google - and was immediately returned references to the book "The Mythical Man Month", which states that 10 lines of code per developer per day is, in fact, the expected quantity. The Mythical Man Month was published in 1975, which makes it interesting how far things haven't changed!

So, awesome, I now have a new bow in the estimation arsenal - if I have a hunch on the number of lines involved, then I now know the approximate number of days to develop requested feature/bug. That technique will go along with all the other gut-feels and past-experiences to work out a final estimation number.

When I get time I will do some Mercurial queries and get a more accurate number - and see how that number compares across individual projects/technologies. But for now, I have something to work with.
