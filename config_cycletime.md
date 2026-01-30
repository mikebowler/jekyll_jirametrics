---
layout: page
permalink: /config/cycletime/
title: Configuring the start and end points (cycletime)
---

## `cycletime`

The cycletime for the overall board is defined inside the [board]({% link config_project.md %}#board) declaration. Then it can be overridden in individual charts as needed. It is in the cycle time that we define the start and end points that are used for every flow calculation.

The `cycletime` has two components which define when we consider the work started and when we consider it stopped. For either of `start_at` or `stop_at`, we have a variety of ways to determine the exact time.

For example, if we want to start the clock when the issue enters the 'In Progress' status then we could write<br />`start_at first_time_in_status 'In Progress'`

{: .tip }
The one we use more often than any other is `first_time_in_or_right_of_column`. Consider if this one meets your needs before looking through all the others.

The full set of options that we can use for this are below.

| method | description |
|---+---|
| `currently_in_status` | Similar to `first_time_in_status` except that it only matches if the work is still in that status. If it has moved out of that status, it will no longer match.<br />`currently_in_status 'In Progress'` |
| `currently_in_status_category` | Similar to `first_time_in_status_category` except that it only matches if the work is still in that category. |
| `first_time_in_or_right_of_column` | Returns the first time the issue enters this column or any column the right of it. Example: `first_time_in_or_right_of_column 'In Progress'`
| `first_time_in_status` | Returns the first time the issue enters one of the specified statuses. |
| `first_time_in_status_category` | Returns the first time we entered a status belonging to the specified status category. Categories in an English language Jira instance will be "To Do", "In Progress" and "Done". |
| `first_time_label_added` | Returns the first time a specific label has shown up on the ticket. Added in v2.8 |
| `first_time_not_in_status` | Takes a list of status names and returns the first time that the issue is NOT in one of these statuses. Commonly used if there are a couple of columns at the beginning of the board that we don't want to consider for the purposes of calculating cycletime. |
| `still_in_or_right_of_column` | Same as `first_time_in_or_right_of_column` except that it still has to be in one of these columns |
| `still_in_status` | If an issue has ever been in one of these statuses AND is still in one of these statuses then was was the last time it entered one? This is useful for tracking cases where an item moves forward on the board, then backwards, then forward again. We're tracking the last time it entered the named status. Important: If you have two status changes in a row and both of them return true then this returns the _first_ timestamp. There are subtle cases where we want this behaviour although most of the time, you'd be better off using `currently_in_status` |
| `still_in_status_category` | If an issue has ever been in one of these category AND is still in one of these category then was was the last time it entered one? This is useful for tracking cases where an item moves forward on the board, then backwards, then forward again. We're tracking the last time it entered the named category. Important: If you have two status changes in a row and both of them return true then this returns the _first_ timestamp. There are subtle cases where we want this behaviour although most of the time, you'd be better off using `currently_in_status_category` |
| `first_status_change_after_created` | Returns the timestamp of the first status change after the issue was created. |
| `time_created` | Returns the creation timestamp of the issue |
| `first_time_visible_on_board`| returns the timestamp when the issue first became visible on the board |
| `first_time_added_to_active_sprint` | If this issue will ever be in an active sprint then return the time that it was first added to that sprint, whether or not the sprint was active at that time. Although it seems like an odd thing to calculate, it's a reasonable proxy for 'ready' in cases where the team doesn't have an explicit 'ready' status. You'd be better off with an explicit 'ready' but sometimes that's not an option. |


What if there aren't any built-in methods to extract the piece of data that you want? You can pass in an arbitrary bit of code that will get executed for each issue.

```ruby
start_at ->(issue) { issue.get_whatever_data_you_want }
```

{: #override }
### Changing the cycletime for one chart

Sometimes we want to change the cycletime just for one chart. For example, I might want to have one WIP chart that shows all bugs from their creation date, not the started date.

In this case, we put a cycletime inside the chart declaration and it will override the one that was set on the board.

```ruby
daily_wip_chart do
  cycletime do
    start_at time_created
    stop_at first_time_in_or_right_of_column('Done')
  end
end
```