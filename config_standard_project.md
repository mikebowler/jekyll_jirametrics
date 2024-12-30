---
layout: page
permalink: /config/standard_project/
title: standard project
---

## `standard_project`

If you choose to use `standard_project` then it can be inserted at the same place as the project declaration and you can even mix the two styles together if you want to use them both. Under the covers, `standard_project` just creates a full `project` declaration with a predefined configuration.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'

  standard_project name: 'Maple', file_prefix: 'maple', boards: { 1 => :default }
end
```

This is intended just as a quick way to get started and there is no expectation that this will do everything you want. In fact, we expect that it won't be long before you decide that this isn't good enough and you'll create your own version that does exactly what you need.

Take a look at [the source code](https://github.com/mikebowler/jirametrics/blob/main/lib/jirametrics/examples/standard_project.rb) for `standard_project` to see how you could make your own.

| Parameter | Description |
|:--------|:-------|
| `name` | The name that you're giving to this project. It will be used in the report but does not have to match any fields inside Jira itself. |
| `file_prefix` | This is the file prefix that will be used for all downloaded files. This is for your use only and does not have to match to any fields inside Jira. |
| `boards` | This is a hash of key/value pairs where the keys are the board ids and the values tell `jirametrics` how to determine the start and end points of every item passing through. Examples of this below. |
| `ignore_issues` | It's common that we want to exclude some specific issues from the report because they're outliers and they skew the data in undesirable ways. This is a list of ids to exclude. Example: `ignore_issues: ['ABC123' ,'ABC456']` |
| `ignore_types` | By default the tool will ignore sub-tasks and epics. This allows you to customize that. Example: `ignore_types: ['Sub-task']`. Added in v2.7 |
| `starting_status` | While it's highly undesirable to move items back to the backlog, some teams have to do that and so the tool accommodates it. This status is the one that is considered the starting point. If we move back to this, we consider it in the backlog and not started. If we don't specify this parameter and this is a kanban board, then we use whatever statuses have been configured as `backlog` for that. Sprint boards don't have this concept. |
| `status_category_mappings` | Allows you to specify extra statuses that have been deleted from Jira. Takes a hash of key/value pairs where the key is the status name and the value is the category. If you are specifying an ID as well as the name then use the format `"Review:4"` where the name is before the colon and the ID is after. |
| `rolling_date_count` | Set the number of days of history we want to look at |
| `no_earlier_than` | Restrict how far back we go by specifying one date |

The value passed into `boards` is a little more complicated. It might be `:default` which assumes that we start the item when it enters the `In Progress` status category and stop it when it crosses into the `Done` status category. You might think that all boards would be configured that way and yet we find that only about half the boards we work with, do that.

If it's not `:default` then it will be the same hash that you would normally pass into a [`cycletime`]({% link config_project.md %}#board) declaration.

```ruby
standard_project name: 'Sample', file_prefix: 'sample', boards: {
    1 => lambda do |_|
            start_at first_time_in_status('Refined')
            stop_at still_in_status_category('Done')
          end
  }
```