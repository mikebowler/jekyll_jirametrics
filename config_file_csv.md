---
layout: page
permalink: /project/file_csv/
title: Configuring a File to output data
---

# `file`

The `file` section contains information about a specific file that we want to export. This page explains how to use it to export CSV files. If you looking for the HTML report then [click over here]({% link config_file_html.md %})

```ruby
file do
  file_suffix '.csv'
  # This is a typical configuration for the team dashboard at FocusedObjective.com
  columns do
    write_headers true

    date 'Done', currently_in_status_category('Done')
    date 'Start', first_time_in_status_category('In Progress')
    string 'Type', type
    string 'Key', key
    string 'Summary', summary
  end
end
```

## `file_suffix`

Define the suffix that will be used for the generated file. If not specified, it defaults to `.csv`.

## `only_use_row_if`

This is a hack to exclude rows that we don't want to see in the export. This will likely be deprecated at some point in the future when we figure out a better approach.

For example, sometimes we only want to write a row if it has either a start date or an end date or both. We could use this to exclude the row unless one of those values is present.

```ruby
file do
  only_use_row_if do |row|
    row[0] || row[1]
  end
end
```

## `columns`

The `columns` block provides information about the actual data that will be exported.

Within the columns declaration, we can have a number of options.

### `write_headers`

This indicates whether we want a header row in the output or not. The default is false.

{: #type_specific_values }
### Type specific values

These are `date` or `string`

This will output a value of the appropriate type into a column of the output file. The first parameter is the name of the column and the second is a method that will be called on the Issue class. 

Methods that are frequently used here are any of methods that you would have used with `start_at` or `stop_at` in the board configuration. Also the items below.

| `key` | The Jira issue number |
| `type` | The issue type |
| summary | The issue description |
| url | The issue URL.
| blocked_percentage | Takes two of the above date methods (first for the start time and second for the end time) and then calculates the percentage of time that this issue was marked as blocked (flagged in Jira parlance).|

If there isn't already a method to do what you want, you can specify some code to calculate that value for yourself.

```ruby
columns do
  string 'sprint_count', ->(issue) { issue.get_whatever_data_you_want }
end
```

### `column_entry_times`

This will autogenerate multiple columns based on the columns found on your board and will put an entry date in each of those columns. This is useful for tools like Actionable Agile that need entry times per column. Note that to use this option, you must have specified a board_id in the project.

```ruby
# This is typical configuration for the Actionable Agile tool
columns do
  write_headers true

  string 'ID', key
  string 'link', url
  string 'title', summary
  column_entry_times
end
```

