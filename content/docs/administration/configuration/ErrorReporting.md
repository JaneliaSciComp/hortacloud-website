---
title: "Error reporting"
linkTitle: "Error reporting"
weight: 100
description: >
  How to configure error reporting from within the Horta application.
---


HortaCloud has the ability to report errors during operation both automatically and at the user's request. These errors can be configured to be either sent via email to an external issue tracker (e.g., JIRA or similar), or to be posted to a GitHub project's issues via the GitHub API.

These reports will be controlled by environment variables in your `.env.config` file, derived from the provided `.env.template` file. Upon deployment, these values will be available to the application via Java properties.


# General information

{{% alert title="Privacy" color="primary" %}}
Note that issues and logs often contain information that should be kept confidential, even if they don't contain legally-protected personally identifiable information. This may include:
- usernames, which can be email addresses
- infrastructure information, including server names and IP addresses (whether local or in the cloud)
- scientific information, such as the names of samples or neurons; often scientists do not want information about their specific research targets to be public before publication
- any other information the user chooses to share with developers, possibly with the expectation that it will not be public

For these reasons, any issue tracker receiving error reports from HortaCloud should be configured to be private and only accessible by the developers and/or admins responsible for the instance.
{{% /alert %}}


## Base configuration

The base configurations are set directly in either `console.properties` or `client.properties`.

- `console.ErrorReportingMethod=github`
    + valid values: `email`, `github`
- `console.AutoSendExceptions=true`
    + valid values: `true`, `false`
    + if set to `true`, uncaught exception will automatically send an error report without user action or knowledge, if the error is silent
    + if set to `false`, only error reports initiated by the user will be sent


## Error report format

The reported errors have the following format. The examples are edited excerpts from real reports.

- The subject line will look like `[JW] Auto-report from [username] -- 9.19 -- java.lang.IllegalStateException: HTTP status 404 (not OK) from url https...`
    + The subject line will contain `User-report` if user-initiated, instead of `Auto-report`. 
- The body will contain a summary of the runtime environment:
    ```
    Subject: user:(username here)
    Application: Janelia Workstation v9.19
    Server: (server URL here)
    Operating System: Windows Server 2019 10.0 (amd64)
    Java: OpenJDK Runtime Environment 1.8.0_382-b05
    Disk Cache Usage: 0%
    System Memory Usage: 3%
    JVM Memory: 1.52GB / 7.11GB
    Memory Setting: 8
    ```
- After the summary, there is optionally a user comment, if the user initiated the report.
- At the end of the body is a copy of the log file.
    + For email reports, it's included in-line.
    + For GitHub reports, it's uploaded to the repository and linked in a comment on the issue.
    + Note that the log file can be several megabytes long!
        * However, it's not limitless. The log file will be truncated for long sessions, and only the most recent lines will be included.


## Repeated issues

Because issues can be created automatically by the HortaCloud application, you should expect to receive _many_ repeated issues, if an issue can be triggered during a common workflow. In worst case scenarios, this can be several dozen or more issues in hours, in spite of the issue report throttling built in to HortaCoud. The rate of message generation depends on the frequency of the issue and the size of the user base. 

Due to this repetition, it's useful to keep these automated and user issue reports in their own issue tracker separate from issues created by developers for managing their software development processes, or the one will overwhelm the other.


# Configuration

In the HortaCloud environment, environment variables such as those listed below are controlled through an `.env.config` file (derived from `.env.template`). See the deployment documentation for details.

Note that if `console.ErrorReportingMethod` is not set, the behavior will default to "email" for backward compatibility.


# Email configuration

The following environment variables must be populated with information about your email server:

- `MAIL_SERVER`
- `MAIL_SENDER`
- `MAIL_USER`
- `MAIL_PASSWORD` 
        * `MAIL_USER` and `MAIL_PASSWORD` are often for a service account
- `MAIL_RECEIVER`
    + this is the address to which email is sent; your issue tracker will access this account

Note that the email values are Janelia-specific and should be changed.

Set `console.ErrorReportingMethod=email`.



# GitHub configuration

This section assumes some familiarity with GitHub and git.

A GitHub repository is a convenient place to hold issues even if it doesn't contain code. The free tier of repository is more than enough for this task. HortaCloud uses GitHub's REST API to create issues instead of email.

First, create a new private repository. The repository need not contain any files, though a `README.md` file explaining the purpose of the repository will be helpful.

Second, create a branch named `issues`. While the issues are created in the repository, the stack traces are uploaded as files to this branch, in a directory named `attachments`. GitHub issues are limited in size and are not large enough to contain our log files if included in-line in the issue.

Last, create a fine-grained personal access token (PAT) from the GitHub website. The PAT should have permissions only for this repository, and should have read and write access to code, issues, and pull requests. Set the expiration date for the token to whatever you are comfortable with. 

Issues will be created by this user's identity, not the identity of the user reported the issue. This user and any developer responsible for handling issue can then adjust their GitHub notifications for this repository to the level they choose. 

Then transfer the token and the repository's API URL to the following environment variables:

- `GITHUB_URL`: URL will look like this: https://api.github.com/repos/username/repository-name
    + note the inital `www` is replaced by `api` for this URL
- `GITHUB_TOKEN`: this is a long string beginning with `github_pat_`

Set `console.ErrorReportingMethod=github`

























