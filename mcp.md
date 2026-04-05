---
layout: page
permalink: /mcp/
title: MCP Server (AI Integration)
---

{: .important }
MCP support is new and the available tools will grow over time.

# What is MCP?

[MCP (Model Context Protocol)](https://modelcontextprotocol.io) is a standard that allows AI assistants such as Claude to directly query data from tools like JiraMetrics. Instead of copying and pasting data into a conversation, the AI can query your Jira data directly and reason about it in context.

For example, once configured, you can ask Claude things like:
- "Show me all aging work older than 60 days in the Mobile team project"
- "What's been sitting in Review the longest?"
- "How many items were cancelled last month?"
- "What backlog items have ever been flagged as blocked?"

# Setup

## Claude Code (recommended)

Claude Code supports per-project MCP configuration via a `.mcp.json` file in your project directory. This is the recommended approach because each directory can point to a different Jira instance, keeping client data fully isolated.

Create a `.mcp.json` file in the same directory as your `config.rb`:

```json
{
  "mcpServers": {
    "jirametrics": {
      "type": "stdio",
      "command": "/bin/bash",
      "args": ["-c", "source ~/.rvm/scripts/rvm && jirametrics mcp"]
    }
  }
}
```

{: .tip }
If you use rbenv or asdf instead of RVM, replace `source ~/.rvm/scripts/rvm &&` with the equivalent for your version manager, or omit it if `jirametrics` is already on your PATH.

When you start Claude Code from that directory, the MCP server starts automatically and the `jirametrics` tools become available in your conversation.

### Data isolation between instances

Because `.mcp.json` is per-directory, each directory has its own configuration pointing to its own `config.rb` and its own downloaded data. Starting Claude Code from directory A gives you only that instance's data; starting from directory B gives you only that instance's data. There is no cross-contamination.

{: .tip }
Add `.mcp.json` to your `.gitignore` if the file contains paths specific to your machine.

## AI assistants that require a single-word command

Some AI assistants (such as [apfel](https://github.com/Arthur-Ficial/apfel)) invoke MCP servers by executing a single command with no arguments. They cannot call `jirametrics mcp` because the space makes it look like a path to a binary named `jirametrics mcp` rather than a command with a subcommand.

For these cases, JiraMetrics ships a second executable, `jirametrics-mcp`, which is equivalent to running `jirametrics mcp`. Pass any options you would normally pass to `jirametrics mcp` directly to `jirametrics-mcp`:

```
jirametrics-mcp --config /full/path/to/your/config.rb
```

With apfel, for example:

```
apfel --mcp $(which jirametrics-mcp) "what boards do we have?"
```

{: .tip }
Apfel resolves the command as a file path rather than searching your PATH, so you need to pass the full path. `$(which jirametrics-mcp)` expands to the correct path at the time you run the command.

## Claude Desktop

Claude Desktop loads all configured MCP servers at startup and runs them for the entire session. This works well if you only have one Jira instance to work with.

Add a `jirametrics` entry to your `claude_desktop_config.json` (on macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "jirametrics": {
      "command": "/bin/bash",
      "args": [
        "-c",
        "source ~/.rvm/scripts/rvm && jirametrics mcp --config /full/path/to/your/config.rb"
      ]
    }
  }
}
```

Restart Claude Desktop after making changes to this file.

{: .important }
If you have multiple JiraMetrics instances with confidentiality requirements between them, Claude Desktop is not recommended as all configured servers run simultaneously in the same session.

# Available tools

## list_projects

Returns all available projects with their issue count and data end date, plus any aggregate groups defined in your config. Claude will call this automatically when a question is ambiguous about which project it applies to, allowing it to ask you to clarify before running a broader query.

This tool takes no parameters.

Aggregate groups appear at the bottom of the output and can be used as the `project` parameter in any tool — they expand automatically to cover all constituent projects. For example, if your config defines a `forest` aggregate over Pine, Maple, Willow, Birch, and Cedar, you can ask "what's aging in forest?" and it will query all five.

## aging_work

Returns all issues that have been started but not yet completed (work in progress), sorted from oldest to newest. Age is the number of days since the issue was started, calculated relative to the end of the downloaded data range — consistent with how the aging charts in the HTML report calculate age. Flow efficiency (FE) is also returned for each issue: the percentage of elapsed time the issue was actively being worked on.

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `min_age_days` | integer | Only return issues at least this many days old. Omit to return all. |
| `project` | string | Only return issues from this project name. Omit to return all projects. |
| `current_status` | string | Only return issues currently in this status (e.g. `"Review"`, `"In Progress"`). |
| `current_column` | string | Only return issues whose current status maps to this board column (e.g. `"In Progress"`). |
| `history_field` | string | Only return issues where this field ever had the value specified by `history_value` (e.g. `"priority"`). Must be used together with `history_value`. |
| `history_value` | string | The value to look for in the change history of `history_field` (e.g. `"Highest"`). Must be used together with `history_field`. |
| `ever_blocked` | boolean | Only return issues that were ever blocked. Blocked includes flagged items, issues in blocked statuses, and blocking issue links. |
| `ever_stalled` | boolean | Only return issues that were ever stalled. Stalled means the issue sat inactive for longer than the stalled threshold, or entered a stalled status. |
| `currently_blocked` | boolean | Only return issues that are blocked right now (as of the data end date). |
| `currently_stalled` | boolean | Only return issues that are stalled right now (as of the data end date). |

### Examples

Ask Claude naturally — it will choose the right parameters:

- "Show me all aging work" — returns everything
- "Show aging work older than 90 days" — uses `min_age_days: 90`
- "What's aging in the Mobile project?" — uses `project: "Mobile"`
- "Show aging work older than 60 days in the Mobile project" — uses both filters
- "Show aging work that was ever priority Highest" — uses `history_field: "priority"`, `history_value: "Highest"`
- "What aging work has been blocked?" — uses `ever_blocked: true`
- "What aging work is currently blocked?" — uses `currently_blocked: true`
- "Show work in progress that has stalled" — uses `ever_stalled: true`
- "What's stalled right now?" — uses `currently_stalled: true`
- "Show me all aging work currently in Review" — uses `current_status: "Review"`
- "Show me aging work in the In Progress column" — uses `current_column: "In Progress"`

## completed_work

Returns issues that have been completed, sorted most recently completed first. Includes cycle time (days from start to completion), flow efficiency (FE — the percentage of cycle time spent actively working on the issue), and the status and resolution at the time of completion.

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `days_back` | integer | Only return issues completed within this many days of the data end date. Omit to return all. |
| `project` | string | Only return issues from this project name. Omit to return all projects. |
| `completed_status` | string | Only return issues whose status at completion matches this value (e.g. `"Done"`, `"Cancelled"`). |
| `completed_resolution` | string | Only return issues whose resolution at completion matches this value (e.g. `"Won't Do"`). |
| `history_field` | string | Only return issues where this field ever had the value specified by `history_value`. Must be used together with `history_value`. |
| `history_value` | string | The value to look for in the change history of `history_field`. Must be used together with `history_field`. |
| `ever_blocked` | boolean | Only return issues that were ever blocked. |
| `ever_stalled` | boolean | Only return issues that were ever stalled. |
| `currently_blocked` | boolean | Only return issues that were blocked at the time of completion. |
| `currently_stalled` | boolean | Only return issues that were stalled at the time of completion. |

### Examples

- "What work was completed in the last 30 days?" — uses `days_back: 30`
- "How many items were cancelled?" — uses `completed_status: "Cancelled"`
- "Show completed work that was never started (no cycle time)" — cycle time shown as "unknown"
- "What completed work was ever flagged as highest priority?" — uses `history_field: "priority"`, `history_value: "Highest"`
- "Show completed items that were blocked at some point" — uses `ever_blocked: true`

## not_yet_started

Returns issues that have not yet been started (backlog items), sorted by creation date oldest first.

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `project` | string | Only return issues from this project name. Omit to return all projects. |
| `current_status` | string | Only return issues currently in this status (e.g. `"To Do"`, `"Backlog"`). |
| `current_column` | string | Only return issues whose current status maps to this board column (e.g. `"Ready"`). |
| `history_field` | string | Only return issues where this field ever had the value specified by `history_value`. Must be used together with `history_value`. |
| `history_value` | string | The value to look for in the change history of `history_field`. Must be used together with `history_field`. |
| `ever_blocked` | boolean | Only return issues that were ever blocked. |
| `ever_stalled` | boolean | Only return issues that were ever stalled. |
| `currently_blocked` | boolean | Only return issues that are blocked right now (as of the data end date). |
| `currently_stalled` | boolean | Only return issues that are stalled right now (as of the data end date). |

### Examples

- "What's in the backlog?" — returns all unstarted issues
- "What's been sitting in the backlog the longest?" — oldest items appear first
- "Show unstarted work in the Mobile project" — uses `project: "Mobile"`
- "What backlog items have ever been flagged?" — uses `history_field: "Flagged"`, `history_value: "Impediment"`
- "Show backlog items in the To Do status" — uses `current_status: "To Do"`

## status_time_analysis

Aggregates the time issues spend in each status or column, ranked by average days per issue. Useful for identifying bottlenecks and understanding where flow slows down. Time in status is counted from issue creation through to completion (or data end date for in-progress issues).

Because the answer differs significantly depending on which issues are included, Claude will ask you to clarify the `issue_state` before running this tool if it isn't clear from context.

| Parameter | Type | Description |
|:----------|:-----|:------------|
| `project` | string | Only include issues from this project. Omit to include all projects. |
| `issue_state` | string | Which issues to include: `"aging"` (in progress), `"completed"`, `"not_started"` (backlog), or `"all"` (default). |
| `group_by` | string | Group results by `"status"` (default) or `"column"`. Column-level grouping aggregates multiple statuses that map to the same board column. |

### Examples

- "In what status do issues spend the most time?" — no parameters needed
- "In what column do issues spend the most time?" — uses `group_by: "column"`
- "Where is work getting stuck for completed issues?" — uses `issue_state: "completed"`
- "What's the bottleneck in the Mobile project?" — uses `project: "Mobile"`
- "Where does aging work tend to pile up?" — uses `issue_state: "aging"`
- "Which column is the biggest bottleneck?" — uses `group_by: "column"`, `issue_state: "completed"`
