---
layout: page
permalink: /faq/
---
## Q1: I'm getting an error about a status not being found

`Status name "My Status" for issue ABC-123 not found...`

What this means is that there used to be a status by that name but it's since been deleted in your Jira instance. Unfortunately, the history still references it and so we need to know information about it, but Jira no longer knows anything about it.

* If you're using the `standard_project` declaration then use the `status_category_mapping` [as defined here](https://github.com/mikebowler/jirametrics/wiki/Examples-folder#standard-project).
* Otherwise the other `status_category_mapping` is defined [over here](https://github.com/mikebowler/jirametrics/wiki/Common-configuration#status-category-mapping-declaration).

If you're using a version of JiraMetrics earlier than 2.6 then the error above will be a fatal error that will stop the app. Starting with 2.6, it will just be a warning message and we will make the assumption that the missing status belongs to the `In Progress` status category. That's not always a valid assumption though so you should still set the `status_category_mapping` anyway.

## Q2: I'm getting an error about being rate limited.

It might really be that you've hit the Jira instance too often. It might also be that your access token was deleted and Jira is just returning a misleading message