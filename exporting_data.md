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

### Methods to retrieve data ###

* **first_time_in_status** or **first_time_in_status_category** Returns the timestamp of the first time we entered the respective grouping.
* **first_time_not_in_status** takes a list of status names and returns the timestamp of the first time that the issue is NOT in one of these statuses. Commonly used if there are a couple of columns at the beginning of the board that we don't want to consider for the purposes of calculating cycletime.
* **currently_in_status** or **currently_in_status_category** Takes a list of names. If the most recent status change matches the specified condition then return that timestamp.
* **still_in_status** or **still_in_status_category** Takes a list of names. If an issue has ever been in one of these statuses AND is still in one of these statuses then was was the last time it entered one? This is useful for tracking cases where an item moves forward on the board, then backwards, then forward again. We're tracking the last time it entered the named status. Important: If you have two status changes in a row and both of them return true then this returns the *first* timestamp. There are subtle cases where we want this behaviour although most of the time, you'd be better off using **currently_in[status|status_category**
* **first_status_change_after_created** Returns the timestamp of the first status change after the issue was created.
* **time_created** Returns the creation timestamp of the issue
* **key** The Jira issue number
* **type** The issue type
* **summary** The issue description
* **url** The issue URL. Note that this is not actually found in the data that Jira provides us so we fabricate it from information we do have. It's possible that the URL we generate won't work although it has in all the cases we've tested.
* **blocked_percentage** Takes two of the above date methods (first for the start time and second for the end time) and then calculates the percentage of time that this issue was marked as blocked (flagged in Jira parlance).


What if there aren't any built-in methods to extract the piece of data that you want? You can pass in an arbitrary bit of code that will get executed for each issue.

```ruby
columns do
  string 'sprint_count', ->(issue) { issue.get_whatever_data_you_want }
end
```
