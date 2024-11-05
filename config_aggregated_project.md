---
layout: page
permalink: /config/aggregated_project/
title: standard project
---

## `aggregated_project`

Same idea as [`standard_project`]({% link config_standard_project.md %}) except that instead of looking at a single project, we're aggregating a bunch of them together to look at a higher level view.


```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'

  standard_project name: 'Maple', file_prefix: 'maple', boards: { 1 => :default }
  standard_project name: 'Pine', file_prefix: 'pine', boards: { 2 => :default }

  aggregated_project name: 'forest', project_names: ['Maple', 'Pine']
end
```

This is intended just as a quick way to get started and there is no expectation that this will do everything you want. In fact, we expect that it won't be long before you decide that this isn't good enough and you'll create your own version that does exactly what you need.

Take a look at [the source code](https://github.com/mikebowler/jirametrics/blob/main/lib/jirametrics/examples/aggregated_project.rb) for `aggregated_project` to see how you could make your own.

| Parameter | Description |
|:--------|:-------|
| `name` | The name that you're giving to this project. It will be used in the report but does not have to match any fields inside Jira itself. |
| `project_names` | This is list of project names that will be included in the aggregated report. All of these names must have already been declared using either `project` or `standard_project` |
