---
layout: page
permalink: /quickstart/
title: Quick Start
---
There are so many possible configuration options for this tool, that it can be a little overwhelming to get your first report working. This page will show one quick end-to-end from not having anything installed to a full report, in just a few minutes.

If you haven't installed JiraMetrics yet, [follow these instructions]({% link install.md %}).

Create a directory to hold all the data you're going to download and the reports you'll generate, and navigate into it.

```
mkdir myreports
cd myreports
```

The two configuration files you're going to create will all be in this directory, and you'll be running the tool from here as well.

Create a file called `jira_config.json` and populate it [as described here]({% link connecting_to_jira.md %}).

In my case, I'm on an instance of Jira Cloud and so mine looks like this. You'll need to change it to reflect your own settings.

```json
{
  "url": "https://improvingflow.atlassian.net",
  "email": "mbowler@gargoylesoftware.com",
  "api_token": "abcdefghijklmnopqrstuvwxyz"
}
```

Create another file called `config.rb` and fill it with this.

```ruby
require 'jirametrics/examples/standard_project'

Exporter.configure do
  timezone_offset '-08:00'

  target_path 'target/'
  jira_config 'jira_config.json'

  standard_project name: 'Sample', file_prefix: 'sample', boards: { 2 => :default }
end
```

On the second last line, the 2 is the board id and you need to replace it with the id of the board you want to pull data from. Navigate to that board in your jira instance now and look at the URL that you're currently on.

You may see a URL that looks something like this and the board id is the number at the end. In this case, 44.
```
https://improvingflow.atlassian.net/jira/software/c/projects/SP/boards/44
```

Or you might see a URL that looks something like this and the board id is the number in the rapidView parameter. In this case, 17.
```
https://example.com/secure/RapidBoard.jspa?projectKey=ABC&rapidView=17&view=planning.nodetail
```

Once you've found that board id, put it in the `config.rb` now.

On the `sample_project line`, you'll likely also want to change the `name` and `file_prefix` to something that is more descriptive for the board you're about to pull. Or you can leave it alone for now, until you've seen it run once.

**Optional:** If you want to use the dependency chart, which is included in `standard_project` by default then you'll want to install [graphviz](https://graphviz.org/download/). In my case, that was as simple was `brew install graphviz`. Note that nothing will blow up if you don't install this - you just won't get the dependency chart in the output.

Now we're ready to run the tool. From the command line, run:

```
jirametrics go
```

{: .tip }
The most common error at this point is one about a missing status. If you get that, see [FAQ #1]({% link faq.md %}#q1).

Assuming everything ran successfully, you should see output that looks something like this. You may have more lines depending on how much data is being pulled. The board I'm using for testing has very little data in it.

```
Sample

Sample
  Downloading all statuses
  Downloading board configuration for board 2
  Downloading sprints for board 2
  Downloading primary issues for board 2
    Downloaded 1-2 of 2 issues to target/sample_issues/ 
  Downloading linked issues for board 2
Full output from downloader in jirametrics.log
Sample
```

Now if you look at the `target` directory (that name was specified in the config above and can be changed), you'll see a bunch of files that are all data downloaded from Jira, plus one with an `.html` extension. Open that html in a web browser and be amazed ;-)

For a single board, that's it. It will have pulled the most recent 90 days worth of data and you can get the latest at any time by re-running `jirametrics go`.

---

When you want to add a second board, it goes in the same `config.rb file` so now it might look like this:

```ruby
require 'jirametrics/examples/standard_project'

Exporter.configure do
  timezone_offset '-08:00'

  target_path 'target/'
  jira_config 'jira_config.json'

  standard_project name: 'Sample', file_prefix: 'sample', boards: { 2 => :default }
  standard_project name: 'Board2', file_prefix: 'board2', boards: { 3 => :default }
end
```

---

Note:  `standard_project` is the fastest way to get something up and running but it makes a lot of assumptions about how your boards work. You'll likely want to create your own variation of that, that works better for you. Refer to [`standard_project`]({% link config_standard_project.md %}) to see how you could customize your own. 

The jirametrics tool is a lot more customizable than is shown in `standard_project`.

If you want a quick Look at the kinds of charts you can create, look at the documentation for the [charts]({% link config_charts.md %}).