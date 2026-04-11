---
layout: page
permalink: /faq/
---
* This will become a table of contents (this text will be scrapped).
{:toc}

----

# Errors

{: #q1 }
## I'm getting an error about a status not being found and was directed here

`Warning: The history for issue SP-51 references a status ("Walking":10012) that can't be found in ["In Progress":3, "Backlog":10000, "Selected for Development":10001, "Done":10002, "Review":10011, "To Do":10018, "In Progress":10019, "Done":10020].`

What this means is that there used to be a status by that name (`Walking` in this example), but it's since been deleted in your Jira instance. Unfortunately, the history still references it and we need to know what category it belongs to. You would think that Jira would version that sort of thing so we could still query that information after it was removed from use but no, it does not. Instead, you will need to specify that status to category mapping in the configuration.

* If you're using the `standard_project` declaration then use the `status_category_mapping` [as defined here](https://jirametrics.org/config/standard_project/).
* Otherwise the other `status_category_mapping` is defined [over here](https://jirametrics.org/config/project/#status_category_mapping).

If you're using a version of JiraMetrics earlier than 2.6 then the error above will be a fatal error that will stop the app. Starting with 2.6, it will just be a warning message and we will make the assumption that the missing status belongs to the `In Progress` status category. That's not always a valid assumption though so you should still set the `status_category_mapping` anyway.

{: #rate-limited }
## I'm getting an error about being rate limited.

It might really be that you've hit the Jira instance too often, and you'll have to wait until it resets. It might also be that your access token was deleted on the server and Jira is just returning a misleading message. We've seen both.

----

# Configuration

{: #stalled }
## How is "stalled" calculated and how do I change that?

"Stalled" indicates that the work cannot proceed because the team has no capacity to work on it. If we had someone available, it would be in progress.

By default, we consider an item to be stalled if there is no activity in Jira for 5 days. Activity could be a status change or a comment or the movement of a subtask. If it's being shown as stalled then it had no activity at all for that long.

* You can change the number of days in [settings]({% link config_project.md %}#settings) with the key `stalled_threshold_days`
* You can also designate a particular status so that the work immediately becomes stalled when entering this status. That is also in [settings]({% link config_project.md %}#settings) with the key `stalled_statuses`

{: #blocked }
## How is "blocked" calculated and how do I change that?

"Blocked" indicates that the work cannot proceed because of some external blocker.

By default, the only thing that automatically triggers "blocked" is the Jira flag. When the flag is enabled on a ticket, it's considered blocked. In our experience, this is by far the most common use of the Flag so we turn that on by default.

* If your team uses Flagged for some other purpose then you can change that with the [setting]({% link config_project.md %}#settings) `flagged_means_blocked`.
* You can also designate a particular status so that the work immediately becomes blocked when entering this status. That is also in [settings]({% link config_project.md %}#settings) with the key `blocked_statuses`
* An item can be designated as blocked with the use of a link. We can say that ticket ABC-1 is blocked by ABC-2, and we configure that in [settings]({% link config_project.md %}#settings) with the key `blocked_link_text`

{: #css }
## How do I customize the CSS for the report?

Perhaps you want to change the colours on the report or you want to otherwise change the appearance, we give you the ability to insert your own CSS file that will override ours.

Instructions are [over here]({% link config_file_html.md %}#css).

{: #stitcher }
## I need to create a consolidated report that pulls charts from multiple other reports into one place

Check out the [stitcher]({% link stitcher.md %})

----

# Data issues

{: #team-managed-kanban-backlog }
## On a team-managed kanban board, why does the data show more items in progress than I see on the board?

In a team-managed kanban project, Jira allows items to be in an in-progress status while still sitting in the backlog — they are not visible on the board until you explicitly drag them across. Unfortunately, Jira does not record this _"moved to board"_ action distinctly in the issue changelog. Both dragging an item onto the board and simply reordering items within the backlog produce an identical `Rank` changelog entry, so there is no way to tell them apart from the issue data alone.

This means JiraMetrics cannot distinguish between _"in-progress and on the board"_ and _"in-progress but still in the backlog."_ Items in the latter state will be counted as in-progress in the metrics even though they are not visible on the board.

The only workaround available today is to use a separate status for backlog items — one that is not mapped to any board column — so that items only enter an in-progress status when they are genuinely being worked on. This is good practice regardless, as it keeps your flow data accurate.

----

# Jira instance types

{: #data_center }
## Some features, like proper cache invalidation, are marked as Cloud only. Why is that?

There are three main reasons. 

1. We no longer have access to an instance of Jira Data Center and have to rely on people reporting bugs to try things in their environment. This is very time consuming for everyone.
2. Jira Data Center has already had it's end of life announced (March 2029) and Atlassian themselves are putting very little attention here. 
3. The API's between Cloud and Data Center are already starting to deviate and so implementing the same feature for both, sometimes requires completely different implementations.

The bottom line is that it makes no sense for us to continue adding functionality here, when the whole platform is going away. Particularly when that feature development is more time consuming for us.

If you're using Data Center today and have budget to fund development of features for Data Center then we're happy to entertain that; see our [support options](/support). We're unlikely to be adding to the Data Center support otherwise, however.