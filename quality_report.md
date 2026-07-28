---
layout: page
permalink: /data_quality/
title: Quality Report
---
The charts we look at will only be as good as the data that went into them, even though our own [cognitive biases](https://blog.mikebowler.ca/2024/11/10/cognitive-bias) will lead us to believe otherwise.

It's critical that we understand how good the data is, before we use it to make decisions. At the top of every report, we have a "Data Quality" section and in that, we list those things that we think are important to note.

The items highlighted there are not necessarily bad data. Some are genuine contradictions worth fixing in Jira, such as an item finishing before it started. But many are simply places where the way your team actually works bumps up against the simplified start-to-finish model this tool uses to compute flow metrics. Those aren't mistakes; we flag them so you can read the charts with the right context, not because anyone did anything wrong.

What do we check for?
1. Items that were moved "back to the backlog" after being started. This is a poor practice and yet almost every team does it.
2. Items for which we know when they completed but can't tell when they started. This usually means items moving directly from ToDo to Done.
3. Items that continued to have status changes after they were identified as having completed. Likely what we're considering 'done' isn't really done.
4. Items that moved backwards on the board. Almost always a poor practice.
5. Items that are in progress but for whatever reason, are not visible on the board.
6. Items that were created directly into a status that isn't part of the backlog, rather than starting there. Usually this means the item was created from a column on the board.
7. Items that are considered 'done' before they even started.
8. Items that are considered to not have started, yet some of their subtasks have started. Almost certainly a mistake.
9. Items that have been declared as done but which still have active subtasks.
10. Items that show up on multiple boards and that are likely being included in multiple sets of metrics.
11. Items that are marked as blocked by another issue that has already been completed, so it can no longer really be blocking.