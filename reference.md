# L7 PM at Meta — Voice & Style Guide

## Core Principles

A Meta L7 PM owns outcomes, not tasks. Every sentence should reflect this.

### Voice
- **Direct, confident, low-ego.** No filler, no hedging, no "I/we are excited to…"
- **Impact-first.** Lead every item with what changed for users or the business, then what was built.
- **Quantify ruthlessly.** Attach metrics, deltas, or directional signals to every claim.
- **Name owners.** Accountability is visible. "Shipped by *Risk Eng (Sarah)*" not "the team shipped".
- **State risks plainly.** A blocker is not "a challenge"; it is "blocked on X, ETA slips Y days without resolution by Z".

### Anti-patterns (never do these)
- Generic status language: "making good progress", "on track", "moving forward"
- Listing tasks without outcomes: "updated the config", "fixed a bug"
- Burying the lede: the most important thing goes in the first bullet of each section
- Passive voice for ownership: "it was decided" → "*Risk leads* decided"
- Emoji-heavy headers or filler emoji

## Formatting (Slack mrkdwn)

- `*bold*` for section headers, key metrics, team/owner names
- `_italic_` for dates, caveats, secondary context
- Bullet points with `•` (not `-`)
- 300-500 words total. Brevity is a feature.
- No emoji in section headers. At most 1-2 emoji as visual anchors in section labels.

## Section Templates

### Headline
One bold line. The single most important thing this cycle.

> *Fraud loss rate dropped 18bps after v2.3 model deploy — largest single-cycle improvement in H1.*

### Shipped & Impact
Each bullet: what shipped → who's affected → measured result.

> • *ACH fraud model v2.3* live in prod (Mar 28). False-positive rate down 12%, saving ~$340K/mo in ops review cost. Shipped by *Risk ML (Amir)*.
> • *Donor re-engagement email flow* launched to 42K lapsed donors. Early signal: 8.2% reactivation rate vs 3.1% control. *Growth Eng (Priya)*.

### In Flight
Top 2-4 workstreams. Each: what, owner, land date.

> • *LTV scoring v3 migration*: backfill running, 68% complete. Owner: *Data Eng (Jake)*. ETA: Apr 18.
> • *Jira → Advanced Plans rollout*: pilot with 3 squads this sprint. Owner: *PM Tools (Lisa)*. GA target: May 2.

### Key Decisions & Bets
Strategic calls made, trade-offs accepted.

> • Bet: Doubling down on ACH over wire for SMB segment — higher margin, lower fraud surface. Trade-off: slower settlement (T+2 vs T+0). Aligned with *Payments leadership*.

### Risks & Asks
Specific blocker + specific ask + named owner.

> • *Brazil KYC vendor* contract renewal stalled — Legal review needed by Apr 15 or we lose 30-day grace window. *Ask: Legal (David)* to prioritize review.

### Metrics Snapshot
2-4 headline numbers. Always include direction (↑↓→) or delta.

> • *Fraud loss rate*: 22bps → 4bps (↓18bps)
> • *Donor retention (D30)*: 61.3% (↑2.1pp vs prior cycle)
> • *Ticket deflection*: 34.7% (→ flat, targeting 40% by EOQ)
