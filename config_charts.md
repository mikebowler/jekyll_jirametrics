---
layout: page
permalink: /charts/
title: Charts
---

This page lists all the built-in charts you can put in an HTML report.

_It is possible to create your own charts without forking the codebase, although the process isn't obvious or documented. If you really want to do that then [reach out]({% link about.md %}) and we can talk._

## `cycletime_scatterplot`

Plots the cycle time (y axis) against the date that the work completed (x axis).

```ruby
cycletime_scatterplot
```

You can customize this report with `grouping_rules` as shown below.

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
      # Set the color of stories to be green
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

----

## `cycletime_histogram`

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

----

## `throughput_chart`

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

----

## `aging_work_in_progress_chart`

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

----

## `daily_wip_by_age_chart`

For each day in the period, how many items were in progress? Items are colour coded based on how long they've been in progress.

```ruby
daily_wip_by_age_chart
```

<img width="1433" alt="Screen Shot 2022-09-18 at 10 26 13 AM" src="https://user-images.githubusercontent.com/1743195/190920651-b332cce2-e703-4b0d-9598-d53ebe6929a0.png">

----

## `daily_wip_by_blocked_stalled_chart`

For each day in the period, how many items are blocked (Flagged in Jira terms) or stalled (no status changes in the last five days)?

```ruby
daily_wip_by_blocked_stalled_chart
```

<img width="1433" alt="Screen Shot 2022-09-18 at 10 26 27 AM" src="https://user-images.githubusercontent.com/1743195/190920668-8c8ee505-32b6-4331-a7a0-466f4aa5c16c.png">

----

## `daily_wip_by_parent`

Grouping the WIP by the parent ticket. This is useful to see if we're focused on more strategic goals (small number of epics) or whether our focus is scattered.

```ruby
daily_wip_by_parent
```

----

## `daily_wip_chart`

The daily WIP charts above are just customized versions of the more generic `daily_wip_chart`. If you want to build your own, you can do that with code like the example below. This example groups the daily WIP by the parent of the ticket in progress.

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

----

## `expedited_chart`

This chart shows how many items are expedited and how long they've been that way. Configure what it means for an item to be expedited through the `expedited_priority_names` key in [project specific settings]({% link config_project.md %}#settings)


```ruby
expedited_chart
```

----

## `sprint_burndown`

Displays all the sprint burndowns that happened during this period. By default, this renders two charts - the top one is burndown by story points and the bottom one is burndown by story count. If you only want one or the other then you can customize that.

```ruby
# Generate both burndowns
sprint_burndown

# Generate only the story point burndown
sprint_burndown :points_only

# Generate only the story count burndown
sprint_burndown :counts_only
```

----

## `estimate_accuracy_chart`

Graphs the estimates (y axis) against the actual cycle time of the item. It's useful to be able to see how much correlation there is between the estimates and the actual time it took. By default, it uses _story points_ for the estimate although that can be configured as seen below.

![Estimate accuracy](https://github.com/mikebowler/jirametrics/assets/1743195/f1550ea3-4746-4727-a593-8c6d85bc4422)

{: .tip }
There is never any correlation between the two, which begs the question _"why we even do story point estimates if they're never accurate?"_

```ruby
estimate_accuracy_chart
```

What if you don't use the _story point_ field and use something custom like TShirt sizes? You can specify that with the yaxis

```ruby
estimate_accuracy_chart do
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

----

## `dependency_chart`

Jira gives you the ability to link issues. So you can say that one issue depends on another or one blocks another. This visualizes all of those relationships.

```ruby
dependency_chart
```

Note that this requires graphviz to be installed. See the [GraphViz](https://graphviz.org/download/) website for installation instructions.

You can customize this chart with two different kinds of rules. One to describe the actual issues themselves and the other to describe the links between issues.

### `issue_rules`

To customize the individual issues.

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

### `link_rules`

To customize the links between issues

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
