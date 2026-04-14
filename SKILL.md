---
name: biweekly-comms
description: >-
  Generate and post bi-weekly program communications. Pulls data from Notion,
  Jira, Slack, and Google Drive via MCP, drafts in L7 PM at Meta style, and
  posts to #ml-comms-agent. Trigger: "run bi-weekly comms".
---

# Bi-Weekly Comms Agent

Fully autonomous agent that gathers live data from Notion, Jira, Slack, and
Google Drive via MCP, drafts executive-grade bi-weekly program updates in the
voice of a Meta L7 PM, and posts them to Slack. Zero manual steps after trigger.

## Prerequisites

Ensure these MCP servers are authenticated (call `mcp_auth` if tools are missing):
- **Notion** — `plugin-Notion-notion`
- **Atlassian** — `plugin-atlassian-atlassian` (cloud: `axilis.atlassian.net`)
- **Slack** — `user-slack`

## Execution

When the user says "run bi-weekly comms" (or similar), execute ALL steps below
without pausing for confirmation. Process all 5 focus areas end-to-end and post
results to `#ml-comms-agent` (channel ID: `C0ASVLY92UC`).

---

### Step 1: Read config

Read `~/biweekly-comms-agent/config.yaml`. Extract:
- `global.lookback_days` (default 14)
- `global.personal_channel` — target Slack channel ID for posting
- `slack_shared_channels` — cross-cutting channels to read for every area
- Each focus area's `display_name`, `prompt_context`, and `sources` (Notion pages,
  Jira projects, Slack channels, Google Drive search terms)

### Step 2: Gather data for ALL focus areas

For each focus area, pull from all 4 sources. Use parallel MCP calls where possible.

#### 2a. Notion pages

For each `sources.notion` entry, fetch the page:

```
CallMcpTool: plugin-Notion-notion / notion-fetch
  { "id": "<page_id>" }
```

#### 2b. Notion meeting notes

Search for recent meetings related to this focus area:

```
CallMcpTool: plugin-Notion-notion / notion-search
  {
    "query": "<display_name> meeting notes weekly sync",
    "query_type": "internal",
    "filters": { "created_date_range": { "start_date": "<lookback_days ago, YYYY-MM-DD>" } },
    "page_size": 10,
    "max_highlight_length": 200
  }
```

For results that are meeting notes, fetch them with `notion-fetch` to get the
AI-generated summary and action items. Meeting transcripts often contain
decisions, blockers, and context not found anywhere else.

#### 2c. Jira issues

For each `sources.jira` entry:

```
CallMcpTool: plugin-atlassian-atlassian / searchJiraIssuesUsingJql
  {
    "cloudId": "axilis.atlassian.net",
    "jql": "project = \"<project_key>\" AND status IN (\"Done\", \"In Review\", \"In Progress\") AND updated >= \"<lookback date YYYY-MM-DD>\" ORDER BY updated DESC",
    "maxResults": 50,
    "fields": ["summary", "status", "issuetype", "priority", "assignee", "updated", "labels", "fixVersions"],
    "responseContentFormat": "markdown"
  }
```

#### 2d. Slack channels

For each `sources.slack_channels` entry AND each `slack_shared_channels` entry:

```
CallMcpTool: user-slack / slack_read_channel
  {
    "channel_id": "<channel_id>",
    "limit": 50,
    "response_format": "concise"
  }
```

Also run a keyword search across all channels for this area:

```
CallMcpTool: user-slack / slack_search_public_and_private
  {
    "query": "<area keywords> after:<lookback date YYYY-MM-DD>",
    "limit": 15,
    "sort": "timestamp",
    "sort_dir": "desc",
    "include_context": true,
    "response_format": "concise"
  }
```

For important threads with substantive discussion, fetch the full thread:

```
CallMcpTool: user-slack / slack_read_thread
  { "channel_id": "<channel_id>", "message_ts": "<parent_ts>", "limit": 50 }
```

#### 2e. Google Drive (via Notion connected sources)

For each `sources.gdrive_search_terms` entry:

```
CallMcpTool: plugin-Notion-notion / notion-search
  {
    "query": "<search_term>",
    "query_type": "internal",
    "filters": { "created_date_range": { "start_date": "<lookback date YYYY-MM-DD>" } },
    "page_size": 5,
    "max_highlight_length": 300
  }
```

Include results where `type` is `"google-drive"` or URL points to `docs.google.com`.

### Step 3: Draft comms

For each focus area, synthesize ALL gathered source material into a bi-weekly
update following the L7 PM voice defined in [reference.md](reference.md).

**Key rules:**
- Lead with impact, not activity
- Quantify everything — attach metrics, deltas, or directional signals
- Name owners and teams explicitly
- State risks as blockers with specific asks, not vague "challenges"
- 300-500 words per area
- Use Slack mrkdwn: `*bold*` for headers/metrics/owners, `_italic_` for dates/caveats
- Bullets with `•` not `-`

**Section structure** (omit a section only if zero items apply):

```
*<One-line bold headline — the single most important thing this cycle>*

:rocket: *Shipped & Impact*
• <What landed>, <who it affects>, <measured or expected result>

:construction: *In Flight*
• <Workstream>: <what's happening>, <owner>, <expected land date>

:dart: *Key Decisions & Bets*
• <Strategic call made or trade-off accepted>

:rotating_light: *Risks & Asks*
• <What could slip> — ask: <specific action needed from named person/team>

:chart_with_upwards_trend: *Metrics Snapshot*
• <Metric>: <value> (<delta or direction>)
```

### Step 4: Post to Slack

Post each area's comms to `#ml-comms-agent` using Slack MCP:

```
CallMcpTool: user-slack / slack_send_message
  {
    "channel_id": "C0ASVLY92UC",
    "message": "<area display_name> Bi-Weekly Update\n\n<drafted comms>"
  }
```

Post all 5 areas as separate messages so each is readable independently.

After all posts complete, confirm to the user: "All 5 bi-weekly updates posted
to #ml-comms-agent."

---

## Quick reference

| Source | MCP Server | Tool |
|--------|-----------|------|
| Notion pages | `plugin-Notion-notion` | `notion-fetch` |
| Notion meetings | `plugin-Notion-notion` | `notion-search` + `notion-fetch` |
| Jira | `plugin-atlassian-atlassian` | `searchJiraIssuesUsingJql` |
| Slack (read) | `user-slack` | `slack_read_channel`, `slack_search_public_and_private`, `slack_read_thread` |
| Google Drive | `plugin-Notion-notion` | `notion-search` (connected sources) |
| Slack (post) | `user-slack` | `slack_send_message` |
