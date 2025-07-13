---
layout: page
permalink: /changes/
title: Change log
---
Changes that affect behaviour or expected functionality will be listed here. This does not list all commits - refer to git log for that.

# v2.12 (July 13, 2025)

* New chart: [Daily View]({% link config_charts.md %}#daily_view) that is an optimized view for use during the daily standup/sprint/meeting.
* Estimation changes
  * The [`sprint_burndown`]({% link config_charts.md %}#sprint_burndown) and [`estimate_accuracy_chart`]({% link config_charts.md %}#estimate_accuracy_chart) both pull the estimate from the issue history. In the past we've looked for the field "Story Points" which works just fine in an English instance of Jira but fails for many (all?) non-English instances.
  * Also changed them to support time based estimates, if the board is configured to use them.
* Aging Work Table now shows the issue priority.
* Bug: Fixed exception when downloading for the first time and status_category_mappings are already set.
* New criteria that can be used with `start_at` or `stop_at`: `first_time_visible_on_board`
* Whenever we display statuses now, we display them in the format `"Review":10011` rather than just `Review`. We do this to remove any ambiguity around which specific `Review` status we're talking about as there may be several.

# v2.11 (March 11, 2025)

* [Aging work in progress chart]({% link config_charts.md %}#aging_work_in_progress_chart) - improvements
  * We've always shown the 85% point in each column so we can immediately see if an item is an outlier. We now show the 50%, 85%, 98%, and 100% points as well.
  * The CSS variable `--aging-work-in-progress-chart-shading-color` has been removed and replaced with four new ones for the percentile colours. This will only matter to you if you're [customizing the CSS]({% link config_file_html.md%}#css).
  * We no longer show all the columns, only those that are considered "in progress", to reduce clutter.
* [Aging work table]({% link config_charts.md %}#aging_work_table) - improvements
  * In addition to the current age, we display how much longer it's likely to take to finish this. This is a forecast, based on historical data for this board.
  * If an item has a due date then we display that. We also show if we're likely to complete by that date.
* If Javascript is disabled in the browser then almost none of the charts will be visible and the report will be largely useless. We now display a warning the the top of the page if this is the case. This has been reported as a frequent problem when reports are stored on Sharepoint as it will disable javascript by default and then people are left wondering why the report isn't helpful.
* Bug: Fixed obscure exception when one issue had been blocked on another issue that isn't currently downloaded AND we were using either `currently_in_status` or `currently_in_status_category`.
* Bug: Fixed bug where `info` was only being written to the log and not to the console, making it seem if it weren't working at all.
* `info` improvement: Statuses now list the id of the status as well as the name.
* Jira will happily let you create two columns on a board with the same name. Unfortunately there are no id's with those columns so there is now no way to tell them apart. This change will add a numerical suffix such as Backlog-2 to duplicate columns so that we're able to work with them.

# v2.10 (February 6, 2025)

* Cycletime Histogram improvements
  * Added a collection of statistics under the chart. Thanks to Fernando Cuenca for the contribution.
  * Added percentile lines on the chart.
* Bug: Fixed exception in rare case when comparing output rows
* Bug: Fixed some timezone logic, for which the unit tests only failed when being run somewhere other than the Pacific Timezone.

# v2.9.1 (January 21, 2025)

* Fixed an exception when running the tool for the very first time.

# v2.9 (January 17, 2025)

* Status/category mappings have been an ongoing pain point for a long time. When a status gets removed from Jira, all of a sudden the reports can't be generated until someone sets up a `status_category_mapping` and it isn't always obvious what the values should be. We are now much better at guessing the correct mapping and will use that guess where we can. You will see suggestions in the output, where we guessed and what values we used.
* Bug: Fixed an exception in the data quality report when a status can't be found at all.
* On StatusCollection, there used to be methods `todo`, `in_progress`, and `done` that would return the names of statuses that matched those categories. Since we now know that names aren't unique, those methods have just been removed. They hadn't been documented before and were never officially supported so we aren't going through a deprecation process.
* Bug: Fixed case where the quality report didn't always correctly warn about items that had been moved back to the backlog.
* Whenever a status or status-category is specified, we can do a better job of identifying typos.
* The methods `first_time_in_status_category`, `still_in_status_category`, and `currently_in_status_category`...
  * ...will now accept names ("Done"), name/ID pairs ("Done:3"), or just ID's ("3"). If the name and ID are both specified then we will verify that they match and raise an error if they don't.
  * ...will also accept one of the following keys `:new`, `:indeterminate`, or `:done` to reflect the three categories. The code had previously worked only with names but those names will change in non-English installations of Jira so the keys are better. 
    * Why is it called _"indeterminate"_? That's a question for Atlassian; we just use the key names that Jira uses internally.
* `standard_project` now uses the keys, which should make things easier for non-English instances.
* Added a quality check for the case where an issue is identified as being blocked by another issue and yet that other issue is already closed. This is clearly a mistake and the quality report will now show it.

# v2.8 (December 30, 2024)

The main focus of this release is improving the experience with better error messages, and more helpful suggestions.

{: .tip }
If you're seeing a ton of warnings after upgrading to 2.8, try commenting out all your `status_category_mappings` first. You may find that you don't need them anymore and removing them might just fix all the warnings that you're now seeing.

* Bug: It wasn't possible to clear the description or title texts on individual charts.
* Added `first_time_label_added` for determining start or end times.
* Moved the generated timestamp of the report to the footer.
* Added the version number of JiraMetrics to the footer so there's a record of what version of the tool was used for this particular report.
* `no_earlier_than` previously only worked during the download stage and this proved to be confusing as there was no effect when just doing an export. It now works at both points and should be clearer as to what is actually happening.
* All the methods like `first_time_in_status` used to return a Time object and now return the ChangeItem that happened at that time. Unless you're writing your own methods, you likely won't even be aware that anything changed here. If you are, then you'll get a warning when returning a Time. The reason for this change is to give more context that will be useful in future reporting.
* Bug: We've discovered that Jira allows for multiple statuses with the same name and when this happens, it broke some of our existing logic. Fixes for this case have been added.
  * In `standard_project`, when specifying `status_category_mappings`, it's now possible to specify the ID at the same time as the name. "Review" will continue to specify just the name and "Review:4" will specify the name and also an ID of 4.
  * Status ID's are now mandatory and the tool will do it's best to guess the correct one, however it won't be able to guess in every case and you'll now get a error when it can't.
* Bug: some status category logic was failing in non-English instances of Jira because we were relying on category names, rather than keys. We think we've caught all the cases of this.

# v2.7.3 (November 13, 2024)

* Fixed an exception when two scrum projects had overlapping names like `myproject` and `really_myproject`. Our matcher wasn't precise enough and was matching where it shouldn't have.

# v2.7.2 (November 11, 2024)

* Fixed a regression that prevented a new full download from working. Incremental downloads hadn't been affected.

# v2.7.1 (November 5, 2024)

* All documentation has been moved from the Github wiki to [this site](https://jirametrics.org) that you're looking at now. There are places in the code where error messages refer to specific pieces of documentation and they've been updated to point to the new place.
* Cleaned up the look, and implementation of the data quality report.

# v2.7 (October 27, 2024)

* Fixed misleading usage information when running `jirametrics` with no options.
* Fixed deprecated warning that would display if an unknown option was given to `jirametrics`
* Added `info` command from terminal for debugging purposes. If you are trying to figure out why the tool has calculated something in one way then this will dump out information about that particular issue that might be helpful. Usage `jirametrics info ABC-123` where ABC-123 is the issue you want information on.
* Experimental: Trying out a way to visualize flow efficiency. I'm not convinced this is the right way yet but I'm making it public so I can get feedback. This may change. Add `flow_efficiency_scatterplot` to your configuration. If you want to try this from `standard_project` then specify the option `show_experimental_charts: true`.
* Fix: `discard_changes_before` was always intended to work either as an option under `project` or under `html_report` but there were cases where it was broken in the former case.
* Fix: There are legitimate cases where the calculated start and end times for an issue are exactly the same. Consider `in_or_right_of_column` where Done is legitimately to the right of that. In this case, we now pretend that the issue never started, which in almost every case will be the expected result anyway. In the cases where that isn't true, our configuration is probably wrong anyway.
* Deprecated: `CycletimeConfig.started_time(Issue)` and `CycletimeConfig.stopped_time(Issue)`, which are now replaced with a single method that returns both: `CycletimeConfig.started_stopped_times(Issue)`. This was necessary to fix the bug above, where started and stopped are the same.
* Added another quality check. It now checks for subtasks that aren't closed when the main issue is closed. This likely indicates that the issue is still in progress, despite what the status says.

# v2.6 (October 2, 2024)

* When a status can't be found, it now just dumps a warning, rather than blowing up. See Q1 in the [FAQ](https://jirametrics.org/faq#q1). This has been a source of frustration for many people so we're trying to get some more reasonable default behaviour. Note that you still want to fix it but now you'll see all the missing statuses at once instead of getting one each time.
* There is a new `Issue.flow_efficiency_numbers` method that is a first step towards visualizing flow efficiency. Note that none of the charts currently make use of this. See this article for [an introduction to flow efficiency](https://improvingflow.com/2024/07/06/flow-efficiency.html).
* By default, we assume that `flagged` means blocked but not all teams use the flag for that purpose. Added new setting to change that behaviour: `settings['flagged_means_blocked'] = false`.

# v2.5 (September 22, 2024)

* Better error messages in the case where `settings['customfield_parent_links']` does not point to the correct field.
* Fixed exceptions when loading a scrum board that has no sprints.
* Added `no_earlier_than` option in `download` block. This is helpful when there were some big changes in the way we worked and we want to ignore all data before a specific date. This can be used together with `rolling_date_count` or by itself.
* `standard_project` now accepts `rolling_date_count` and `no_earlier_than` options.
* `expedited_priority_names` has been deprecated on the board block. Now set it through `settings['expedited_priority_names']`
* Provided more reasonable defaults for `DependencyChart`. Everything is still configurable if you don't like the defaults. 
  * Less noisy in terms of console output.
  * Now excludes any link where both ends are already closed.
  * Now excludes `cloned by` that everyone was manually excluding anyway.
  * Now properly uses the calculated `done` rather than looking at status category.
  * `standard_project` and `aggregated_projects` now build on the new defaults.
* More reasonable defaults in settings. As above, these can be overridden if you don't like the defaults.
  * `"blocked_link_text"` now defaults to `["is blocked by"]`
  * `expedited_priority_names` now defaults to `['Critical', 'Highest']`


# v2.4 (August 16, 2024)

* The unit tests have never worked reliably on JRuby due to differences between JRuby and MRI (what we develop on). Fixed all the inconsistencies so that unit tests now work reliably on both MRI and JRuby.
* Fixed bug where blocked and stalled were not being reported correctly in some cases.
* In the (rare) case that jira redirects to a different URL, follow those redirects.

# v2.3 (June 3, 2024)

* Renamed `story_point_accuracy_chart` to `estimate_accuracy_chart` to more accurately capture what it does; it's not just story points. The old name will continue to work for now but has been deprecated and you'll get a warning.
* Explicitly force all files to be loaded in UTF-8 to fix bug #27 happening on JRuby.

# v2.2.1 (May 16, 2024)

* Fixed exception when description_text was set to nil on a chart

# v2.2 (May 16, 2024)

* Daily WIP charts now have the ability to show trend lines. Daily WIP by age now has one shown by default.
* Aging Work Table: 
  * When an aging cutoff is in use, it's now more visible and obvious
  * Header and description text wasn't overridable. It is now.
* Daily WIP by parent has been an example of how to create a chart for a while. It's proven to be generally useful enough that it's now a built-in chart. Add it with `daily_wip_by_parent_chart`
* Improved error messages in a couple of cases.

# v2.1.1 (May 8, 2024)

> [!NOTE]
Where's v2.1? It had a packaging problem that didn't get caught before it was deployed. The symptom is that the tool would throw an exception on load and not run at all, so it was unusable and we've removed it. v2.1.1 fixes all of that and is what we'd intended to deploy as v2.1.0. We've also added a new step to our testing process to ensure this doesn't happen again.

* CSS Customization
  * Added the setting `include_css` which will specify how to find a CSS file that you have written, so you can override the default styling. Under the project declaration, you can say `settings['include_css'] = 'extra.css'`
  * Externalized all colours used in all charts (except the dependency chart) to the CSS, which will make the previous point more useful.
    * _The dependency chart has its own complications and we haven't figured out yet how we're going to make that customizable._
    * References to colour names in the description text above the charts has been changed to use colour blocks.
  * Dark mode is now supported and the report will obey the setting that the operating system uses.
    * _Except for the dependency report as above. It will remain in light mode all the time until we can fix that._
* Changes to colours
  * Generally tweaked colours to be more consistent
  * Changed the way we show status names to use a colour swatch instead of drawing them in the colour of the category. The blue of "In Progress" was misleading and appeared to be a link.
  * Daily WIP by age chart now has completely different colour set. The previous set was functional but not pretty so hopefully you like this set better. If not, you can customize them, as above.
* Changes to configuration
  * Renamed a couple of internal setting names that had never been documented. In theory, this shouldn't break anyone but if you were relying on undocumented behaviour then be aware...
    * `stalled_threshold` has become `stalled_threshold_days`
    * `colors/stalled` has been removed and replaced with the CSS variable `--stalled-color` that you can customize as above.
    * `colors/blocked` has been removed and replaced with the CSS variable `--blocked-color` that you can customize as above.
  * Added `settings: options` to the example projects so you can customize settings.
* Bugs
  * There were a couple of cases where you could make a change in Jira and immediately download a report and the data wouldn't reflect the changes you just made. Fixed.
  * Bug #24 highlighted a particularly horrible error message. Now have a more reasonable error in that case.
* Examples
  * [standard_project](https://jirametrics.org/config/standard_project/) now allows you to pass in status_category_mappings

# v2.0.1 (April 23, 2024)

* Fixed exception in rare case where history has an entry with no items
* Improved error messages when an issue cannot be parsed. The issue key will now be included in the output.

# v2.0 (April 22, 2024)

> [!WARNING]
> Things that had previously been deprecated but had continued to work, have now been removed.
>
> If you've been paying attention to deprecation warnings up to now then there should be no surprises in here. However, if you've been ignoring warnings then this version is a potentially breaking change.

* [Removed] Quite a while ago, we changed the directory where issues would be loaded from but the logic still remained to look in the old location as well as the new. Everyone should be on the new by now so removed that unneeded code.
* [Removed] The old way of configuring grouping rules was deprecated over two years ago but still worked. No longer supported.
* [Removed] Board ids being specified on the download block had been deprecated and are no longer supported.
* [Removed] Project keys, JQL and filter ids being specified right on the download block had been deprecated and are now removed.
* [Removed] AgingWorkTable: passing the `priority_name` right into the chart had been deprecated and is now removed
* [Removed] Chart `total_wip_over_time_chart` had been deprecated and replaced with `daily_wip_by_age_chart`. Now it's removed.
* [Removed] Chart `blocked_stalled_chart` had been deprecated and replaced with `daily_wip_by_blocked_stalled_chart`. Now it's removed
* [Removed] Passing the `priority_name` directly into the expedited chart has been deprecated for two years. Specify that in the board declaration.
* [Removed] Explicitly calling the `discarded_changes_report` was deprecated because it's always generated now.
* [Removed] In the `Issue` class, the method `status_id` was deprecated in favour of `status.id`. Removed.
* [Removed] `status_category_mapping` used to take a type parameter but that was deprecated because we don't need it anymore. Removed now.
* [Removed] The story point estimation chart used to support a `grouping` but that was deprecated when we changed the type of the chart. Removed.
* [Removed] If you still had some really old data with the old format for statuses, it was continuing to load because we had redundant code just for that. Removed that extra code.
* Generally tightened up all the logic around loading statuses, to account for the edge cases introduced by team-managed projects. There will be cases where you'll need to explicitly add a project id to the config and you'll do that as shown. The id won't be needed in most cases so I'd leave it off until the tool complains about needing it.
```ruby
project id: 5 do
  ...
end
```
* [Aggregated project sample](https://github.com/mikebowler/jirametrics/blob/main/lib/jirametrics/examples/aggregated_project.rb):
  * Added a WIP chart by parent.
  * Added a dependency chart that only shows dependencies that cross boards
* Remove null fields from the JSON that came back from Jira, before saving them locally. On some instances, this simplistic compression can reduce the JSON file size by half.

# v1.5 (April 16, 2024)

* Turns out that the board information returned by Jira Data Centre does not contain the project id and this was causing an exception. Fixed.

# v1.3 (Apr 5, 2024)

* Added partial support for NextGen (team managed) projects in order to fix bug #22 where a NextGen project anywhere in the instance could cause jirametrics to blow up, in rare cases. The bug looks like this even though you hadn't set a `status_category_mapping`:
```
/jirametrics/project_config.rb:205:in `add_possible_status': Redefining status category Status(name="FakeBacklog", id=10012, category_name="To Do", category_id=2) with Status(name="FakeBacklog", id=10017, category_name="In Progress", category_id=4). Was one set in the config? (RuntimeError)
```
* Tweaked the `standard_project` a bit

# v1.2.1 (Oct 5, 2023)

* Fixed some misleading text on the AgingWorkBarChart
* Fixed bug with loading the English gem that appeared to only happen on linux systems

# v1.2 (Sep 14, 2023)

* Fixed bug in link creation when Jira isn't hosted at the top level of the domain.

# v1.1 (Aug 22, 2023)

* Added the command 'go' that will do a download, followed by an export
* Fixes a bug when Jira chooses not to return a creator element in the issue. Theoretically impossible but it's been seen in production.

# v1.0.2 (Aug 3, 2023)

* Added --name parameter to export and download so that we can selectively work with a subset of the named projects. Wildcards are supported just as in a terminal. Usage: `jirametrics export --name "a*"` to export only the projects starting with 'a'.
* Fixed bug when including individual projects into an aggregated one. If issues had been excluded in the individual project, they were still appearing in the aggregated.

# v1.0.1 (July 3, 2023)

* Commands are now through the jirametrics tool, rather than rake. So you will execute `jirametrics export` rather than `rake export`
* Project is now packaged as a gem 'jirametrics'. This changelog will now list changes by version number instead of by date. `gem install jirametrics`
* Project has been renamed to jirametrics from jira-export because there was already a ruby gem by that name.

# June 2023

* Added completed items to the Daily WIP by Blocked and Stalled chart. In the process, fixed a bug that had been causing miscounts in the total.
* Fixed bug where an item could be incorrectly marked as Blocked by Status in some cases where it was actually stalled

# May 2023

* Issue.blocked_percentage has been removed. We're guessing that you didn't even know it was here and won't care that it's gone. The method was incomplete, in that it didn't track all possible ways an issue could be blocked, and generally shouldn't have been there in the first place.
* Certain statuses can be set up as 'stalled' and will reflect as stalled on all the reports. Configuration details are with [project_config](https://jirametrics.org/config/project/)

# April 2023

* StoryPointAccuracy chart now has the ability to use other types of estimates such as TShirt Sizes
* StoryPointAccuracy chart now uses size, rather than colour to indicate how many data points are at a specific spot. Also, it provides the option to see aging work in addition to completed.
* A side effect of above, is that now the grouping tag has been disabled as it doesn't make sense anymore.
* AgingWorkInProgressChart will now display an extra [Unmapped Statuses] column if there are any aging issues that aren't actually visible on the board. It's clearly a mistake if we have aging issues that aren't visible and this will make that more obvious.
* DailyWipByBlockedStalledChart now shows how long something has been stalled or why it's blocked.
* Removed some methods on Issue. stalled_on_date?, blocked_by_flag?, in_blocked_status_on_date? All of these are replaced with the new logic in Issue.blocked_stalled_changes() which provides far more detailed information about each state.
* The issue class now includes links in reasons that an item could be blocked.
* AgingWorkBarChart now uses times rather than dates when plotting blocked and stalled, which results in more accurate data.
* AgingWorkBarChart now displays in hover text why it's blocked. If it's blocked on a status then you'll see the status. If it's blocked on other issues, you'll see the issue ids.
* AgingWorkBarChart stalled now shows as starting right away rather than not starting for a full five days, which had been misleading in the past.
* Fixed bug where Issue.first_time_in_or_right_of_column would fail after anonymization
* AgingWorkBarChart now shows why an item is blocked (Flagged, status, etc) and not just that it is blocked.
* Certain statuses can be set up as 'blocked' and will reflect as blocked on all the reports. Configuration details are with [project_config](https://jirametrics.org/config/project/)

# March 2023

* AgingWorkBarChart will now grow the chart if there are too many issues. Previously, the chart would omit issues when too many were there and that was very misleading. It was also very hard to read when there were large numbers of issues.
* Fixed exception in the rare case where a status that is no longer accessible to the project is still mapped to 'backlog' on a Kanban board. This is another "theoretically impossible" situation that we've now seen in a production system.

# January 2023

* Fixed bug where we didn't properly identify the backlog statuses in some cases
* Fixed bug where the log file name wasn't always being displayed during download.
* The log now writes to the root, rather than the target directory. In configurations that would switch target directories mid-download, it was ambiguous where it would actually show up.
* Explicitly throw exceptions if status names can't be found. The positive is that this will catch typos in confiration files more easily. The negative is that it may require more status_category_mapping declarations
* Fixed bug where sprints weren't showing up on sprint burndown in rare cases
* Now supports the personal access key form of authentication. Why does Jira have two different kinds of access tokens?

# December 2022

* Aging work table now shows when the work was expedited.
* Fixed bug in aging work table where some blocked and stalled times weren't showing properly.
* Expedited priority names are now specified on the board, not on the individual charts. Also, you can now specify multiple different names.
* Added the canvas method on all charts so that you can override the default dimensions of any chart. Rarely needed when visualizing a single teams data but becomes valuable when visualizing multiple teams at once.
* Fixed a bug where the data could be messed up if some groupings didn't have a colour set.
* Cleaned up the output. What goes to $stdout is now readable and the full messy log goes to a file
* Aging Work Burndown now shows blocked and stalled.

# November 2022

* Fixed bug where the final line segment wasn't being drawn for an active sprint in the burndown

# October 2022

* project_key, filter, and jql declarations under the download section no longer work. See the deprecated page for details. Specifying a board id is now enough to pull all the right issues for that board.
* Many changes under the covers to prepare for aggregated projects, where we pull a bunch of projects together to show a common report across all of them.
* Fixed bug where items that were already expedited on creation, might not show on the expedited report.

# September 2022

* Moved all quality checks to their own section at the top of the page. This will make the report more compact
* Moved discarded report into normal data quality checks
* Added a new quality check that will identify if subtasks are starting while the main issue is not.
* The 'items moving backwards' quality check no longer reports on items that were moved to the backlog. Those are covered in a different report
* The daily WIP charts have been completely rewritten to make them easier to extend and customize. More details to come on how you can customize them in your own reports.
* The two daily WIP charts (by age and by blocked/status) now show items that have completed but never satisfied the criteria for started. These are problematic so we're making them more visible.
* Some of the methods to configure reports have been renamed to be more consistent. If you use the old name then you'll get a deprecated warning along with the new name to use. In general, the names of these reports have been very inconsistent so you'll see more of these consistency changes as we go forward.
* Colours and ranges are now configurable on the story point estimate chart
* Fixed an interesting bug in the downloader where Jira would occasionally not download some issues that were in a sprint but had not yet been started. Only affected Scrum boards. After updating to the latest code, the code will do one full download before resuming incrementals and this is to ensure that we've downloaded all the issues we might have missed.
* Added summary statistics to the scrum burndown chart.

# August 2022

* Pagination wasn't working properly when there were multiple pages of sprint data. Fixed.
* More quality checks for the story point accuracy chart.
* Added :backlog option when checking to see if an item was moved back to the backlog
* Added fix versions to the aging table. Also put an indicator when the current status isn't visible on the board. The really old work in the aging table is sometimes there because the issue is no longer visible on the kanban/scrum board and the team has therefore forgotten about it. These changes will make it more obvious when this is the case.
* If you don't specify any of project_key, filter, or jql then we look at the configuration for the board (assuming you set exactly one) and will use the filter specified there.
* The anonymizer would periodically blow up due to a bug in RandomWord. Put in retry logic to work around that.
* Fixed bug where a start and stop that happened at the same time could put the stop before the start. This made several charts inaccurate - particularly the WIP charts.
* Put in a quality check to look for issues that stop before they start. There are several cases that could cause this (not just the bug above) and when it happens, the charts become a mess.

# July 2022

* Added status column to aging work table
* All charts can now customize the header and description by using header_text and description_text in their config
* All charts can now customize which quality checks are shown. The most common use case will be to turn all checks off but you can now tweak it any way you want. Use check_data_quality_for with no parameters to disable them all.
* Issue.stalled_on_date? will now look across subtasks (if present) to see if there was activity on those.
* Downloader now relies on the status category of 'Done' rather than checking the resolved field. Turns out that it's not mandatory to use resolved and for some projects that meant the downloader was pulling down massive amounts of data every time.

# May 2022

* Issue.status now returns a Status object rather than a string.
* Dependency Chart: Labels for issues can now be overridden in rules
* Comments now get included in changes which means that the stalled report will now take comments into account
* Cycletime Scatterplot, Cycletime Histogram, and Aging Work in Progress charts now all support the same grouping rules that are in Throughput Chart.
* Throughput Chart now defaults to splitting data by issue type and also displaying a total line. It's still configurable with grouping rules if you want the old behaviour.

# April 2022

* Throughput Chart now supports grouping rules
* Speed improvement for incremental downloads.
* Fixed exception when timezone_offset wasn't specified
* New chart: Dependency Chart to show all the links between issues. This requires that graphviz be installed. See docs.
* New chart: Story Point Accuracy to compare story point estimates to actual measured cycle times.
* Fixed bug where both burndowns were always being displayed regardless of which option was specified.
* Table headers are now "sticky" which means they'll stay on screen as you scroll. Requires newer browsers.
* Fixed bug in the cycletime scatterplot where the 85% lines could be incorrectly placed.
* Fixed bug where the html_report was requiring a board id even in cases where it wasn't ambiguous.
* jql has been reintroduced as an option on the download element so you aren't limited to project_name and filter_name.
* A debug message has been added to the downloader to make it more obvious how many more issues there are to pull.
* When we're pulling configuration information, if the board is "scrum" then we also download sprint data.

# March 2022

* Changed the API we use to collect statuses so in theory, you won't have to add status_categories_mappings to the config anymore.
* Support for incremental downloads so we're only pulling those items that have changed, rather than all items in the system.
* Changed how issues are stored in the target directory. We now store them as individual files rather than in the groupings that JIRA had originally returned. Done to support incremental downloads.
* Added *discard_changes_before* so we can pretend that items moved back to the backlog hadn't actually started. If you do use then then also add the *discarded_changes_report* so you can see how this impacted any metrics you generate. _Allowing items to move back to the backlog after being started is a poor practice as it hides the reality of what's been done. Think carefully before allowing that or using this feature._
* Changed quality notes to use tables instead of bullets for improved readability
* Improved the error messages in the case where we can't determine what status-category a particular status belongs to.
* Started tracking changes here
