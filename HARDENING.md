<!-- markdownlint-disable -->

# Hardening Report: archive--github-actions-slack/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **archive--github-actions-slack/v3.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: Multiple workflow files contain `run:` steps that directly interpolate `${{ steps.*.outputs.slack-result }}` expressions into shell commands (e.g., `run: echo '${{ steps.send-message.outputs.slack-result }}'`). GitHub Actions interpolates `${{ }}` expressions before the shell executes, so even single-quoted strings are vulnerable — if the Slack API response contains shell metacharacters or newlines, they will be interpreted by the shell. The `steps.*.outputs.*` context is workflow-controllable and must not be interpolated directly in `run:` blocks.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:24`
- `.github/workflows/2-slack-notification.yml:18`
- `.github/workflows/3-slack-reaction.yml:24`
- `.github/workflows/3-slack-reaction.yml:35`
- `.github/workflows/4_v2-slack-thread.yml:24`
- `.github/workflows/4_v2-slack-thread.yml:38`
- `.github/workflows/4_v3-slack-thread.yml:23`
- `.github/workflows/4_v3-slack-thread.yml:37`
- `.github/workflows/5-slack-update-message.yml:23`
- `.github/workflows/5-slack-update-message.yml:35`
- `.github/workflows/6-slack-thread-with-broadcast.yml:24`
- `.github/workflows/6-slack-thread-with-broadcast.yml:36`
- `.github/workflows/7-slack-notification-multi-channel.yml:18`
- `.github/workflows/7-slack-notification-multi-channel.yml:21`
- `.github/workflows/8-slack-notification-line-breaks.yml:26`
- `.github/workflows/8-slack-notification-line-breaks.yml:37`
- `.github/workflows/8-slack-notification-line-breaks.yml:48`
- `.github/workflows/9-slack-update-message-line-breaks.yml:20`
- `.github/workflows/9-slack-update-message-line-breaks.yml:37`
- `.github/workflows/9-slack-update-message-line-breaks.yml:50`
- `.github/workflows/9-slack-update-message-line-breaks.yml:67`
- `.github/workflows/11_v2-slack-message-blocks.yml:55`
- `.github/workflows/11_v3-slack-message-blocks.yml:52`
- `.github/workflows/12-slack-message-blocks-update.yml:28`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:44`
- `.github/workflows/15-slack-upload-file.yml:22`

### unpinned-uses (severity: high)

All 17 workflow files reference actions using mutable branch or version tags instead of pinned full-length SHA commit hashes. Unpinned references are vulnerable to supply-chain attacks where a compromised or malicious update to the referenced tag/branch would automatically affect all runs. Failing references include: `archive/github-actions-slack@master` (used in 15 workflow files), `archive/github-actions-slack@v2.11.0` (used in 11_v2 and 4_v2), and `actions/checkout@v4` (used in 15-slack-upload-file.yml). All should be pinned to a full 40-character commit SHA.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:14`
- `.github/workflows/2-slack-notification.yml:13`
- `.github/workflows/3-slack-reaction.yml:14`
- `.github/workflows/3-slack-reaction.yml:28`
- `.github/workflows/4_v2-slack-thread.yml:15`
- `.github/workflows/4_v2-slack-thread.yml:30`
- `.github/workflows/4_v3-slack-thread.yml:14`
- `.github/workflows/4_v3-slack-thread.yml:28`
- `.github/workflows/5-slack-update-message.yml:14`
- `.github/workflows/5-slack-update-message.yml:27`
- `.github/workflows/6-slack-thread-with-broadcast.yml:15`
- `.github/workflows/6-slack-thread-with-broadcast.yml:28`
- `.github/workflows/7-slack-notification-multi-channel.yml:14`
- `.github/workflows/8-slack-notification-line-breaks.yml:14`
- `.github/workflows/8-slack-notification-line-breaks.yml:30`
- `.github/workflows/8-slack-notification-line-breaks.yml:42`
- `.github/workflows/9-slack-update-message-line-breaks.yml:14`
- `.github/workflows/9-slack-update-message-line-breaks.yml:27`
- `.github/workflows/9-slack-update-message-line-breaks.yml:43`
- `.github/workflows/9-slack-update-message-line-breaks.yml:58`
- `.github/workflows/10-slack-fake-build-updates.yml:14`
- `.github/workflows/10-slack-fake-build-updates.yml:31`
- `.github/workflows/11_v2-slack-message-blocks.yml:16`
- `.github/workflows/11_v3-slack-message-blocks.yml:14`
- `.github/workflows/12-slack-message-blocks-update.yml:14`
- `.github/workflows/12-slack-message-blocks-update.yml:33`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:14`
- `.github/workflows/14-slack-message-icon.yml:14`
- `.github/workflows/15-slack-upload-file.yml:13`
- `.github/workflows/15-slack-upload-file.yml:17`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed all 17 workflow files:

**unpinned-uses**: Pinned all action references to full commit SHAs:
- `archive/github-actions-slack@master` → `@433fef9978d3adae73168bfc9c5a7ce722780231 # master` (15 workflow files)
- `archive/github-actions-slack@v2.11.0` → `@b1d27920d1b9cbacbfe6732572d325baf93ae402 # v2.11.0` (files 4_v2 and 11_v2)
- `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262 # v4` (file 15)

**script-injection**: Moved all `${{ steps.*.outputs.slack-result }}` and `${{ steps.*.outputs.slack-results }}` expressions out of `run:` shell strings into `env:` blocks, referencing them as plain environment variables (`$SLACK_RESULT`, `$SLACK_RESULTS`) in the shell commands. This affects 26 locations across files 1, 2, 3, 4_v2, 4_v3, 5, 6, 7, 8, 9, 11_v2, 11_v3, 12, 13, and 15.

