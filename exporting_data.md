---
layout: page
permalink: /data/
---
First see the [common configuration](https://github.com/mikebowler/jira-export/wiki/Common-configuration) and then add these, if you want to dump raw CSV files.

## Columns config ##

**write_headers** indicates whether we want a header row in the output or not. The default is false.

The **date** and **string** lines will output one of those data types into a column in the output file. The first parameter that they're passed is the name of the column and the second is a method that will be called on the Issue class. Each of those latter options are described below.

```ruby
# This is a typical configuration for the team dashboard at FocusedObjective.com
columns do
  write_headers true

  date 'Done', currently_in_status_category('Done')
  date 'Start', first_time_in_status_category('In Progress')
  string 'Type', type
  string 'Key', key
  string 'Summary', summary
end
```

**column_entry_times** will autogenerate multiple columns based on the columns found on your board and will put an entry date in each of those columns. This is useful for tools like Actionable Agile that need entry times per column. Note that to use this option, you must have specified a board_id in the project.

# Pulling data
* key** The Jira issue number
* **type** The issue type
* **summary** The issue description
* **url** The issue URL. Note that this is not actually found in the data that Jira provides us so we fabricate it from information we do have. It's possible that the URL we generate won't work although it has in all the cases we've tested.
* **blocked_percentage** Takes two of the above date methods (first for the start time and second for the end time) and then calculates the percentage of time that this issue was marked as blocked (flagged in Jira parlance).

```ruby
columns do
  string 'sprint_count', ->(issue) { issue.get_whatever_data_you_want }
end
```

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

