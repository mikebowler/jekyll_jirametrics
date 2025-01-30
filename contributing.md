---
layout: page
permalink: /contributing/
title: Contributing
---

JiraMetrics is hosted on Github, as is the source for this documentation site. We accept PR's for both.

* [JiraMetrics](https://github.com/mikebowler/jirametrics)
* [Documentation](https://github.com/mikebowler/jekyll_jirametrics)

# JiraMetrics

Once you have downloaded the [source from github](https://github.com/mikebowler/jirametrics), you'll want to go to the root of that folder and run `bundle install` from the terminal to install all dependencies.

Most of the individual commands that you would call on JiraMetrics itself, can be run through `rake` so that you don't have to package and install the gem just to test some new code. The supported commands are `go`, `download`, and `export`

For example, when all the source is downloaded, `rake export` will do the same as `jirametrics export`. You will get a warning about how you should have called it through the gem but you can ignore that if you're doing development.

To run the tests, you can invoke `rake spec` or `rake test`. Why two different commands? Because sometimes my fingers want to type _spec_ and sometimes they want to type _test_ and it was easier to alias the command than to retrain my fingers.

Also the command `rake focus` will run only the tests that have the `:focus` tag on them. It's often convenient to only run one test at a time, if we're trying to debug something.

We do expect that all new code will have tests, unless there's a really good reason to skip them. Yes, we're aware that the existing codebase doesn't have 100% coverage. We're making a point of continually inproving that coverage number though.

We use [RuboCop](https://rubocop.org) as our linter and there are rubocop rules in the project. Please ensure that your changes run with no rubocop warnings as that just makes it easier for the committers.

Run it from the terminal with `rubocop` or integrate it into your source editor.

# JiraMetrics.org documentation website

Once you have downloaded the [source from github](https://github.com/mikebowler/jekyll_jirametrics), you'll want to go to the root of that folder and run `bundle install` from the terminal to install all dependencies.

This is a static website built with [Jekyll](https://jekyllrb.com) so familiarizing yourself with that tool will be helpful.

It unfortunately uses a custom theme that you'll also need to [download from github](https://github.com/mikebowler/so-simple-theme). This a forked copy of a formally published theme and the plan was always to push changes back upstream but that project no longer seems to accept PR's so we're stuck with our own custom version.

The command `rake server` will spin up a server on port 4000 that you can access with http://localhost:4000
