---
layout: page
permalink: /config/project/
---
## Project configuration ##

### Naming the project

A name can be passed into the project and if you have multiple projects defined, we recommend that you do.

```ruby
project name: "MyName" do
  ...
end
```

### Setting the project id

There are certain situations having to do with team-managed projects where we need to know the id of the project. If this happens and we are unable to determine the project from the board itself (often we can) then you'll get an error about having to specify the project id and you do that like this.

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

### File prefix declaration

The **file_prefix** will be used in the filenames of all files created during download or during the export. This is purely for your use and does not need to match any name inside Jira.

For example, if you specify a file prefix of `sample` then it will generate a bunch of files such as `sample_board_8185_configuration.json` and `sample_meta.json`.

### Download section

The **download** section contains all the information specific to the project in Jira. There can only be one of these.

`rolling_date_count` indicates how many days back we're looking for files. For example, if we say `rolling_date_count 90` then we're retrieving any items that have closed in the last 90 days. We always return items that are still open, regardless of date. If this field isn't specified then we retrieve all issues that have ever been in this project and that's usually undesirable. This value is optional.

`no_earlier_than` will put a boundary on the start date. If the team has changed how they work, they will often want to ignore any data before a specific date and this option allows for that. If I say `no_earlier_than '2024-10-01'` then we will only retrieve information that was after that. This option will be rarely used but when it is needed, it's very helpful. This value is optional.

```ruby
download do
  rolling_date_count 90
  no_earlier_than '2024-10-01'
end
```


The board ids that you specify will be used to determine what issues to download.


```ruby
board id: 1 do
  cycletime do
    start_at first_time_in_status_category('In Progress')
    stop_at still_in_status_category('Done')
  end
  expedited_priority_names 'Critical', 'Highest', 'Immediate Gating'
end
```
**Notes about board ids:**
* To find the board id, navigate to the respective board in your web browser and then look at the URL. You'll see a parameter `rapidView` and that's your board id.
* If you're using a team-managed project (formerly called NextGen), you won't have a board id as team-managed projects don't support that concept. The bad news is that today, we don't support those projects because the API support for them is sorely lacking, even 5 years after team-managed projects were introduced. We do plan to support them at some point so if this is important to you, then reach out and let us know that it's something you need. There have been no requests to support this yet and that's why it's still low priority.

### File section

The **file** section contains information specific to the output file we're going to create. There can be multiples of these, if we're generating multiple different files.

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

The **file** section contains information about a specific file that we want to export.

**file_suffix** specifies the suffix that will be used for the generated file. If not specified, it defaults to '.csv'

The **columns** block provides information about the actual data that will be exported.

**only_use_row_if** is a bit of a hack to exclude rows that we don't want to see in the export. For example, sometimes we only want to write a row if it has either a start date or an end date or both. We could use this to exclude the row unless one of those values is present.

The full list of issues is made available in the **issues** variable so it's possible to do things like exclude issues we don't want. The sample below is excluding any issues that are either epics or sub-tasks. We'll often use it to exclude specific issues that we know have bad data.

```ruby
file do
  file_suffix '.csv'

  issues.reject! do |issue|
    %w[Sub-task Epic].include? issue.type
  end

  only_use_row_if do |row|
    row[0] || row[1]
  end
end
```

### Status category mapping declaration

**status_category_mapping** is there to work around a specific problem where statuses have been deleted from Jira but the Issue histories still reference that status. In this case, you'll get an error during the export, telling you to add a `status_category_mapping` and you put it inside the project section.

The category will always be one of 'To Do', 'In Progress', or 'Done'. The status will be the name of the status that we can't find and that will be named in the error message above.

```ruby
project do
  status_category_mapping status: 'MyNewStatus', category: 'In Progress'
end
```

### Settings

The project section also has the ability to add custom settings that are used in various places. Each of these custom settings is unique and they're described in the table below.

```ruby
project do
  ...
  settings['ignore_ssl_errors'] = true
end
```

| Settings key | Description |
|:--------|:-------|
| 'blocked_statuses' | A list of statuses that should be considered blocked. Before you start to use these, see this article on [why blocked statuses are usually a bad idea](https://improvingflow.com/2023/03/31/blocked-column.html). Example: *settings['blocked_statuses']=['Blocked']* |
| 'flagged_means_blocked' | By default, we assume that `flagged` indicates that a ticket is blocked but some teams use `flagged` to mean something else so you can turn it off with `'flagged_means_blocked' => false`. Requires JiraMetrics v2.6 or higher |
| 'stalled_statuses' | A list of statuses that should be considered stalled, same as blocked above. This is useful if you have queues in your workflow where the work is just sitting and waiting for someone to free up.|
| 'ignore_ssl_errors' | true to ignore the SSL errors that are common with self-signed certificates |
| 'intercept_jql' | Pass a lambda that can make changes to the JQL before it's executed. If you have to use this then your Jira instance is pretty poorly configured. It's here because we have to work with instances that are. |
| 'customfield_parent_links' | A list of custom_field ids that link to parent keys. If you're using Jira Advanced Roadmap then parent relationships will be set up with a custom field and this is how you set it up. |
| 'expedited_priority_names' | An array of priorities that will be considered to be expedited. ie ['Highest', 'Critical'] |

### Anonymization

Lastly, we can anonymize the report with the **anonymize** setting.

```ruby
project do
  anonymize
  ...
end
```