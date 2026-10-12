# Claude task prompt (replaces automation-prompt.md)

Stored in two Claude scheduled tasks. Neither needs copy-paste into a third-party tool.

## ml-comms-slack-listener (cron `*/5 * * * *`)

1. Read the last 15 messages of #ml-comms-agent (C0ASVLY92UC).
2. Find the newest message from Manav that starts with "run bi-weekly comms" (case-insensitive, optional leading @Claude, optional period or "dry run"). Messages carrying "Sent using" are never triggers.
3. If none, or the trigger already has a "Run started" or "Run complete" thread reply, or a completion message exists after it: stop silently.
4. Reply in the thread: ":eyes: Run started ..."
5. Execute `SKILL.md` steps 0 and 2 to 6 (skip the duplicate guard and the re-arm). "dry run" posts drafts in the thread only.
6. Reply in the thread: ":white_check_mark: Run complete ..." or the exact failure.

## ml-biweekly-comms-agent (one-time fireAt, re-arms itself every 14 days)

Runs `SKILL.md` steps 0 to 7 in full, including the duplicate guard and the re-arm.

The full stored prompts live in the Claude app under Scheduled, in the task folders `ml-comms-slack-listener` and `ml-biweekly-comms-agent`.
