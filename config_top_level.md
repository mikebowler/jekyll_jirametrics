---
layout: page
permalink: /config/
title: Top level configuration
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

## `target_path`

The `target_path` specifies where all temporary files will be saved. We recommend that it not be the current directory as that tends to get very cluttered, very quickly. 

## `jira_config`

This tells JiraMetrics where to find the file that contains your [Jira configuration]({% link connecting_to_jira.md %}).

## `timezone_offset`

Timezones in the Jira data are frequently all over the place, so in order to make the report look coherent, we have to convert them all to one common timezone.

 Why wouldn't we just default to the timezone of the machine that's running the report? We've found in practice that it's quite common for organizations to be spread across timezones and therefore it's common that the person running the report may be in a different timezone from the person reading it. Forcing it to a known timezone, removes that ambiguity.

{: .tip }
If you don't specify a `timezone_offset` then the report will all be generated in UTC, which is almost certainly not what you want.

