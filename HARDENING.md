<!-- markdownlint-disable -->

# Hardening Report: archive--github-actions-slack/v2.10.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **archive--github-actions-slack/v2.10.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All 14 workflow files reference `archive/github-actions-slack@master`, which is a mutable branch name rather than an immutable 40-character commit SHA. This means the action code can change at any time without notice, enabling supply-chain attacks. Every `uses:` line should be pinned to a full SHA (e.g., `archive/github-actions-slack@<40-hex-char-sha> # master`).

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:13`
- `.github/workflows/2-slack-notification.yml:12`
- `.github/workflows/3-slack-reaction.yml:13`
- `.github/workflows/3-slack-reaction.yml:25`
- `.github/workflows/4-slack-thread.yml:13`
- `.github/workflows/4-slack-thread.yml:25`
- `.github/workflows/5-slack-update-message.yml:13`
- `.github/workflows/5-slack-update-message.yml:25`
- `.github/workflows/6-slack-thread-with-broadcast.yml:13`
- `.github/workflows/6-slack-thread-with-broadcast.yml:25`
- `.github/workflows/7-slack-notification-multi-channel.yml:12`
- `.github/workflows/8-slack-notification-line-breaks.yml:13`
- `.github/workflows/8-slack-notification-line-breaks.yml:30`
- `.github/workflows/8-slack-notification-line-breaks.yml:40`
- `.github/workflows/9-slack-update-message-line-breaks.yml:13`
- `.github/workflows/9-slack-update-message-line-breaks.yml:26`
- `.github/workflows/9-slack-update-message-line-breaks.yml:43`
- `.github/workflows/9-slack-update-message-line-breaks.yml:57`
- `.github/workflows/10-slack-fake-build-updates.yml:13`
- `.github/workflows/10-slack-fake-build-updates.yml:31`
- `.github/workflows/11-slack-message-blocks.yml:13`
- `.github/workflows/12-slack-message-blocks-update.yml:13`
- `.github/workflows/12-slack-message-blocks-update.yml:32`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:13`
- `.github/workflows/14-slack-message-icon.yml:13`

### missing-permissions (severity: medium)

None of the 14 workflow files define a top-level `permissions:` block, and no job within any of these files defines job-level `permissions:` either. Without explicit permissions, workflows run with the default (often broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:1`
- `.github/workflows/2-slack-notification.yml:1`
- `.github/workflows/3-slack-reaction.yml:1`
- `.github/workflows/4-slack-thread.yml:1`
- `.github/workflows/5-slack-update-message.yml:1`
- `.github/workflows/6-slack-thread-with-broadcast.yml:1`
- `.github/workflows/7-slack-notification-multi-channel.yml:1`
- `.github/workflows/8-slack-notification-line-breaks.yml:1`
- `.github/workflows/9-slack-update-message-line-breaks.yml:1`
- `.github/workflows/10-slack-fake-build-updates.yml:1`
- `.github/workflows/11-slack-message-blocks.yml:1`
- `.github/workflows/12-slack-message-blocks-update.yml:1`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:1`
- `.github/workflows/14-slack-message-icon.yml:1`

### script-injection (severity: high)

Multiple `run:` steps directly interpolate `${{ steps.*.outputs.* }}` expressions inside shell commands (sub-rule a). For example: `run: echo '${{ steps.send-message.outputs.slack-result }}'`. Even though the value is single-quoted in the shell, the `${{ ... }}` expression is substituted by the Actions runner before the shell ever sees it, meaning a malicious value in the step output could break out of the single-quote context and inject arbitrary shell commands. The fix is to pass the value via an `env:` variable and reference it as `"$ENV_VAR"` in the shell.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:22`
- `.github/workflows/2-slack-notification.yml:21`
- `.github/workflows/3-slack-reaction.yml:21`
- `.github/workflows/3-slack-reaction.yml:33`
- `.github/workflows/4-slack-thread.yml:22`
- `.github/workflows/4-slack-thread.yml:34`
- `.github/workflows/5-slack-update-message.yml:22`
- `.github/workflows/5-slack-update-message.yml:34`
- `.github/workflows/6-slack-thread-with-broadcast.yml:22`
- `.github/workflows/6-slack-thread-with-broadcast.yml:34`
- `.github/workflows/7-slack-notification-multi-channel.yml:21`
- `.github/workflows/7-slack-notification-multi-channel.yml:24`
- `.github/workflows/8-slack-notification-line-breaks.yml:24`
- `.github/workflows/8-slack-notification-line-breaks.yml:33`
- `.github/workflows/8-slack-notification-line-breaks.yml:42`
- `.github/workflows/9-slack-update-message-line-breaks.yml:22`
- `.github/workflows/9-slack-update-message-line-breaks.yml:35`
- `.github/workflows/9-slack-update-message-line-breaks.yml:49`
- `.github/workflows/9-slack-update-message-line-breaks.yml:62`
- `.github/workflows/11-slack-message-blocks.yml:47`
- `.github/workflows/12-slack-message-blocks-update.yml:29`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all 14 workflow files under .github/workflows/:
1. unpinned-uses: Pinned all 'archive/github-actions-slack@master' references to the full SHA '433fef9978d3adae73168bfc9c5a7ce722780231' with '# master' comment for readability.
2. missing-permissions: Added 'permissions: {}' top-level block to all 14 workflow files (these workflows only call Slack APIs via secrets, requiring no GitHub token permissions).
3. script-injection: Moved all '${{ steps.*.outputs.* }}' expressions out of 'run:' shell strings into 'env:' blocks, referencing them as double-quoted environment variables (e.g., SLACK_RESULT, SLACK_RESULTS) in the shell commands. This covers all 23 flagged locations across files 1-9, 11, 12, and 13.

