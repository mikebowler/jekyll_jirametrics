---
layout: page
permalink: /troubleshooting/
title: Errors and how to fix them
---

This page maps the errors you're most likely to hit to their cause and the fix, roughly in the order they show up during setup. If you just want a working setup from scratch, start with the [Quick Start]({% link quickstart.md %}) and come back here when something breaks.

Two commands are useful diagnostics throughout (both added in v3.1):

- `jirametrics verify` checks that your credentials authenticate, without downloading any data.
- `jirametrics boards` lists your boards; `jirametrics boards <id>` shows a board's columns and statuses.

## Installation

### `command not found: jirametrics`

**Cause:** the gem installed into a Ruby that isn't first on your `PATH`. This is common when Ruby was installed with Homebrew or a version manager.

**Fix:** see the `PATH` note in the [installation instructions]({% link install.md %}), then confirm with `jirametrics --version`.

## Connecting to Jira

### `The request was not authorized. Verify that your authentication token hasn't expired`

**Cause:** HTTP 401. The API token (Cloud) or personal access token (Server/Data Center) is wrong, expired, or was deleted on the server.

**Fix:** recreate the token, update the file referenced by `jira_config`, and run `jirametrics verify` to confirm. See [Connecting to Jira]({% link connecting_to_jira.md %}).

### `Jira returned 503 (Service Unavailable) ...`

**Cause:** a Jira outage, or (for a free Cloud instance) the instance was deactivated after a period of inactivity.

**Fix:** check your Jira status/subscription and retry. If it's a free instance that went dormant, reactivate it.

### An error about being rate limited

**Cause:** either you really have hit the instance too often, or (misleadingly) your token was deleted on the server and Jira returned a rate-limit message instead of a clear auth error.

**Fix:** wait and retry; if it persists, recreate your token and run `jirametrics verify`. More detail in the [FAQ]({% link faq.md %}#rate-limited).

### `Must specify URL in config`

**Cause:** the Jira config JSON is missing a `url` (or it's malformed).

**Fix:** make sure the file referenced by `jira_config` has a valid `url`. See [Connecting to Jira]({% link connecting_to_jira.md %}).

## Configuration

### `undefined method 'standard_project'`

**Cause:** your `config.rb` uses `standard_project` but never required it.

**Fix:** add `require 'jirametrics/examples/standard_project'` as the first line of `config.rb`. See [standard_project]({% link config_standard_project.md %}).

### `Cannot find configuration file "config.rb"`

**Cause:** you're running from a directory that has no `config.rb`, or the `--config` path is wrong.

**Fix:** run from the directory that holds `config.rb`, or pass `--config <path>`.

### `Warning: The history for issue X references a status ("Name":id) that can't be found ...`

**Cause:** a status that used to exist has been deleted in Jira, so its category (To Do / In Progress / Done) can't be looked up.

**Fix:** declare the missing status's category. With `standard_project`, use the `status_category_mappings` parameter (note the trailing `s`); with the full DSL, use `status_category_mapping`. Full explanation in [FAQ #1]({% link faq.md %}#q1).

### `discard_changes_before: Status "X" not found`

**Cause:** the status you named for [`discard_changes_before`]({% link config_project.md %}#discard_changes_before) doesn't exist on the board.

**Fix:** check the spelling. `jirametrics boards <id>` lists the real status names, shown as `"name":id`.

## Downloading and exporting

### `No data found. Must do a download before an export`

**Cause:** you ran `jirametrics export` before ever downloading.

**Fix:** run `jirametrics download` first (or `jirametrics go`, which downloads and then exports in one step).

## Interpreting the report

### Cycle times look wrong, or items are missing from a chart

**Cause:** the cycle time start/stop points don't match how your board actually works, most often because the config uses `boards: { N => :default }`, which only fits about half of boards.

**Fix:** run `jirametrics boards <id>` to see your columns, then set explicit start and stop points with `first_time_in_or_right_of_column`. See [cycletime]({% link config_cycletime.md %}). The [data quality]({% link quality_report.md %}) section at the top of every report also flags many of these cases.

### Parent issues aren't linked correctly

**Cause:** Jira stores the parent in an instance-specific custom field that JiraMetrics can't guess.

**Fix:** set `customfield_parent_links`. See the [FAQ]({% link faq.md %}#parent_key).
