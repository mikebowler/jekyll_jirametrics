---
layout: page
permalink: /quickstart/
title: Quick Start
---
There are so many possible configuration options for this tool that it can be a little overwhelming to get your first report working. This page walks one path end-to-end, from nothing installed to a correct report, in a few minutes. Each step includes a way to confirm it worked before moving on.

If you haven't installed JiraMetrics yet, [follow these instructions]({% link install.md %}) first. Confirm it's ready with `jirametrics --version`.

{: .tip }
This guide uses the `verify` and `boards` commands, added in v3.1, to make the setup deterministic. If you're on an earlier version, you can still follow along: skip the `verify` step, find your board id from the board's URL (see [board]({% link config_project.md %}#board)), and start with `boards: { <id> => :default }` instead of the explicit cycle time in step 4.

## 1. Create a working directory

Create a directory to hold the data you'll download and the reports you'll generate, and move into it. Both configuration files live here, and you'll run the tool from here.

```
mkdir myreports
cd myreports
```

## 2. Create the Jira credentials file

Create a file called `jira_config.json` and populate it [as described here]({% link connecting_to_jira.md %}). On Jira Cloud it looks like this — change it to reflect your own instance:

```json
{
  "url": "https://improvingflow.atlassian.net",
  "email": "mbowler@gargoylesoftware.com",
  "api_token": "abcdefghijklmnopqrstuvwxyz"
}
```

Now create a `config.rb` with just the connection details for the moment — we'll add your board once we've looked at it:

```ruby
Exporter.configure do
  timezone_offset '-08:00'
  target_path 'target/'
  jira_config 'jira_config.json'
end
```

Change `timezone_offset` to your own; without it the report is generated in UTC, which is almost certainly not what you want.

## 3. Verify the connection

Confirm your credentials work before going any further:

```
jirametrics verify
```

A successful run prints a line like:

```
Verified https://improvingflow.atlassian.net — authenticated as Mike Bowler
```

If it can't authenticate, it tells you why — see [Errors and how to fix them]({% link troubleshooting.md %}) for the fix.

## 4. Find your board and its columns

List the boards your account can see:

```
jirametrics boards
```

Find the one you want and note its id, then look at its columns:

```
jirametrics boards 44
```

This prints the board's columns, left to right, and the statuses in each (with their To Do / In Progress / Done category). You need two of these column names: the one where work is genuinely **started**, and the one where it's **finished** (usually `Done`).

## 5. Set the cycle time and add your board

Add a `standard_project` to `config.rb`, using the two column names you picked. The `require` line at the top is mandatory — `standard_project` is a helper that lives outside the core tool.

```ruby
require 'jirametrics/examples/standard_project'

Exporter.configure do
  timezone_offset '-08:00'
  target_path 'target/'
  jira_config 'jira_config.json'

  standard_project name: 'Sample', file_prefix: 'sample', boards: {
    44 => lambda do |_|
      start_at first_time_in_or_right_of_column 'In Progress'
      stop_at first_time_in_or_right_of_column 'Done'
    end
  }
end
```

Replace `44` with your board id and the two column names with yours. This explicit start/stop is what makes your cycle time correct. There is a shortcut — `boards: { 44 => :default }` — which assumes work starts at the In Progress category and stops at Done, but that only matches about half of the boards we see, so it's worth taking the minute to set it explicitly. `first_time_in_or_right_of_column` is the most common choice; [cycletime]({% link config_cycletime.md %}) lists the other ways to define start and stop points.

**Optional:** the dependency chart included in `standard_project` needs [graphviz](https://graphviz.org/download/) (`brew install graphviz` on a mac). Nothing breaks without it — you just won't get that one chart.

## 6. Download and generate the report

```
jirametrics go
```

{: .tip }
The most common error here is one about a missing status. If you hit it — or anything else — see [Errors and how to fix them]({% link troubleshooting.md %}).

`go` downloads the data and then generates the report. You should see output something like this (you may have more lines depending on how much data there is):

```
Sample

Sample
  Downloading all statuses
  Downloading board configuration for board 44
  Downloading sprints for board 44
  Downloading primary issues for board 44
    Downloaded 1-2 of 2 issues to target/sample_issues/ 
  Downloading linked issues for board 44
Full output from downloader in jirametrics.log
Sample
```

## 7. Open the report

In the `target` directory you'll find the downloaded data plus one file with an `.html` extension, named after your `file_prefix` — so with the config above it's `target/sample.html`. Open that in a web browser and be amazed ;-)

At the top of the report is a [data quality]({% link quality_report.md %}) section — read it before trusting the charts, as it flags where the data may not mean what you'd assume.

That's it for a single board. It pulls the most recent 90 days and you can refresh any time by re-running `jirametrics go`.

---

## Adding a second board

A second board goes in the same `config.rb`. Run `jirametrics boards <id>` for it too, choose its start/stop columns, and add another `standard_project`:

```ruby
require 'jirametrics/examples/standard_project'

Exporter.configure do
  timezone_offset '-08:00'
  target_path 'target/'
  jira_config 'jira_config.json'

  standard_project name: 'Sample', file_prefix: 'sample', boards: {
    44 => lambda do |_|
      start_at first_time_in_or_right_of_column 'In Progress'
      stop_at first_time_in_or_right_of_column 'Done'
    end
  }
  standard_project name: 'Board2', file_prefix: 'board2', boards: {
    3 => lambda do |_|
      start_at first_time_in_or_right_of_column 'In Progress'
      stop_at first_time_in_or_right_of_column 'Done'
    end
  }
end
```

---

`standard_project` is the fastest way to get running but it makes a lot of assumptions about how your boards work, and you'll likely want to build your own variation. See [`standard_project`]({% link config_standard_project.md %}) for how, and the [charts]({% link config_charts.md %}) for everything you can put in a report. The tool is far more customizable than `standard_project` shows.
