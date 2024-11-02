---
layout: page
permalink: /config/
---
Create a configuration file called `config.rb` in the same directory where you plan to run JiraMetrics.

{: .tip }
If you want to have multiple configuration files for different setups then you can override the default name in the [command line parameters]({% link usage.md %}).

The contents of this file will contain some generic set up information and then specific details about each project we're looking at. This sample will give you some idea of what it might look like. Each element within that, is explained individually below.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'
  timezone_offset '-08:00'

  project do
    file_prefix 'sample'

    download do
      rolling_date_count 90
    end

    board id: 1 do
      cycletime do
        start_at first_time_in_status_category('In Progress')
        stop_at still_in_status_category('Done')
      end
    end

    file do
      file_suffix '.csv'

      columns do
        write_headers true

        date 'Done', currently_in_status_category('Done')
        date 'Start', first_time_in_status_category('In Progress')
        string 'Type', type
        string 'Key', key
        string 'Summary', summary
      end
    end
end
```

### target_path

The `target_path` specifies where all temporary files will be saved. We recommend that it not be the current directory as that tends to get very cluttered, very quickly. 

### jira_config

This tells JiraMetrics where to find the file that contains your [Jira configuration]({% link connecting_to_jira.md %}).

### timezone_offset

{: .tip }
If you don't specify a `timezone_offset` then the report will all be generated in UTC, which is almost certainly not what you want. Why wouldn't we just default to the timezone of the machine that's running the report? We've found in practice that it's quite common for organizations to be spread across timezones and therefore it's common that the person running the report may be in a different timezone from the person reading it. Forcing it to a known timezone, removes that ambiguity.


### Projects

The `project` declaration tells us details about this project and what output we want to create. See the [project configuration]({% link config_project.md %}) documentation for details on that.

Putting an `x` in front of project will cause that project to be ignored.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'
  timezone_offset '-08:00'

  project do
    # ... project 1 ...
  end

  project do
    # ... project 2 ...
  end

  xproject do
    # ... ignored project ...
  end
end
```

What if you need to have different target paths or Jira config for different projects? Each project will find the preceding settings and use those so you can redefine them at any time and the subsequent projects will use the new settings.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'

  project do
    # ... project 1 ...
  end

  target_path 'target2'
  jira_config 'other.json'

  project do
    # ... project 2 using different target and jira config ...
  end
end
```

If you choose to use `standard_project` then it can be inserted at the same place as the project declaration and you can even mix the two styles together if you want to use them both. Under the covers, `standard_project` just creates a full `project` declaration with a predefined configuration.

```ruby
Exporter.configure do
  target_path 'target'
  jira_config 'improvingflow.json'

  standard_project name: 'Maple', file_prefix: 'maple', boards: { 1 => :default }
end
```

More details on configuring...
* The [project]({% link config_project.md %}) element
* The [standard_project]({% link config_project.md %}) element
