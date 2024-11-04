---
layout: page
permalink: /project/file_html/
title: Configuring a File to output an HTML report
---

# `file`

The `file` section contains information about a specific file that we want to export. This page explains how to use it to export an HTML report. If you looking for the data in CSV then [click over here]({% link config_file_csv.md %})

```ruby
file do
  file_suffix '.html'

  html_report do
    discard_changes_before status_becomes: :backlog

    # List of all the charts we want to include, in the order we want to see them in the report
    cycletime_scatterplot
    cycletime_histogram
    throughput_chart
  end
end
```

## `file_suffix`

Define the suffix that will be used for the generated file. If not specified, it defaults to `.csv` so when generating an HTML report, you probably want to set it.

```ruby
file_suffix '.html'
```

## `discard_changes_before`

If someone moves an issue back to the backlog, we can optionally pretend that it had never been started.

Moving things back to the backlog after they've started is a horrible practice and yet, it's extremely common. Pass a list of status names and if the issue gets moved into any one of them, we discard any history before that point. 

If you pass in `:backlog` AND you're using a Kanban board then that expands to the full list of statuses that are configured for your Backlog column. Scrum boards don't have the concept of backlog statuses so if you're using that, you'll need to explictly name the statuses.

```ruby
discard_changes_before :backlog
```

## `html_report`

The `html_report` block contains all the individual charts that you want included in the report. The charts will show up in the report in the order that they are defined here and it's ok to have the same chart show up multiple times with different options set.

### `board_id`

If you specified multiple boards in the `project` section then you'll need to specify which one of those is in use for this report.

```ruby
board_id: 1
```

{: #charts }
### Charts

There are many charts that you can add to the report and [all charts are documented here]({% link config_charts.md %}). You can add them here in the order you want to see them in the report.

```ruby
html_report do
  cycletime_scatterplot
  cycletime_histogram
  throughput_chart
end
```

{: #css }
## Styling the report

Out of the box, the report supports light mode and dark mode. It will obey whatever the Operating System tells it about whether you're in light mode or dark mode and will adjust accordingly. There is nothing you need to configure to make this happen.

If you decide that you want to customize the colours or general styling then you can override the [default CSS](https://github.com/mikebowler/jirametrics/blob/main/lib/jirametrics/html/index.css) by creating your own CSS file that will be loaded after the default one. This site is not a tutorial on CSS so all I'll say is that the colours we use are set in CSS variables and you can easily override them.

Inside your project declaration, you'll want to add a setting for `include_css`, where you specify the filename of your custom css.

```ruby
project name: 'foo' do
  setting['include_css'] = './my_custom_css.css'
end
```

One caveat is that the dependency report is currently not configurable. This is a limitation of the tool we currently use to generate that report.