**[GitLab Listener](https://marketplace.atlassian.com/plugins/com.allenta.jira.plugins.gitlab.gitlab-listener) is a JIRA add-on allowing the reception of [GitLab](https://about.gitlab.com) push events using GitLab project web hooks.**

This add-on allows integration of commits pushed to GitLab inside JIRA issues. Issue keys are extracted from commit messages; then, these messages are logged as JIRA comments, activities and / or work logs linking back to the GitLab changesets.

QUICKSTART
==========

1. **Install the add-on** following the [instructions available in the Atlassian Marketplace](https://marketplace.atlassian.com/plugins/com.allenta.jira.plugins.gitlab.gitlab-listener).

2. **Configure the add-on** according with your preferences. Optionally you can set:
    - **A shared secret** between GitLab & JIRA.
    - **A couple of fallback JIRA usernames** to be used when the e-mail address in a commit message does not match any existing JIRA user. If a valid JIRA user is not available while processing commits, comments / activities and / or work logs linked to those unmatched commits won't be generated.

3. **For each GitLab project you want to integrate with JIRA, set up a web hook** in order to submit all push events to the listener add-on. If defined in the previous step, remember to include the value of the secret in the query string of the URL:

    ```
    https://www.example.com/jira/plugins/servlet/gitlab/listener?secret=t0ps3cr3t.
    ```

4. **Start pushing commits with messages including references to JIRA issues and work log information.** Some examples:

    ```
    # Appends a JIRA comment / activity to issue ALLGEN-10.
    $ git commit -m "ALLGEN-10 Fixed memory leak"

    # Appends a JIRA comment / activity to issues ALLGEN-10, ALLGEN-15 and GTTSLT-6.
    $ git commit -m "ALLGEN-42 ALLGEN-15 GTTSLT-6 Fixed some memory leaks"

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
