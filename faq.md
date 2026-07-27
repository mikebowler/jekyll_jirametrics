---
layout: page
permalink: /faq/
---
* This will become a table of contents (this text will be scrapped).
{:toc}

----

# Errors

{: .tip }
For a concise map of the errors you're most likely to hit, with the cause and fix for each, see [Errors and how to fix them]({% link troubleshooting.md %}).

{: #q1 }
## I'm getting an error about a status not being found and was directed here

`Warning: The history for issue SP-51 references the status ("Refinement":10012) which can't be found, most likely because it was deleted from Jira after this issue passed through it...`

**What's going on.** Jira's status API only ever returns statuses that still exist. If a status was deleted after some issues had already moved through it, the issue history still references it, but Jira can no longer tell us which category it belonged to. So any status you retire mid-flight goes invisible and has to be sorted out by hand. (You would think Jira would keep that information around. It does not.)

**Do I have to chase down every deleted status?** No. The warning only fires for the deleted statuses that actually affect this export, so you'll only ever be asked about the ones that matter. Deal with the ones you're warned about and ignore the rest.

**It will usually guess correctly.** For an English-language instance, JiraMetrics guesses the category from the name: anything like "To Do", "New" or "Backlog" is treated as `To Do`; "Done", "Closed" or "Cancelled" as `Done`; everything else as `In Progress`. The warning tells you what it guessed, and if that's right you don't need to do anything. You only need to step in when the guess is wrong, or when the name doesn't make the category obvious.

**Working out the right category.** It has to be one of `To Do`, `In Progress`, or `Done`. If the name doesn't settle it, look at what the status connects to: run `jirametrics info SP-51` (or open the issue's history in Jira) and see what the missing status transitions into. If work flows out of it into your in-progress statuses, it sits before work has started, so it's a `To Do`. If work flows into it and then stops, it's a `Done`. Anything in between is `In Progress`.

**Setting the mapping.** Status names are rarely unique in Jira (plenty of instances have a dozen statuses all called "To Do"), so a mapping is keyed by `"Name":id` and the id is required. The id is right there in the warning (`"Refinement":10012` means id `10012`), and `jirametrics boards <id>` will list every current status on a board with its id.

* With `standard_project`, pass a `status_category_mappings` hash (note the trailing `s`). You can define one hash and share it across several projects:

```ruby
retired_statuses = {
  'Refinement:10012' => 'To Do',
  'Sign Off:10044' => 'Done'
}

standard_project name: 'Sample', file_prefix: 'sample',
  boards: { 44 => :default }, status_category_mappings: retired_statuses
```

* With the full `project` DSL, use the singular `status_category_mapping` method [defined here]({% link config_project.md %}#status_category_mapping).

If you're on a version of JiraMetrics earlier than 2.6, a missing status is a fatal error that stops the app rather than a warning.

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

{: #parent_key }
## JiraMetrics isn't correctly finding the parent issue for issues on my board.

Jira has a confusing history of how it has attached parents at different points in the past. For this reason, we try several different ways of identifying the parent. One of the ways that it uses is a specific custom field, which is different in every instance, so we can't automatically determine it.

Let's assume that you've identified a ticket `ABC-001` and it has a parent ticket `ABC-002` that is not being linked correctly. You need to find the JSON file for ABC-001 which will likely be found in the directory  `{target_path}/{file_prefix}_issues` or something like `target/sample_issues`. Inside that file, you need to search for the parent key, `ABC-002` in this case. You'll find it defined in a custom field.

If you then take that custom field and put it in the settings as shown, then parents will start to display properly.

```
settings['customfield_parent_links'] = ['customfield_10019']
```

----

# Data issues

{: #team-managed-kanban-backlog }
## On a team-managed kanban board, why does the data show more items in progress than I see on the board?

In a team-managed kanban project, Jira allows items to be in an in-progress status while still sitting in the backlog - they are not visible on the board until you explicitly drag them across. Unfortunately, Jira does not record this _"moved to board"_ action distinctly in the issue changelog. Both dragging an item onto the board and simply reordering items within the backlog produce an identical `Rank` changelog entry, so there is no way to tell them apart from the issue data alone.

This means JiraMetrics cannot distinguish between _"in-progress and on the board"_ and _"in-progress but still in the backlog."_ Items in the latter state will be counted as in-progress in the metrics even though they are not visible on the board.

The only workaround available today is to use a separate status for backlog items - one that is not mapped to any board column - so that items only enter an in-progress status when they are genuinely being worked on. This is good practice regardless, as it keeps your flow data accurate.

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