---
layout: home
---
<div style="text-align: center; padding-bottom: 1em;">
	{% imagesize /assets/images/site_icon.png:img alt="JiraMetrics Logo" %}
</div>

# What is it?

It's a way to pull useful metrics out of Jira.

Jira collects and maintains all kinds of useful data about your workflow. Unfortunately it does a horrible job of exposing that in any meaningful way. This project addresses that gap by giving you a way to pull that data out of Jira and either dumping it into CSV files that you can then manipulate yourself or by creating an HTML report with key findings.

{: .tip }
If you just want to see something working fast, without wading through a lot of documentation on configuration, jump to the [QuickStart](https://github.com/mikebowler/jirametrics/wiki/Quick-Start) and come back to the rest later.

# How do I make it work?

At a high level, there are four steps, and we'll walk through each.

1. Install the tool, on one of these Ruby runtimes. If you're not sure which to use, start with the first option.
  * [Install on CRuby](https://www.ruby-lang.org/en/) (previously called MRI), the standard ruby implementation.
  * [Install on JRuby]({% link jruby.md %}), running on the Java Virtual Machine (JVM). 
2. Configure it to [talk to your Jira instance(s)]({% link connecting_to_jira.md %}).
3. Configure what kind of output you want and options you want to use. This is all in a single configuration file, which is [described here]({% link config_top_level.md %}).
  * HTML report using `standard_project`. Significantly easier to get set up but more limited in customization, as it makes a lot of assumptions about how you want things.
  * HTML report with full configuration. Highly customizable, but that means that you need to spend more time customizing it.
  * CSV files, suitable for import into other tools.
4. Run the tool and look at the output in the target directory.
  * See [command line options]({% link usage.md %})

# Other things you might want to know

* We release new versions fairly frequently. The full [changelog]({% link changes.md %}) can be found here.
* Getting errors or weird behaviour when running the tool? Check out the [Frequently Asked Questions]({% link faq.md %})
* If you find a bug or have an idea for a feature, [report it here](https://github.com/mikebowler/jirametrics/issues)
* If you want to contribute then check out the github projects for [the tool itself](https://github.com/mikebowler/jirametrics) or for [the documentation](https://github.com/mikebowler/jekyll_jirametrics) and submit a PR.

----

You can export CSV files that can be used with various spreadsheets, like the excellent ones from [Focused Objective](https://www.focusedobjective.com/w/support/) or with tools like [Actionable Agile](https://analytics.actionableagile.com).

Alternatively, this tool can directly [generate HTML](https://github.com/mikebowler/jira-export/wiki/HTML-Reports) files with pretty charts in them.


----


* [Changes]({% link changes.md %})
* [Common configuration]({% link config_top_level.md %})
* [Connecting to Jira]({% link connecting_to_jira.md %})
* [Deprecated]({% link deprecated.md %})
* [Examples folder]({% link examples_folder.md %})
* [Exporting raw data]({% link exporting_data.md %})
* [FAQ]({% link faq.md %})
* [HTML Reports]({% link reports.md %})
* [JRuby]({% link jruby.md %})
* [Quick Start]({% link quickstart.md %})
* [Report a bug!](https://github.com/mikebowler/jirametrics/issues)
* How to contribute
* Creating your own variant of `standard_project`
