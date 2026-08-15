<!-- markdownlint-disable -->

# Hardening Report: archive--github-actions-slack/v3.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **archive--github-actions-slack/v3.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple workflow `run:` steps directly interpolate `${{ steps.*.outputs.slack-result }}` (a `steps.*.outputs.*` context value) into shell commands without routing through an env var. This is a rule-(a) violation: any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, allowing an attacker-controlled value to break out of the intended string context. Affected lines include `run: echo '${{ steps.send-message.outputs.slack-result }}'` and similar patterns across all workflow files.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:22`
- `.github/workflows/2-slack-notification.yml:18`
- `.github/workflows/3-slack-reaction.yml:20`
- `.github/workflows/3-slack-reaction.yml:33`
- `.github/workflows/4_v2-slack-thread.yml:24`
- `.github/workflows/4_v2-slack-thread.yml:36`
- `.github/workflows/4_v3-slack-thread.yml:23`
- `.github/workflows/4_v3-slack-thread.yml:35`
- `.github/workflows/5-slack-update-message.yml:21`
- `.github/workflows/5-slack-update-message.yml:33`
- `.github/workflows/6-slack-thread-with-broadcast.yml:22`
- `.github/workflows/6-slack-thread-with-broadcast.yml:33`
- `.github/workflows/7-slack-notification-multi-channel.yml:20`
- `.github/workflows/7-slack-notification-multi-channel.yml:23`
- `.github/workflows/8-slack-notification-line-breaks.yml:27`
- `.github/workflows/8-slack-notification-line-breaks.yml:38`
- `.github/workflows/8-slack-notification-line-breaks.yml:49`
- `.github/workflows/9-slack-update-message-line-breaks.yml:22`
- `.github/workflows/9-slack-update-message-line-breaks.yml:35`
- `.github/workflows/9-slack-update-message-line-breaks.yml:51`
- `.github/workflows/9-slack-update-message-line-breaks.yml:67`
- `.github/workflows/11_v2-slack-message-blocks.yml:53`
- `.github/workflows/11_v3-slack-message-blocks.yml:50`
- `.github/workflows/12-slack-message-blocks-update.yml:29`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:46`

### unpinned-uses (severity: high)

All `uses:` references across every workflow file are pinned to mutable refs (`@master` branch or `@v2.11.0` version tag) rather than immutable 40-character commit SHAs. This means a supply-chain compromise of the referenced action repository could silently alter the code executed by these workflows. Failing references include `archive/github-actions-slack@master` (in 14 workflow files) and `archive/github-actions-slack@v2.11.0` (in 2 workflow files: 4_v2-slack-thread.yml and 11_v2-slack-message-blocks.yml).

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:13`
- `.github/workflows/2-slack-notification.yml:13`
- `.github/workflows/3-slack-reaction.yml:14`
- `.github/workflows/3-slack-reaction.yml:26`
- `.github/workflows/4_v2-slack-thread.yml:16`
- `.github/workflows/4_v2-slack-thread.yml:29`
- `.github/workflows/4_v3-slack-thread.yml:14`
- `.github/workflows/4_v3-slack-thread.yml:27`
- `.github/workflows/5-slack-update-message.yml:14`
- `.github/workflows/5-slack-update-message.yml:27`
- `.github/workflows/6-slack-thread-with-broadcast.yml:14`
- `.github/workflows/6-slack-thread-with-broadcast.yml:27`
- `.github/workflows/7-slack-notification-multi-channel.yml:14`
- `.github/workflows/8-slack-notification-line-breaks.yml:14`
- `.github/workflows/8-slack-notification-line-breaks.yml:31`
- `.github/workflows/8-slack-notification-line-breaks.yml:42`
- `.github/workflows/9-slack-update-message-line-breaks.yml:15`
- `.github/workflows/9-slack-update-message-line-breaks.yml:26`
- `.github/workflows/9-slack-update-message-line-breaks.yml:42`
- `.github/workflows/9-slack-update-message-line-breaks.yml:57`
- `.github/workflows/10-slack-fake-build-updates.yml:14`
- `.github/workflows/10-slack-fake-build-updates.yml:33`
- `.github/workflows/11_v2-slack-message-blocks.yml:16`
- `.github/workflows/11_v3-slack-message-blocks.yml:14`
- `.github/workflows/12-slack-message-blocks-update.yml:14`
- `.github/workflows/12-slack-message-blocks-update.yml:32`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:14`
- `.github/workflows/14-slack-message-icon.yml:13`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 16 workflow files: (1) Pinned all `archive/github-actions-slack@master` references to SHA `433fef9978d3adae73168bfc9c5a7ce722780231` and all `@v2.11.0` references to SHA `b1d27920d1b9cbacbfe6732572d325baf93ae402`, preserving the original tag/branch as a comment. (2) Moved all `${{ steps.*.outputs.slack-result }}` and `${{ steps.*.outputs.slack-results }}` expressions out of `run:` shell strings into `env:` blocks (as `SLACK_RESULT` / `SLACK_RESULTS`), then referenced them as plain `$SLACK_RESULT` / `$SLACK_RESULTS` environment variables in the shell commands.

