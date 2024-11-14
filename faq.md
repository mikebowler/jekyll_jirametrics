---
layout: page
permalink: /faq/
---
* This will become a table of contents (this text will be scrapped).
{:toc}

----

# Errors

{: #q1 }
## I'm getting an error about a status not being found

`Status name "My Status" for issue ABC-123 not found...`

What this means is that there used to be a status by that name but it's since been deleted in your Jira instance. Unfortunately, the history still references it and so we need to know information about it, but Jira no longer knows anything about it.

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

By default, the only thing that automatically actives "blocked" is the Jira flag. When the flag is enabled on a ticket, it's considered blocked.

* If your team uses Flagged for some other purpose then you can change that with the [setting]({% link config_project.md %}#settings) `flagged_means_blocked`.
* You can also designate a particular status so that the work immediately becomes blocked when entering this status. That is also in [settings]({% link config_project.md %}#settings) with the key `blocked_statuses`
* An item can be designated as blocked with the use of a link. We can say that ticket ABC-1 is blocked by ABC-2, and we configure that in [settings]({% link config_project.md %}#settings) with the key `blocked_link_text`
