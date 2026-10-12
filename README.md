# Bi-Weekly ML Comms Agent (Claude setup)

Posts five area updates (Engagement, Risk, LTV, CS AI, Gaming) to #ml-comms-agent (C0ASVLY92UC) and logs them to the Notion ML Comms Log. This replaces the earlier Cursor automation.

## How it runs

| Trigger | Mechanism |
|---|---|
| Every 2 weeks | Claude scheduled task `ml-biweekly-comms-agent`. It re-arms itself at the end of each run (one-time `fireAt`, 14 days out, 10:00 London). |
| From Slack | Claude scheduled task `ml-comms-slack-listener`. Polls #ml-comms-agent every 5 minutes. |

## Calling it from Slack

Post this in #ml-comms-agent as yourself (Manav):

    run bi-weekly comms

Optional suffixes: a period (`run bi-weekly comms Oct 8-Oct 22`) or `dry run` (drafts go into a thread under your message, nothing is posted to the channel and nothing is logged to Notion).

What happens: within about 5 minutes the listener replies in your thread with "Run started", runs the full workflow (see `SKILL.md`), posts the 5 area updates and the completion line, logs to Notion, then replies "Run complete" in the thread. A message that carries the "Sent using" footer is never treated as a trigger, and a trigger with a "Run started" reply or a later completion message is ignored, so a run cannot fire twice.

Limits to know:
- Claude scheduled tasks run while the Claude desktop app is open. If it is closed, the run happens on next launch.
- This is polling, not a Slack slash command or @mention bot. A true instant trigger needs a Slack app with Events API and a small hosted webhook that starts a Claude run. Not set up here.
- Auto mode: the listener runs without per-action approval. Switch to "Manually approve" in the task settings if you want a gate.

## Files

- `SKILL.md`: the workflow (replaces the Cursor-flavoured version).
- `claude-task-prompt.md`: the self-contained prompt stored in the Claude scheduled tasks (replaces `automation-prompt.md`).
- `config.yaml`: unchanged focus-area inventory, only the `mcp:` block changes (see below).
- `reference.md`: unchanged voice guide, except the hygiene rules in `SKILL.md` now override it (no em or en dashes).

### config.yaml, replace the `mcp:` block with

```yaml
mcp:
  atlassian_cloud_id: "axilis.atlassian.net"
  runtime: "claude-scheduled-tasks"
  # Claude resolves Slack, Notion and Atlassian connectors by capability,
  # not by Cursor server names.
notion:
  comms_log_page: "348032f852c58101bd6cd4851f96e653"
  comms_log_data_source: "collection://831851a7-0f9e-42d8-82f7-546592374b3f"
risk_free_source: "https://claude.ai/artifact/DdPJrYRKtt3fBSKFtw9TUn"
```

Also add the channels the research found missing from the seed list: #ltv-ml-internal (C0BM75CHP08), #cs-ai-agent--general (C0AFLPEDBN1), #risk-cross-ml (replaces the old #risk-cross-blue name), #gaming-rcmd--internal.

## Ownership and governance

Owner: Manav. Every post carries the footer `*Sent using* Claude (scheduled task, owner: Manav)`. Pause the agent by disabling the two scheduled tasks. The agent posts only to #ml-comms-agent and never DMs anyone.
