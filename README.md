![GitLab Listener banner](https://github.com/allenta/gitlab-listener/raw/master/banner.png)

**[GitLab Listener](https://marketplace.atlassian.com/plugins/com.allenta.jira.plugins.gitlab.gitlab-listener) is a JIRA add-on allowing the reception of [GitLab](https://about.gitlab.com) push events using GitLab project web hooks.** This add-on allows integration of commits pushed to GitLab inside JIRA issues. Issue keys are extracted from commit messages; then, these messages are logged as JIRA comments, activities, work logs and / or workflow transitions linking back to the GitLab changesets.

QUICK START
===========

1. **Install the add-on** following the [instructions available in the Atlassian Marketplace](https://marketplace.atlassian.com/plugins/com.allenta.jira.plugins.gitlab.gitlab-listener).

2. **Configure the add-on** according with your preferences. Optionally you can set:
    - **A shared secret** between GitLab & JIRA.
    - **Several fallback JIRA usernames** to be used when the e-mail address in a commit message does not match any existing JIRA user. If a valid JIRA user is not available while processing commits, comments / activities, work logs and / or workflow transitions linked to those unmatched commits won't be generated / executed.

3. **For each GitLab project you want to integrate with JIRA, set up a web hook** in order to submit all push events to the listener add-on. If defined in the previous step, remember to include the value of the secret in the query string of the URL:

    ```
    https://www.example.com/jira/plugins/servlet/gitlab/listener?secret=t0ps3cr3t
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
    $ git commit -m "ALLGEN-22 ALLGEN-10 Added all diagrams #time 1w 2d 4h 30m Completed ahead of schedule"

    # Appends a JIRA comment / activity to issues ALLGEN-12 and GTTSLT-2.
    # Appends a JIRA work log to issues ALLGEN-12 and GTTSLT-2.
    $ git commit -m "ALLGEN-12 GTTSLT-2 Bumped new version #time 1h 15m"
    ```

COMMIT MESSAGES
===============

- **Commit format is `<ISSUE-1> [<ISSUE-2> <ISSUE-3> ...] <description of the commit> [#time <duration> [<annotation to be included in the JIRA work log>]] [#action <workflow action to be executed>[, <resolution>]]`.**

- **The default JIRA issue key format is the only supported.** This format is two or more uppercase letters, followed by a hyphen and the issue number, for example ALLGEN-123.

- Users can reference **multiple issues in the same commit message**, but all of them **at the begging** of the message and **separated by whitespace**.

- You may **enforce inclusion of JIRA issues in all commit messages** defining a simple git hook in the local copies of the repositories.

- **The e-mail address included in the commit data must match some e-mail address in the JIRA user base.** Otherwise, if defined in the add-on settings, some fallback JIRA user will be used. Along with a match of the e-mail address:
    + The JIRA user must have permission to comment issues in the particular project. Unlike comments, generation of JIRA activities does not require any special permission. 
    + When using the `#time` directive, the user must have permission to log work on the issue.
    + When using the `#action` directive, (1) the user must have permission to execute workflow transitions to the issue; and (2) the workflow action to be executed should be valid according with the current status of the issue. 

- **When referencing more than one issue and using the `#time` directive, a entry is added to the work log of all the referenced issues.**

- **When referencing more than one issue and using the `#action` directive, a workflow transition is executed in all the referenced issues.**


CONFIGURATION OPTIONS
=====================

- checkbox "Use activities instead of comments"

When this option is unchecked, any changeset pushed to GitLab referencing one or more JIRA issues will be logged as a comment in the 'Comments' tab of the issue in JIRA. However, if the option is checked, the pushed changeset will be logged in the 'Activities' tab of the issue.