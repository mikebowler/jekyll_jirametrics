---
layout: page
permalink: /security/
title: Security Concerns
---

In many companies, there are valid concerns about letting people directly access the data in our Jira instance. This page should give you enough information to understand the potential risks so you can decide if allowing this in your environment is the right answer.

TL;DR: No additional risk is introduced by installing JiraMetrics. Any risk present, was already there.

## Where does the information come from and where is it shared?

JiraMetrics retrieves information through the publicly available API on your Jira instance. It calls only your Jira instance and no other servers. Information retrieved through this API is stored on the file system of the machine that made the request and is not shared anywhere else.

## We have confidential information in our Jira instance. How do we restrict access to only certain projects?

JiraMetrics has the same access level as the person who is running it. If that person can access your confidential projects through a web browser then that same information is available through the API. There is no additional risk here as you had already granted access to this person.

Note that this information is available through the API whether or not you use JiraMetrics.

## Is there auditing so we can see who is calling the API and what information they have retrieved?

I don't know. That would require looking at the Jira logs and only your Jira administrator would have access to that. We call publicly available API's.

## Does JiraMetrics write any information into the Jira instance?

No, we read data only. No information is written back to Jira.
