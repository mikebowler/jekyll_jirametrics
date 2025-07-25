---
layout: home
---
<div style="text-align: center; padding-bottom: 1em;">
	{% imagesize /assets/images/site_icon.png:img alt="JiraMetrics Logo" %}
</div>

{: .important }
Jira has recently made some breaking changes and has planned more of them. If you're seeing strange errors from JiraMetrics then make sure you're on the most recent version before you spend too much time debugging.

# What is it?

In a nutshell, it's a way to pull useful metrics out of Jira&trade;[^jira].

Jira collects and maintains all kinds of useful data about your workflow. Unfortunately it does a horrible job of exposing that in any meaningful way. This project addresses that gap by giving you a way to pull that data out of Jira and either dumping it into CSV files that you can then manipulate yourself or by creating an HTML report with key findings.

{: .tip }
If you just want to see something working fast, without wading through a lot of documentation on configuration, jump to the [QuickStart]({% link quickstart.md %}) and come back to the rest later.

JiraMetrics is an open source project, released under an [Apache License](https://github.com/mikebowler/jirametrics/blob/main/LICENSE).

JiraMetrics is a standalone tool that can connect to any Jira instance that you have. It is not a plugin that needs to be configured by an administrator. If you can access Jira through a web browser, then you can configure this tool to also get reports from that instance.

<div style="display: flex; border: 1px dashed green; background: #EAFFF1; padding: 0.2em 0.5em; gap: 0.5em; margin-bottom: 0.5em;">
  <div>
    <b>Promotion: </b><a href="https://www.mikebowler.ca">Mike</a> (primary author of JiraMetrics) is starting a newsletter and the first hundred people who sign up, will get free access to his video course on flow metrics. While the course isn't dependent on JiraMetrics, it is directly relevant to anyone using it.<br /><a href="https://gargoylesoftware.com/newsletter">Check out the newsletter here</a>.
  </div>
  <div>
    <img src="/assets/images/flow_metrics_basics_cover.png" />
  </div>
</div>


# How do I make it work?

At a high level, there are four steps, and we'll walk through each.

1. [Install JiraMetrics]({% link install.md %})
2. Configure it to [talk to your Jira instance(s)]({% link connecting_to_jira.md %}).
3. Configure what kind of output you want and options you want to use. This is all in a single configuration file, which is [described here]({% link config_top_level.md %}).
  * HTML report using `standard_project`. Significantly easier to get set up but more limited in customization, as it makes a lot of assumptions about how you want things.
  * HTML report with full configuration. Highly customizable, but that means that you need to spend more time customizing it.
  * CSV files, suitable for import into other tools.
4. Run the tool and look at the output in the target directory.
  * See [command line options]({% link usage.md %})

# Other things you might want to know

* At the top of every report is a data quality section. Here's [more information on that]({% link quality_report.md %}).
* We release new versions fairly frequently. The full [changelog]({% link changes.md %}) can be found here and you can upgrade to the latest with `gem update jirametrics`
* Getting errors or weird behaviour when running the tool? Check out the [Frequently Asked Questions]({% link faq.md %})
* If you find a bug or have an idea for a feature, [report it here](https://github.com/mikebowler/jirametrics/issues)
* If you want to contribute then [check out the contribution page]({% link contributing.md %})
* Our security folks have concerns about people pulling potentially confidential information. [What can I tell them?]({% link security.md %})
* How do I get started with [probabilistic forecasting]({% link forecasting.md %})?

# Presentation: Getting Great Metrics out of Jira

This talk walks through the kinds of metrics you can pull with JiraMetrics and walks through many of the charts. The talk was given in November 2024 to the KanbanTO group, and shows off JiraMetrics v2.7.3

{% include responsive-embed url="https://www.youtube.com/embed/hzXeJr6GG3w" ratio="16:9" %}

----

# Configuration Index

There are a lot of configuration items that can be set. Here's an index to get you to the ones you want, quickly.

* `Exporter.configure`
  * [`target_path`]({% link config_top_level.md %}#target_path)
  * [`jira_config`]({% link config_top_level.md %}#jira_config)
  * [`timezone_offset`]({% link config_top_level.md %}#timezone_offset)
  * [`holiday_dates`]({% link config_top_level.md %}#holiday_dates)

  * [`project`]({% link config_project.md %})
    * [`file_prefix`]({% link config_project.md %}#file_prefix)
    * [`download`]({% link config_project.md %}#download)
    * [`board`]({% link config_project.md %}#board)
      * [`cycletime`]({% link config_project.md %}#board)
    * [`file`]({% link config_file_csv.md %}) (when exporting data in CSV)
      * [`file_suffix`]({% link config_file_csv.md %}#file_suffix)
      * [`only_use_row_if`]({% link config_file_csv.md %}#only_use_row_if)
      * [`columns`]({% link config_file_csv.md %}#file_suffix)
        * [`write_headers`]({% link config_file_csv.md %}#write_headers)
        * [`column_entry_times`]({% link config_file_csv.md %}#column_entry_times)
        * [Type specific values]({% link config_file_csv.md %}#type_specific_values)
        * [Accessing custom fields]({% link config_file_csv.md %}#custom_fields)
    * [`file`]({% link config_file_html.md %}) (when creating an HTML report)
      * [`file_suffix`]({% link config_file_html.md %}#file_suffix)
      * [`discard_changes_before`]({% link config_file_html.md %}#discard_changes_before)
      * [`html_report`]({% link config_file_html.md %}#html_report)
        * [`board_id`]({% link config_file_html.md %}#board_id)
        * [All Charts]({% link config_charts.md %})
          * [`cycletime_scatterplot`]({% link config_charts.md %}#cycletime_scatterplot)
          * [`cycletime_histogram`]({% link config_charts.md %}#cycletime_histogram)
          * [`throughput_chart`]({% link config_charts.md %}#throughput_chart)
          * [`aging_work_in_progress_chart`]({% link config_charts.md %}#aging_work_in_progress_chart)
          * [`daily_wip_by_age_chart`]({% link config_charts.md %}#daily_wip_by_age_chart)
          * [`daily_wip_by_blocked_stalled_chart`]({% link config_charts.md %}#daily_wip_by_blocked_stalled_chart)
          * [`daily_wip_by_parent_chart`]({% link config_charts.md %}#daily_wip_by_parent_chart)
          * [`daily_wip_chart`]({% link config_charts.md %}#daily_wip_chart)
          * [`daily_view`]({% link config_charts.md %}#daily_view)
          * [`expedited_chart`]({% link config_charts.md %}#expedited_chart)
          * [`sprint_burndown`]({% link config_charts.md %}#sprint_burndown)
          * [`estimate_accuracy_chart`]({% link config_charts.md %}#estimate_accuracy_chart)
          * [`dependency_chart`]({% link config_charts.md %}#dependency_chart)
            * [`issue_rules`]({% link config_charts.md %}#issue_rules)
            * [`link_rules`]({% link config_charts.md %}#link_rules)
    * [`status_category_mapping`]({% link config_project.md %}#status_category_mapping)
    * [`anonymize`]({% link config_project.md %}#anonymize)
    * [`standard_project`]({% link config_standard_project.md %})
    * [`aggregated_project`]({% link config_aggregated_project.md %})
    * [Project specific settings]({% link config_project.md %}#settings)

[^jira]: Jira is a registered trademark of Atlassian Corporation Plc