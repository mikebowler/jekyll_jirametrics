---
layout: page
permalink: /jira/
---
This configuration step is all about how `jirametrics` connects to your Jira instance, and this can sometimes be the hardest step. Using one of the three methods below, we've been able to connect to every Jira instance we've tried. If you encounter one that you just can't connect to, let us know.

Create a file such as "jira.config". The actual name doesn't matter at this point because the main configuration file will contain a reference to it. What goes into this file depends on the type of authentication that you use with your Jira instance.

{: .tip }
This file will contain access tokens and/or cookies that are unique to you and they should be considered confidential information. Do not check them into version control or share with others. There's a reason that they're in a different file from the rest of the configuration.

The code currently supports three authentication mechanisms with Jira. There are two different access tokens supported by Jira and your instance will support one or the other. If you can't get either working then fall back to the cookie method.

At the time of this writing (April 2024), Jira Cloud supports the API-Token. Jira Server and Jira Data Centre support the Personal Access Token. No configuration supports both. Cookie based authentication has been deprecated in Jira Cloud and at some point will just stop working, but that was the least desirable option anyway. If you're not sure which version of Jira you're using then just try the instructions one by one and see which one works. The other kind of token just won't be an option for you to pick.

# 1. Authentication with the API Token (Cloud)

Navigate to https://id.atlassian.com/manage-profile/security/api-tokens and create an API token. Insert that token in the jira.config like this:

```json
{
  "url": "https://<your_url>",
  "email": "<your email>",
  "api_token": "<your_api_key>"
}
```

We've seen documentation that implies that you could replace the token with your password for a Jira Server installation but we don't have an environment to test that in.  Even if it does work, it would be sending your password in the clear, which is a significant security exposure.

# 2. Authentication with a personal access key (Server or Data Center)

Navigate to your profile in Jira and on the left menu there will be an option to manage personal access keys. If you create one then you can use this as shown.

```json
{
  "url": "https://<your_url>",
  "personal_access_token": "<your_personal_access_token>"
}
```

# 3. Authentication with cookies

This next option is fairly ugly and should be your last resort. It's also been deprecated in Cloud and will stop working at some point.

Once you've logged in with your web browser, your browser will now have authentication cookies saved. If you go into the settings for your brower and copy them, this code can use those cookies for authentication. Generally Jira will set three different cookies and you need them all.

You'll need to refresh the cookies periodically (daily?) so it's annoying.

```json
{
  "cookies": {
    "<key>": "<value>"
  }
}
```