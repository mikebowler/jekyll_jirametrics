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

As of v3.0, `discard_changes_before` is configured at the **project** level, not inside the `file` or
`html_report` block. See [`discard_changes_before`]({% link config_project.md %}#discard_changes_before).

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

### The dependency chart

The dependency chart used to be the exception here, because the tool that draws it, [Graphviz](https://graphviz.org), knows nothing about CSS. Its colours are now variables like everything else.

Each type has a colour for the box and a colour for the text written inside it. They come in pairs, because most of the boxes are dark with white text and some are light with black.

```css
:root {
  --dependency-chart-story-color: #015C41;
  --dependency-chart-story-label-color: white;
  --dependency-chart-task-color: #56B4E9;
  --dependency-chart-task-label-color: black;
  --dependency-chart-bug-color: #783200;    /* also used for Defect */
  --dependency-chart-bug-label-color: white;
  --dependency-chart-epic-color: #F0E442;
  --dependency-chart-epic-label-color: black;
  --dependency-chart-spike-color: #762A58;
  --dependency-chart-spike-label-color: white;

  --dependency-chart-label-color: black;    /* text on a box you coloured yourself */
  --dependency-chart-link-color: gray;      /* the lines between boxes, and their labels */
}
```

An issue type not in that list reuses one of those same five pairs rather than coming from [the fallback palette](#the-fallback-palette), so two uncommon types can end up sharing a colour. Every node names its own type, so you can still tell them apart.

The defaults are chosen to work well for people who are colour blind, so please keep that in mind if you replace them.

**If you change a box colour, change its label colour to match.** That is the one thing worth being careful about here: these colours sit behind text rather than beside it, so a dark box needs white text and a light box needs black. Nothing checks this for you, and getting it wrong makes the node unreadable. For the same reason they need no separate dark mode value, since the box is its own background. `--dependency-chart-link-color` does have one, because the lines sit on the page rather than inside a box.

### The fallback palette

Some things on a chart need to be told apart without any particular colour being called for, such as one series per epic when you have not said which colour each epic should be. Those come from a fallback palette defined as `--palette-color-1` upwards. It is the [Okabe-Ito palette](https://jfly.uni-koeln.de/color/), chosen because its colours stay distinguishable to people with colour vision deficiency, so please keep that in mind if you replace them.

You can override any slot the same way you override any other colour. You can also extend the palette simply by defining the next number, and it will be used; the number of slots is read from the CSS rather than fixed in the code.

```css
:root {
  --palette-color-3: #117733;   /* replace a slot */
  --palette-color-8: #882255;   /* add a new one */
}
```

If you need a *specific* colour for a *specific* thing, configure it on the chart rather than relying on which palette slot that thing happens to be given. The slot a series gets depends on how many other series were drawn before it.

### Reverting to the legacy colour scheme

The default colours were updated to improve accessibility for people with colour vision deficiencies (colour blindness). If you prefer the original colour scheme, you can opt out by using the [legacy_colors.css](https://github.com/mikebowler/jirametrics/blob/main/lib/jirametrics/html/legacy_colors.css) file that ships with jirametrics.

Save a copy of that file alongside your config file, then point `include_css` at it:

```ruby
project name: 'foo' do
  setting['include_css'] = './legacy_colors.css'
end
```

The file covers both light mode and dark mode colours.