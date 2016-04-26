![GitLab Listener banner](https://github.com/allenta/gitlab-listener/raw/master/banner.png)

**[GitLab Listener](https://marketplace.atlassian.com/plugins/com.allenta.jira.plugins.gitlab.gitlab-listener) is a JIRA add-on allowing the reception of [GitLab](https://about.gitlab.com) push events using GitLab project web hooks.** This add-on allows integration of commits pushed to GitLab inside JIRA issues. Issue keys are extracted from commit messages; then, these messages are logged as JIRA comments, activities, work logs and / or workflow transitions linking back to the GitLab changesets.

QUICK START
===========

1. **Install the add-on** following the [instructions available in the Atlassian Marketplace](https://marketplace.atlassian.com/plugins/com.allenta.jira.plugins.gitlab.gitlab-listener).

2. **Configure the add-on** according with your preferences. Optionally you can set:
    - **A shared secret** between GitLab & JIRA.
    - **A list of e-mail addresses mapping to JIRA usernames** to be used when the e-mail address in a commit message does not match any existing JIRA user.
    - **Several fallback JIRA usernames** to be used when a JIRA user is not available. If a JIRA user is not available while processing commits, comments / activities, work logs and / or workflow transitions linked to those unmatched commits won't be generated / executed.

3. **For each GitLab project you want to integrate with JIRA, set up a web hook** in order to submit all push events to the listener add-on. If defined in the previous step, remember to include the value of the secret in the query string of the URL. When integrating multiple GitLab instances with JIRA you must use the ``instance`` field in the query string (simply use a different instance name in each GitLab instance; this allows the add-on to differentiate commits notified by each instance):

    ```
    https://www.example.com/jira/plugins/servlet/gitlab/listener?secret=t0ps3cr3t&instance=default
    ```

4. **Start pushing commits with messages including references to JIRA issues and work log information.** Some examples:

    ```
    # Appends a JIRA comment / activity to issue ALLGEN-10.
    # Changes issue ALLGEN-10 status executing a workflow transition using action named 'close issue'.
    # Sets issue ALLGEN-10 resolution to 'fixed'.
    $ git commit -m "ALLGEN-10 Fixed memory leak #action close issue, fixed"

    # Appends a JIRA comment / activity to issues ALLGEN-10, ALLGEN-15 and GTTSLT-6.
    # Changes issues ALLGEN-10, ALLGEN-15 and GTTSLT-6 status executing a workflow transition using action named 'reopen issue'. 
    $ git commit -m "ALLGEN-42 ALLGEN-15 GTTSLT-6 Fixed some memory leaks #action reopen issue"

    # Appends a JIRA comment / activity to issue ALLGEN-14.
    # Appends a JIRA work log to issue ALLGEN-14.
    $ git commit -m "ALLGEN-14 #time 1w 2d 4h 30m"

    # Appends a JIRA comment / activity to issues ALLGEN-22 and ALLGEN-10.
    # Appends a JIRA work log to issues ALLGEN-22 and ALLGEN-10, each one including the description 'Completed ahead of schedule'.
    $ git commit -m "Added all diagrams #time 1w 2d 4h 30m Completed ahead of schedule #refs ALLGEN-22 ALLGEN-10"

    # Appends a JIRA comment / activity to issues ALLGEN-12 and GTTSLT-2.
    # Appends a JIRA work log to issues ALLGEN-12 and GTTSLT-2.
    $ git commit -m "Bumped new version #refs ALLGEN-12 GTTSLT-2 #time 1h 15m"
    ```

COMMIT MESSAGES
===============

- **Commit format is `<ISSUE-1> [<ISSUE-2> <ISSUE-3> ...] <description of the commit> [#refs <ISSUE-4> [<ISSUE-5> <ISSUE-6> ...]] [#time <duration> [<annotation to be included in the JIRA work log>]] [#action <workflow action to be executed>[, <resolution>]]`.**

- **The configured JIRA issue key format (`jira.projectkey.pattern` property) is the one supported.** This format usually is two or more uppercase letters, followed by a hyphen and the issue number, for example ALLGEN-123.

- Users can reference **multiple issues in the same commit message**, separated by whitespace at the begging of the message or using the `#refs` directive anywhere inside the commit message. There is also an option available to enable searching of issue keys in the whole commit message.

- You may **enforce inclusion of JIRA issues in all commit messages** defining a simple git hook in the local copies of the repositories.

- **The e-mail address included in the commit data must match some e-mail address in the JIRA user base.** Otherwise, if defined in the add-on settings, some explicit e-mail address - JIRA user mapping rules will be used. Finally, fallback JIRA users will be used if everything fails and they are defined in the add-on settings. Along with a match of the e-mail address:
    + The JIRA user must have permission to comment issues in the particular project. Unlike comments, generation of JIRA activities does not require any special permission. 
    + When using the `#time` directive, the user must have permission to log work on the issue.
    + When using the `#action` directive, (1) the user must have permission to execute workflow transitions to the issue; and (2) the workflow action to be executed should be valid according with the current status of the issue. 

- **When referencing more than one issue and using the `#time` directive, a entry is added to the work log of all the referenced issues.**

- **When referencing more than one issue and using the `#action` directive, a workflow transition is executed in all the referenced issues.**

GITLAB LISTENER VS. BUILT-IN GITLAB-JIRA INTEGRATION
====================================================

GitLab CE / EE includes a basic GitLab-JIRA integration. It covers some simple use cases, but that may be not enough in some scenarios. This add-on extends the basic integration allowing you to:

- Reference multiple issues in a single commit message.

- Use commit messages to execute any workflow transition (i.e. not just closing issues).

- Use commit messages to generate worklogs (i.e. time tracking).

- Restrict visibility of comments, etc. generated by the add-on to a project role.

- Select between generating JIRA comments or JIRA activities.

- Link GitLab and JIRA users instead of generating JIRA comments using a generic JIRA API user as the basic integration does.

- Support multiple JIRA issue key formats.

HOW TO ENABLE DEBUG LEVEL?
==========================

1. Log in as an user with the 'JIRA System Administrators' global permission.

2. Choose 'System'. Then select 'Troubleshooting and Support > Logging & Profiling' to open the Logging page.

3. Scroll to the 'Default Loggers' section and click 'Configure logging level for another package'.

4. Package name is ``com.allenta.jira.plugins.gitlab.listener.Servlet``.

PERFORMANCE
===========

The add-on uses a simple underlying table (``AO_9B0D9B_PROCESSED_COMMIT``) to keep track of the commits already processed. Because of limitations of the Active Objects framework, the add-on cannot create indexes and unique constraints that span more than one column on installation time.

Please, ask you DBA to manually create an index and unique constraint spanning the ``INSTANCE``, ``PROJECT`` and ``HASH`` columns. This is not required, but it should boost performance on busy environments.
