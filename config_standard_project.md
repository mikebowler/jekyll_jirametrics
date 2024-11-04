---
layout: page
permalink: /config/standard_project/
title: standard project
---

## `standard_project`

If you choose to use `standard_project` then it can be inserted at the same place as the project declaration and you can even mix the two styles together if you want to use them both. Under the covers, `standard_project` just creates a full `project` declaration with a predefined configuration.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'

  standard_project name: 'Maple', file_prefix: 'maple', boards: { 1 => :default }
end
```
