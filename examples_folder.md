---
layout: page
permalink: /examples_folder/
---
There are a couple of example files in the [examples](https://github.com/mikebowler/jirametrics/tree/main/lib/jirametrics/examples) folder. These are intended to give you ideas of how you might use the tool, not as a finished product.

It's unlikely that these will work exactly for you simply because there is too much variation in Jira instances. For example...
* There are no link types guaranteed to be in any instance. Not even "Cloned By" is guaranteed to be there.
* Custom fields can be different across instances, meaning that common field names such as `Sprint` might have a different meaning in a different instance. I've seen this.
* Priority names are different in every project
* Issue types can be different for every project

This is why everything is so configurable in this tool. You will need to make it work with whatever configuration you have, even when the way your Jira has been configured makes no real sense.

So use these examples for ideas but don't assume that they're already perfect for you. They probably aren't.

You would use any of them by requiring them from the examples folder and then adding them to your config like this.

```ruby
require 'jirametrics/examples/standard_project'

Exporter.configure do
  timezone_offset '-08:00'

  target_path 'target/'
  jira_config 'jira_config.json'

  # Used here
  standard_project name: 'Sample', file_prefix: 'sample', boards: { 2 => :default }
end
```

What's more likely is that you'll create your own project files from the ideas here and include them.

# Standard project

The `standard_project` method is a very quick way to configure a single teams board(s).

```ruby
standard_project name: 'Sample', 
  file_prefix: 'sample', 
  boards: { 2 => :default }, 
  ignore_issues: ['ABC123' ,'ABC456'], 
  starting_status: 'todo',
  status_category_mappings: { 'MyCustomStatus' => 'In Progress' }
```

| Parameter | Description |
|:--------|:-------|
| `name` | The name that you're giving to this project. It will be used in the report but does not have to match any fields inside Jira itself. |
| `file_prefix` | This is the file prefix that will be used for all downloaded files. This is for your use only and does not have to match to any fields inside Jira. |
| `boards` | This is a hash of key/value pairs where the keys are the board ids and the values tell `jirametrics` how to determine the start and end points of every item passing through. Examples of this below. |
| `ignore_issues` | It's common that we want to exclude some specific issues from the report because they're outliers and they skew the data in undesirable ways. This is a list of ids to exclude. Example: `ignore_issues: ['ABC123' ,'ABC456']` |
| `ignore_types` | By default the tool will ignore sub-tasks and epics. This allows you to customize that. Example: `ignore_types: ['Sub-task']`. Added in v2.7 |
| `starting_status` | While it's highly undesirable to move items back to the backlog, some teams have to do that and so the tool accommodates it. This status is the one that is considered the starting point. If we move back to this, we consider it in the backlog and not started. If we don't specify this parameter and this is a kanban board, then we use whatever statuses have been configured as `backlog` for that. Sprint boards don't have this concept. |
| `status_category_mappings` | Allows you to specify extra statuses that have been deleted from Jira. Takes a hash of key/value pairs where the key is the status name and the value is the category. |
| `rolling_date_count` | Set the number of days of history we want to look at |
| `no_earlier_than` | Restrict how far back we go by specifying one date |

The value passed into `boards` is a little more complicated. It might be `:default` which assumes that we start the item when it enters the `In Progress` status category and stop it when it crosses into the `Done` status category. You might think that all boards would be configured that way and yet we find that only about half the boards we work with, do that.

If it's not `:default` then it will be the same hash that you would normally pass into a `cycletime` declaration. See the section on [[HTML reports|HTML-Reports]] for options for that.

```ruby
standard_project name: 'Sample', file_prefix: 'sample', boards: { 2 => {
  start_at first_time_in_status_category('In Progress')
  stop_at currently_in_status_category('Done')
} }
```

# Aggregated projects

The `aggregated_project` will give you the beginnings of a report across boards. This still needs a lot of work, but you may get some ideas from it.

```ruby
aggregated_project name: 'Cross team', project_names: ['Sample', 'Test']
```

It takes its own name and then a list of names for other projects that were likely all defined by `standard_project` above. It will take all the data from all of those boards and merge them together.