---
layout: page
permalink: /forecasting/
title: Probabilistic Forecasting
---
One of the most common reasons we want to look at metrics is so that we can more accurately answer the question _"when will we be done?"_ With those metrics, we can create a probabilistic forecast that answers that question based on historical data.

Although JiraMetrics doesn't create a probablistic forecast for you, it can easily extract the data you need to do a forecast in tools like [Actionable Agile](https://actionableagile.com) (commercial) or the [Focused Objective throughput forecaster](https://www.focusedobjective.com/pages/free-spreadsheets-and-tools) (free).

There are multiple forms of [probabilistic forecasting](https://improvingflow.com/2024/06/02/probabilistic-forecasting.html) although the one you'll likely use most often is a [monte carlo simulation](https://improvingflow.com/2024/06/05/monte-carlo.html). That's also the one used by both the tools mentioned above.

If you haven't used JiraMetrics before then you'll likely want to start with the [QuickStart]({% link quickstart.md %}) and then you can come back here for the configuration to help with probabilistic forecasting.

## Actionable Agile

Actionable Agile needs a specific format for the input file and this configuration will generate that. Note that only the part within `columns` is specific to this output. The rest is only provided for context.


```ruby
Exporter.configure do
  timezone_offset '-05:00'

  target_path 'target/'
  jira_config 'jira_config_improvingflow.json'

  project name: 'myproject' do
    file_prefix 'myproject'

    download do
      rolling_date_count 90
    end

    board id: 1 do
      cycletime do
        start_at first_time_in_status_category('In Progress')
        stop_at still_in_status_category('Done')
      end
    end

    discard_changes_before status_becomes: :backlog # 'To Do'

    file do
      file_suffix '.csv'

      columns do
        write_headers true

        string 'ID', key
        string 'link', url
        string 'title', summary
        column_entry_times
      end
    end
  end
end


# This is typical configuration for the Actionable Agile tool
```

## Focused Objective Spreadsheet

The typical configuration I use to extract data for this spreadsheet looks like this.

```ruby
file do
  file_suffix '.csv'
  # This is a typical configuration for the team dashboard at FocusedObjective.com
  columns do
    write_headers true

    date 'Done', currently_in_status_category('Done')
    date 'Start', first_time_in_status_category('In Progress')
    string 'Type', type
    string 'Key', key
    string 'Summary', summary
  end
end
```
