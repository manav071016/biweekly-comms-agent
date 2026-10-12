---
name: biweekly-comms
description: >-
  Generate and post bi-weekly ML program communications for Engagement, Risk,
  LTV, CS AI and Gaming. Researches Slack, Jira, Notion and meeting notes,
  drafts in an L7 PM voice, posts to #ml-comms-agent and logs to Notion.
  Trigger: "run bi-weekly comms" in #ml-comms-agent.
---

# Bi-Weekly Comms Agent (Claude)

Runs as Claude scheduled tasks. Trigger paths: the 2-week schedule (`ml-biweekly-comms-agent`) or a Slack message "run bi-weekly comms" picked up by `ml-comms-slack-listener` (polls every 5 minutes).

## Hard rules

- No em dashes or en dashes anywhere. No AI-tell phrasing. Straight quotes. Footer on every post: `*Sent using* Claude (scheduled task, owner: Manav)`.
- Post only to #ml-comms-agent (C0ASVLY92UC). Never DM anyone.
- Unverified numbers are omitted or tagged [verify]. No individual player data.
- Risk-Free impact only from the Risk Free Model Monitor (https://claude.ai/artifact/DdPJrYRKtt3fBSKFtw9TUn): quote link, data-as-of date, market, model version, metric. Cohort outcome since setCore is not causal lift. Brazil stays separate from RO/GR/PL.
- Verify before repeating: Brazil ML models were switched off 5 Oct 2026 except CS AI; Romania and Greece are the live focus; Brazil player-ID restriction deadline is 20 Oct.

## Steps

0. Load the `10x-pm` and `pm-brain` skills. Read the 10x-pm playbook for channels, Jira structure and rosters.
1. Guard: find the latest "All 5 bi-weekly updates posted (<period>)" message. If under 10 days old and the run was not triggered from Slack, post nothing and only re-arm. A Slack trigger bypasses the guard. Period = previous edition end date through today (or the period named in the trigger).
2. Read the previous edition's five posts. Reuse the structure. Carry forward every Blocker, Ask and Experiment and say whether it closed, moved, slipped or went silent. Never assume closure without evidence.
3. Research each area in parallel (one read-only subagent per area): area Slack channels plus #ml-product-leads (C09CXJX8B9B), #ml-tech-leads (C092KAVP953) and new #tmp-* channels; Jira projects AM and MLOP (updated and Done in period); Notion new pages and meeting notes. Tag evidence: "(PR merged)", "(Jira Done; no associated code change detected)", "(meeting-validated)". Omit "(PR merged)" if GitHub is unavailable. Rank: 4 sources Impact Spotlight, 3 What Shipped, 2 Experiments, 1 omit unless nothing else.
4. Post format, in order Engagement, Risk, LTV, CS AI, Gaming:

```
:newspaper: *TIER 2 Stakeholder Update*
<emoji> *<Area> Bi-Weekly Update <Mon D>-<Mon D>*
:trophy: *Impact Spotlight*
:package: *What Shipped*
:microscope: *Experiments in Flight*
:construction: *Blockers & Asks*
:brain: *Did You Know?*
*Sent using* Claude (scheduled task, owner: Manav)
```

Emoji: Engagement :dart:, Risk :shield:, LTV :moneybag:, CS AI :robot_face:, Gaming :video_game:. Every ask has a named owner and a date. Call out missed prior asks plainly. Flag impact not attributable to ML and missing instrumentation.
5. Post the five messages, then ":white_check_mark: All 5 bi-weekly updates posted (<period>) and logged to Notion." with the footer.
6. Log one page to the ML Comms Log data source (collection://831851a7-0f9e-42d8-82f7-546592374b3f): Edition, Date, Period, Tier "Tier 2 — Stakeholder Update", Audience "Stakeholders", Areas Covered all five, Status by judgment, content = the five posts.
7. Scheduled runs only: re-arm with update_scheduled_task, fireAt = 14 days out at 10:00 London (+01:00 BST, +00:00 after 25 Oct 2026). Slack-triggered runs skip this step.

## Slack trigger protocol

Trigger: a message from Manav starting with "run bi-weekly comms" (optional period or "dry run"). The listener replies "Run started" in the thread, runs steps 0 and 2 to 6, then replies "Run complete". Dry run posts drafts in the thread only.
