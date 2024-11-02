---
layout: page
permalink: /reports/
---
This tool can generate a variety of reports to visualize different metrics from the data.

A html report declaration inside a project might look something like this. Documentation still needs work but most of the charts are fairly self explanatory. Try them and see what you like.

```ruby
file do
  file_suffix '.html'

  html_report do
    # Define the start and end times for all calculations
    cycletime do
      start_at first_time_in_status_category('In Progress')
      stop_at currently_in_status_category('Done')
    end

    # If someone moves an issue back to the backlog, we can optionally pretend that it
    # had never been started. This is a horrible practice and yet, it's extremely common.
    # Pass a list of status names and if the issue gets moved into any one of them, we
    # discard any history before that point. If you pass in :backlog then that expands
    # to the full list of statuses that are configured for your Backlog column (kanban
    # boards only)
    discard_changes_before status_becomes: :backlog #'To Do'

    # Reports about completed work
    cycletime_scatterplot
    cycletime_histogram
    throughput_chart

    # Reports about aging work (started but not finished)
    aging_work_in_progress_chart
    aging_work_bar_chart
    aging_work_table 'Highest'
    daily_wip_by_age_chart
    daily_wip_by_blocked_stalled_chart
    daily_wip_by_parent

    # Reports about expedited work
    expedited_chart 'Highest'

    # Scrum specific reports
    sprint_burndown
    story_point_accuracy_chart

    # Visualizing dependencies
    dependency_chart
  end
end
```

## Specifying when the work starts and stops

The `cycletime` declaration tells us how we determine the start and stop times (ie cycletime) for the items on that board.

```ruby
cycletime do
  start_at first_time_in_status_category('In Progress')
  stop_at stil_in_status_category('Done')
end
```

Why do we need so many different ways to calculate when a work item started or stopped? Because every team does things differently.

Here are the different ways you can determine either a start or stop time:

* `first_time_in_status_category` is exactly that. The first time that we enter a status belonging to the specified status category. In a normal installation of Jira, there will only be three status categories: `To Do`, `In Progress`, and `Done`. Theoretically you won't ever see others but we've seen `null` as an option.
* `still_in_status_category` means that not only has it entered that category, but it's still there. This is relevant if it was moved to Done, for example, and then moved backwards so it isn't done anymore.
* `first_time_in_status` and `still_in_status` are the same except for status names, not status category names.
* `first_time_in_or_right_of_column` and `still_in_or_right_of_column` take the name of a column and key off of that.
* `created` uses the time this issue was created.
* `first_resolution` and `last_resolution` take the time from either the first or last time the resolution was set. Typically resolution is set when an item becomes `Done` but this is again configurable.

## Cycletime Scatterplot

Plots the cycle time (y axis) against the date that the work completed (x axis).

```ruby
cycletime_scatterplot
```

Rules options

| Rule | Description |
|:--------|:-------|
| label|The name used for the group |
| color |The color used for the group. If a colour isn't set then it will be randomly chosen. |
| ignore |Discard this item from the dataset |

<img width="1224" alt="scatterplot" src="https://user-images.githubusercontent.com/1743195/172074171-a94fd773-23ad-4aa3-af5b-2bc6bdc0abb4.png">

Example

```ruby
cycletime_scatterplot do
  grouping_rules do |issue, rules|
    # Put all data into groups by type. Use the name of the
    # type as the label for the group
    rules.label = issue.type
    if issue.type == 'Story'
      # Set the colour of stories to be green
      rules.color = 'green'
    else issue.type == 'Spike'
      # Ignore spikes
      rules.ignore
    else
      rules.color = 'yellow'
    end
  end
end
```

## Cycletime Histogram

Plots the distribution of cycle times. How many times did something complete in three days?

By looking at the histogram, we can see groupings of different types of work and we can also tell how predictable the work is by how much the cycle times cluster together.

```ruby
cycletime_histogram
```

This chart supports the same grouping rules as Throughput Chart. See that chart for an example..

Rules options

| Rule | Description |
|:--------|:-------|
| label|The name used for the group |
| color |The color used for the group. If no color is specified then it will be randomly chosen. |
| ignore |Discard this item from the dataset |

<img width="1224" alt="histogram" src="https://user-images.githubusercontent.com/1743195/172074191-d07480f7-49d3-413e-a9e5-bf9cc95449e5.png">

## Throughput Chart

A line chart showing how many items completed each week (Monday to Sunday)

```ruby
throughput_chart
```

By default, this splits data across issue types and also shows a totals line.

Rules options

| Rule | Description |
|:--------|:-------|
| label|The name used for the group |
| color |The color used for the group. If no color is specified then it will be randomly chosen. |
| ignore |Discard this item from the dataset |

Example

```ruby
throughput_chart do
  grouping_rules do |issue, rules|
    # Put all data into groups by type. Use the name of the
    # type as the label for the group
    rules.label = issue.type
    if issue.type == 'Story'
      # Set the colour of stories to be green
      rules.color = 'green'
    else issue.type == 'Spike'
      # Ignore spikes
      rules.ignore
    else
      rules.color = 'yellow'
    end
  end
end
```

<img width="1224" alt="throughput" src="https://user-images.githubusercontent.com/1743195/172074204-adcd5850-2d00-4be4-b0e7-fbc50270abaa.png">

## Aging Work in Progress Chart

For items that have started but not finished, what column are they currently in and how old are they?

```ruby
aging_work_in_progress_chart
```

This chart supports the same grouping rules as Throughput Chart. See that chart for an example..

Rules options

| Rule | Description |
|:--------|:-------|
| label|The name used for the group |
| color |The color used for the group. If no color is specified then it will be randomly chosen. |
| ignore |Discard this item from the dataset |

