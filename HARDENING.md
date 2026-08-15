<!-- markdownlint-disable -->

# Hardening Report: archive--github-actions-slack/v2.11.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **archive--github-actions-slack/v2.11.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All 14 workflow files reference `archive/github-actions-slack@master`, which is a mutable branch ref rather than a pinned 40-character commit SHA. This exposes the workflows to supply-chain attacks if the upstream repository is compromised or the branch is force-pushed.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:14`
- `.github/workflows/2-slack-notification.yml:13`
- `.github/workflows/3-slack-reaction.yml:13`
- `.github/workflows/4-slack-thread.yml:13`
- `.github/workflows/5-slack-update-message.yml:13`
- `.github/workflows/6-slack-thread-with-broadcast.yml:13`
- `.github/workflows/7-slack-notification-multi-channel.yml:14`
- `.github/workflows/8-slack-notification-line-breaks.yml:14`
- `.github/workflows/9-slack-update-message-line-breaks.yml:13`
- `.github/workflows/10-slack-fake-build-updates.yml:14`
- `.github/workflows/11-slack-message-blocks.yml:14`
- `.github/workflows/12-slack-message-blocks-update.yml:14`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:14`
- `.github/workflows/14-slack-message-icon.yml:13`

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ steps.*.outputs.slack-result }}` (and `slack-results`) expressions inside shell commands. GitHub Actions performs template substitution before the shell executes, so if the Slack API response contains shell metacharacters, they will be interpreted by the shell. Example offending line: `run: echo '${{ steps.send-message.outputs.slack-result }}'`. Single-quoting the shell string does not prevent GitHub Actions from substituting the expression value before the shell sees it. All occurrences should be moved to an `env:` variable and the variable double-quoted in the shell.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:24`
- `.github/workflows/2-slack-notification.yml:18`
- `.github/workflows/3-slack-reaction.yml:18`
- `.github/workflows/3-slack-reaction.yml:28`
- `.github/workflows/4-slack-thread.yml:18`
- `.github/workflows/4-slack-thread.yml:28`
- `.github/workflows/5-slack-update-message.yml:18`
- `.github/workflows/5-slack-update-message.yml:28`
- `.github/workflows/6-slack-thread-with-broadcast.yml:18`
- `.github/workflows/6-slack-thread-with-broadcast.yml:28`
- `.github/workflows/7-slack-notification-multi-channel.yml:23`
- `.github/workflows/7-slack-notification-multi-channel.yml:26`
- `.github/workflows/8-slack-notification-line-breaks.yml:21`
- `.github/workflows/8-slack-notification-line-breaks.yml:31`
- `.github/workflows/8-slack-notification-line-breaks.yml:41`
- `.github/workflows/9-slack-update-message-line-breaks.yml:18`
- `.github/workflows/9-slack-update-message-line-breaks.yml:30`
- `.github/workflows/9-slack-update-message-line-breaks.yml:42`
- `.github/workflows/9-slack-update-message-line-breaks.yml:55`
- `.github/workflows/11-slack-message-blocks.yml:51`
- `.github/workflows/12-slack-message-blocks-update.yml:21`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 14 workflow files: (1) Pinned all `archive/github-actions-slack@master` references to commit SHA `433fef9978d3adae73168bfc9c5a7ce722780231` with `# master` comment. (2) Fixed all 23 script-injection instances by moving `${{ steps.*.outputs.slack-result }}` and `${{ steps.*.outputs.slack-results }}` expressions out of `run:` shell strings into `env:` blocks, then referencing them as `$SLACK_RESULT` / `$SLACK_RESULTS` environment variables in the shell. The `${{ }}` expressions that remain in `with:` (action inputs) and `env:` blocks are safe and expected.

