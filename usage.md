---
layout: page
permalink: /usage/
title: Command line usage
---

Invoke the tool with `jirametrics [command] [options]`

## Commands

The most common command you'll run is `jirametrics go`. This will download all the new issues from Jira and will then generate a report or a CSV, depending on how it's configured.

If you're making changes to your configuration then you might want to skip the download step each time as that's the most time-consuming part. In that case, you can call `jirametrics download` just to download issues and `jirametrics export` to generate the output, as two separate steps.

| Command | Description |
|:--------|:-------|
| go | Same as calling download, followed by export. It's just a shorter way to do it all at once. |
| download | Download all the data from Jira |
| export | Generate the respective output files. These could be CSV's or HTML reports, as specified in the configuration |
| info | Dump information about a specific issue to the terminal. This is really intended as a debugging tool for the developers but we're making it accessible for everyone. Usage: `jirametrics info ABC-123`. Requires v2.7 |


## Options

| Option | Description |
|:--------|:-------|
| config | By default, the tool assumes that you've put your configuration in a file called config.rb. If you want to use different names then you can specify that with the `--config <filename>` option. This is useful if you want to have multiple configuration files and only call one or another. |
| name | If you have a large configuration file and only want to run a subset of the projects within it, you can use the `--name <name>` option. Example: `jirametrics go --name "a*"` will run all the projects starting with the letter 'a'. The name it compares against is the one specified on the project section. |
