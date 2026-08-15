<!-- markdownlint-disable -->

# Hardening Report: archive--github-actions-slack/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **archive--github-actions-slack/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All 14 workflow files reference `archive/github-actions-slack@master` — a mutable branch name rather than a pinned 40-character commit SHA. If the upstream repository is compromised or the branch is force-pushed, the action will silently execute attacker-controlled code. Each file should pin to a full SHA, e.g. `archive/github-actions-slack@<40-hex-sha> # master`.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:13`
- `.github/workflows/2-slack-notification.yml:13`
- `.github/workflows/3-slack-reaction.yml:13`
- `.github/workflows/4-slack-thread.yml:13`
- `.github/workflows/5-slack-update-message.yml:13`
- `.github/workflows/6-slack-thread-with-broadcast.yml:13`
- `.github/workflows/7-slack-notification-multi-channel.yml:14`
- `.github/workflows/8-slack-notification-line-breaks.yml:13`
- `.github/workflows/9-slack-update-message-line-breaks.yml:13`
- `.github/workflows/10-slack-fake-build-updates.yml:13`
- `.github/workflows/11-slack-message-blocks.yml:13`
- `.github/workflows/12-slack-message-blocks-update.yml:13`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:13`
- `.github/workflows/14-slack-message-icon.yml:13`

### script-injection (severity: high)

Multiple `run:` steps directly interpolate `${{ steps.*.outputs.* }}` expressions into shell commands, violating sub-rule (a). GitHub Actions performs template substitution before the shell executes, so a value containing shell metacharacters (`;`, `|`, `$(...)`, etc.) in a step output can break out of the echo command and execute arbitrary code. The typical pattern is `run: echo '${{ steps.send-message.outputs.slack-result }}'`. Even single-quoting does not protect against this because the substitution happens at the YAML/Actions layer before the shell sees the string. The fix is to route the value through an environment variable and double-quote the shell expansion: `env: RESULT: ${{ steps.send-message.outputs.slack-result }}` then `run: echo "$RESULT"`.

Locations:

- `.github/workflows/1-slack-notification-with-optional-parameters.yml:21`
- `.github/workflows/2-slack-notification.yml:21`
- `.github/workflows/3-slack-reaction.yml:21`
- `.github/workflows/3-slack-reaction.yml:33`
- `.github/workflows/4-slack-thread.yml:21`
- `.github/workflows/4-slack-thread.yml:33`
- `.github/workflows/5-slack-update-message.yml:21`
- `.github/workflows/5-slack-update-message.yml:33`
- `.github/workflows/6-slack-thread-with-broadcast.yml:21`
- `.github/workflows/6-slack-thread-with-broadcast.yml:33`
- `.github/workflows/7-slack-notification-multi-channel.yml:23`
- `.github/workflows/7-slack-notification-multi-channel.yml:26`
- `.github/workflows/8-slack-notification-line-breaks.yml:21`
- `.github/workflows/8-slack-notification-line-breaks.yml:33`
- `.github/workflows/8-slack-notification-line-breaks.yml:45`
- `.github/workflows/9-slack-update-message-line-breaks.yml:21`
- `.github/workflows/9-slack-update-message-line-breaks.yml:33`
- `.github/workflows/9-slack-update-message-line-breaks.yml:49`
- `.github/workflows/9-slack-update-message-line-breaks.yml:65`
- `.github/workflows/11-slack-message-blocks.yml:49`
- `.github/workflows/12-slack-message-blocks-update.yml:21`
- `.github/workflows/13-slack-message-blocks-backward-compatibility.yml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 14 workflow files: (1) Pinned all `archive/github-actions-slack@master` references to the full SHA `433fef9978d3adae73168bfc9c5a7ce722780231` with a `# master` comment for readability. (2) Fixed all 22 script-injection locations by moving `${{ steps.*.outputs.* }}` expressions into `env:` blocks and referencing them as double-quoted shell variables in `run:` scripts. Files 10 and 14 only needed the unpinned-uses fix (no echo of step outputs). All other files needed both fixes.