## Daily WIP by Age

For each day in the period, how many items were in progress? Items are colour coded based on how long they've been in progress.

```ruby
daily_wip_by_age_chart
```

<img width="1433" alt="Screen Shot 2022-09-18 at 10 26 13 AM" src="https://user-images.githubusercontent.com/1743195/190920651-b332cce2-e703-4b0d-9598-d53ebe6929a0.png">

## Daily WIP by Blocked / Stalled

For each day in the period, how many items are blocked (Flagged in Jira terms) or stalled (no status changes in the last five days)?

```ruby
daily_wip_by_blocked_stalled_chart
```

<img width="1433" alt="Screen Shot 2022-09-18 at 10 26 27 AM" src="https://user-images.githubusercontent.com/1743195/190920668-8c8ee505-32b6-4331-a7a0-466f4aa5c16c.png">

## Daily WIP by Parent

Grouping the WIP by the parent ticket. This is useful to see if we're focused on more strategic goals (small number of epics) or whether our focus is scattered.

```ruby
daily_wip_by_parent
```

## Custom Daily WIP charts

The two charts above are just customized versions of the more generic `daily_wip_chart`. If you want to build your own, you can do that with code like the example below. This example groups the daily WIP by the parent of the ticket in progress.

```ruby
daily_wip_chart do
  header_text 'Daily WIP by Parent'
  description_text <<-TEXT
    How much work is in progress, grouped by the parent of the issue. This will give us an
    indication of how focused we are on higher level objectives. If there are many parent
    tickets in progress at the same time, either this team has their focus scattered or we
    aren't doing a good job of splitting those parent tickets. Neither of those is desirable.
  TEXT
  grouping_rules do |issue, rules|
    rules.label = issue.parent&.key || 'No parent'
    rules.color = 'white' if rules.label == 'No parent' 
  end
end
```

## Expedited chart

This chart shows how many items are expedited and how long they've been that way. This chart takes a parameter, which is the name of the Priority that defines "expedited".

```ruby
expedited_chart 'Critical'
```

## Sprint Burndown

Displays all the sprint burndowns that happened during this period. By default, this renders two charts - the top one is burndown by story points and the bottom one is burndown by story count. If you only want one or the other then you can customize that.

```ruby
# Generate both burndowns
sprint_burndown

# Generate only the story point burndown
sprint_burndown :points_only

# Generate only the story count burndown
sprint_burndown :counts_only
```

## Story Point Accuracy Chart

Graphs story point estimates (y axis) against the actual cycle time of the item. It's useful to be able to see how much correlation there is between the estimates and the actual time it took.


![Estimate accuracy](https://github.com/mikebowler/jirametrics/assets/1743195/f1550ea3-4746-4727-a593-8c6d85bc4422)

_Spoiler: There is never any correlation between the two, which begs the question "why we even do story point estimates if they're never accurate?"_

```ruby
story_point_accuracy_chart
```

What if you don't use the story point field and use something custom like TShirt sizes? You can specify that with the yaxis

```ruby
story_point_accuracy_chart do
  y_axis(sort_order: %w[Small Medium Large], label: "TShirt Sizes") do |issue, started_time|
    issue.raw['fields']['custom_field_34'] 
  end 
end
```

**Note:** In this example, _custom_field_34_ is meant to show what's possible. It may not be that field name in your instance.

| Parameters | Description |
|:--------|:-------|
| sort_order|All the possible options in the order you want to see them displayed. Bottom to top. |
| label |The label you want displayed on the axis |
| block |The code that will extract the value from the issue object. This is custom to your setup |


## Dependency Chart

Jira gives you the ability to link issues. So you can say that one issue depends on another or one blocks another. This visualizes all of those relationships.

```ruby
dependency_chart
```

Note that this requires graphviz to be installed. See the [GraphViz](https://graphviz.org/download/) website for installation instructions.

You can customize this chart with a bit of code

**Customizing the issue boxes**

Rules options

| Rule | Description |
|:--------|:-------|
| color |The color used for the group |
| ignore |Discard this item from the dataset |

Example

```ruby
dependency_chart do
  # Set custom colours based on the type of the object.
  # Note that this sample just uses the same colour scheme
  # that you get by default so you'll probably want to change
  # the colour values if you're using this.
  issue_rules do |issue, rules|
    rules.color = case issue.type
    when 'Story'
      '#90EE90'
    when 'Task'
      '#87CEFA'
    when 'Bug', 'Defect'
      '#ffdab9'
    when 'Epic'
      '#fafad2'
    else
      '#dcdcdc'
    end

    # Ignore sub tasks
    rules.ignore if issue_type == 'Sub-task'
  end
end
```

**Customizing the links between issues**

Rules

| Rule | Description |
|:--------|:-------|
| line_color |The color used for the group |
| ignore |Discard this item from the dataset |
| merge_bidirectional keep: 'outward' | If there are bidirectional links (ie A depends on B and B also depends on A) then only draw one of the two lines |
| use_bidirectional_arrows | If there are bidirectional links then display an arrow at both ends of the line. |

```ruby
dependency_chart do
  link_rules do |link, rules|
    case link.name
    when 'Cloners'
      # We don't want to see any clone links at all.
      rules.ignore
    when 'Blocks'
      # For blocks, by default Jira will have links going both
      # ways and we want them only going one way. Also make the
      # link red.
      rules.merge_bidirectional keep: 'outward'
      rules.line_color = 'red'
    when 'Sync'
      # For sync, also only show one link but this time put 
      # arrows at both ends
      rules.merge_bidirectional keep: 'outward'
      rules.use_bidirectional_arrows
    end
  end
end
```

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