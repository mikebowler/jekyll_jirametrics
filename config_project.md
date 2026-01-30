---
layout: page
permalink: /config/project/
title: Configuring the Project
---
## `project`

The project declaration holds information about what is a project. This will often correspond to Jira's notion of a project but may not, as some companies will have many different teams working off a single Jira project. This project declaration is intended to wrap what one team will be doing and will usually (but isn't required to) have only one board.

{: .tip }
Putting an `x` in front of project will cause that project to be ignored. Example: `xproject`

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'
  timezone_offset '-08:00'

  project do
    # ... project 1 ...
  end

  project do
    # ... project 2 ...
  end

  xproject do
    # ... ignored project ...
  end
end
```

What if you need to have different target paths or Jira config for different projects? Each project will find the preceding settings and use those so you can redefine them at any time and the subsequent projects will use the new settings.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'

  project do
    # ... project 1 ...
  end

  target_path 'target2'
  jira_config 'other.json'

  project do
    # ... project 2 using different target and jira config ...
  end
end
```

## Naming the project

A name can be passed into the project and if you have multiple projects defined, we recommend that you do.

```ruby
project name: "MyName" do
  ...
end
```

## Setting the project id

There are certain situations having to do with team-managed projects where we need to know the id of the project. If this happens and we are unable to determine the project from the board itself (often we can) then you'll get an error about having to specify the project id. You can do that like this:

```ruby
project id: 5 do
  ...
end
```

If you have both a name and an id then you can just chain them together.

```ruby
project name: 'MyProject', id: 5 do
  ...
end
```

{: .tip }
It's rare that you'll need to specify an id so I'd ignore it until you get an error saying that it's needed.

## `file_prefix`

The **file_prefix** will be used in the filenames of all files created during download or during the export. This is purely for your use and does not need to match any name inside Jira.

For example, if you specify a file prefix of `sample` then it will generate a bunch of files such as `sample_board_8185_configuration.json` and `sample_meta.json`.

## `download`

The **download** section contains all the information specific to the project in Jira. There can only be one of these.

`rolling_date_count` indicates how many days back we're looking for files. For example, if we say `rolling_date_count 90` then we're retrieving any items that have closed in the last 90 days. We always return items that are still open, regardless of date. If this field isn't specified then we retrieve all issues that have ever been in this project and that's usually undesirable. This value is optional.

`no_earlier_than` will put a boundary on the start date. If the team has changed how they work, they will often want to ignore any data before a specific date and this option allows for that. If I say `no_earlier_than '2024-10-01'` then we will only retrieve information that was after that. This option will be rarely used but when it is needed, it's very helpful. This value is optional.

```ruby
download do
  rolling_date_count 90
  no_earlier_than '2024-10-01'
end
```

## `board`

The board section tells us what board we're pulling data for, and how we declare the [cycle time]({% link config_cycletime.md %}) for that board.


```ruby
board id: 1 do
  cycletime do
    start_at first_time_in_status_category('In Progress')
    stop_at still_in_status_category('Done')
  end
end
```

To find the board id, navigate to the respective board in your web browser and then look at the URL. You'll see a parameter `rapidView` and that's your board ID.

{: .tip }
If there isn't a board ID in your URL then that means that this project isn't classified as a Software project in Jira and you won't have access to any of the Agile features. It's highly unlikely that you'll run into this but it is possible. JiraMetrics cannot do anything useful with projects like this.

## `file`

The `file` section contains information specific to the output file we're going to create. There can be multiples of these, if we're generating multiple different files.


```ruby
project do
  file_prefix 'sample'

  download do
    # ...
  end

  file do
    # ...
  end
end
```

What the file section looks like, will depend on whether we are exporting a CSV or an HTML report.
* Configuring `file` to [generate CSV files]({% link config_file_csv.md %})
* Configuring `file` to [generate an HTML report]({% link config_file_html.md %}).

## `status_category_mapping`

`status_category_mapping` is there to work around a specific problem where statuses have been deleted from Jira but the Issue histories still reference that status. In this case, you'll get an error during the export, telling you to add a `status_category_mapping` and you put it inside the project section.

In an English language Jira instance, the category will always be one of 'To Do', 'In Progress', or 'Done'. The status will be the name of the status that we can't find, followed by a colon and then the id, and that will be named in the error message above.

```ruby
project do
  status_category_mapping status: 'MyNewStatus:3', category: 'In Progress'
end
```

What if you only know the status name and not the id? Put that in the configuration anyway and we'll hopefully give you a better error message that might tell you what the ID is.

```ruby
project do
  # Note that the id is missing for MyNewStatus
  status_category_mapping status: 'MyNewStatus', category: 'In Progress'
end
```

## `anonymize`

Lastly, we can anonymize the report with the `anonymize` setting.

```ruby
project do
  anonymize
  ...
end
```

{: #settings }
## Project specific settings

The project section also has the ability to add custom settings that are used in various places. Each of these custom settings is unique and they're described in the table below.

```ruby
project do
  ...
  settings['ignore_ssl_errors'] = true
end
```

| Settings key | Description |
|:--------|:-------|
| `blocked_link_text` | If you have a link type that indicates blocked, then set this to the text that is used, such as 'Blocked by' |
| `blocked_statuses` | A list of statuses that should be considered blocked. Before you start to use these, see this article on [why blocked statuses are usually a bad idea](https://improvingflow.com/2023/03/31/blocked-column.html). <br />Example: `settings['blocked_statuses']=['Blocked']` |
| `customfield_parent_links` | A list of custom_field ids that link to parent keys. If you're using Jira Advanced Roadmap then parent relationships will be set up with a custom field and this is how you set it up. |
| `expedited_priority_names` | An array of priorities that will be considered to be expedited. ie ['Highest', 'Critical'] |
| `flagged_means_blocked` | By default, we assume that `flagged` indicates that a ticket is blocked but some teams use `flagged` to mean something else so you can turn it off with `'flagged_means_blocked' => false`. Requires JiraMetrics v2.6 or higher |
| `ignore_ssl_errors` | Set to `true` to ignore the SSL errors that are common with self-signed certificates |
| `intercept_jql` | Pass a lambda that can make changes to the JQL before it's executed. If you have to use this then your Jira instance is pretty poorly configured. It's here because we have to work with instances that are. |
| `jira_cloud` | The tool will normally auto-detect whether you're talking to an instance of Jira Cloud or Data Center. If it gets that wrong then you can use this property to override that. Values are `true` or `false` |
| `stalled_statuses` | A list of statuses that should be considered stalled, same as blocked above. This is useful if you have queues in your workflow where the work is just sitting and waiting for someone to free up.|
| `stalled_threshold_days` | The number of days of inactivity before an item becomes considered stalled. Defaults to 5 |


## Excluding issues from the data set

The full list of issues is made available in the `issues` variable so it's possible to do things like exclude issues we don't want. The sample below is excluding any issues that are either epics or sub-tasks. We'll often use it to exclude specific issues that we know have bad data.

```ruby
issues.reject! do |issue|
  %w[Sub-task Epic].include? issue.type
end
```

