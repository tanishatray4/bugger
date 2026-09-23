# 🐞 Bugger — Bug → Better Ticket

A small front-end prototype that turns a messy, free-form bug description into a structured ticket — shown through two different lenses: an **Engineering View** (reproduction, technical detail) and a **Product View** (impact, prioritization).

> **Status: portfolio prototype / MVP.** This is a UX and product-thinking demo, not a production tool. There is no backend, no AI API call, and no persistence — see [How the "AI" works](#how-the-ai-works) below.

---

## The problem

Bug reports often arrive incomplete or ambiguous ("the API is broken, please fix"), which creates unnecessary back-and-forth between support, product, and engineering before the report is even actionable.

## The hypothesis

Structuring a bug report at the point of submission — and being explicit about what's still unknown, rather than quietly filling gaps with plausible-sounding assumptions — can reduce clarification cycles and improve handoff quality between teams.

## The MVP

Convert a free-form bug description into a structured ticket with two role-specific views, while explicitly flagging missing information instead of inventing it. The core rule the generation logic follows throughout:

> **Never fill a gap with a generic assumption.** If the reporter didn't say it, the ticket says "Not specified," "Not provided," or "Needs review" — never a plausible-sounding guess.

## Key product decisions

- **Two views, one dataset.** Product View and Engineering View read from the same ticket object. Editing a shared field (title, summary, expected/actual behavior, severity, priority) from either view updates both — there's no risk of the two getting out of sync.
- **Reproduction steps are extracted, not paraphrased.** A dedicated rule-based parser (`extractReproductionSteps`) only turns explicit user *actions* into steps ("click X", "upload Y"). Vague or non-actionable sentences ("please fix", "it's broken") are excluded rather than repeated back as fake steps. When no real action is described, the ticket says so plainly instead of manufacturing a plausible-looking checklist.
- **Severity/priority require evidence.** They default to "Needs review" and only escalate when the report gives a concrete signal (explicit critical/outage language, or a stated environment *plus* a stated frequency) — environment alone is not treated as sufficient evidence.
- **Quality score is transparent, not a black box.** It's a simple weighted checklist (problem described, timing, repro steps, expected behavior, exact error, environment, endpoint, impact, frequency) shown alongside the score, captioned as a measure of the *report's* completeness — not of AI accuracy.

## How the "AI" works

There is no LLM call in this prototype. `generateBugTicket()` is a deterministic, rule-based function (regex and sentence-level heuristics) that structures the report and flags gaps. It's written with a clearly separated function boundary so a real LLM API call could be substituted in without touching the UI layer — but for this MVP, keeping it rule-based makes the "never invent information" guarantee easy to verify and demo honestly.

## Product View vs. Engineering View

| Engineering View | Product View |
|---|---|
| Steps to Reproduce | Product Area |
| Expected / Actual Behavior | User / Business Impact |
| Environment, Error | Severity, Priority (editable) |
| Confirmed Information | Success Criteria |
| Information Needed | Open Questions |
| Acceptance Criteria | Confirmed Information |

Switching views is instant and never regenerates the ticket — both views render from the same in-memory object.

## Tech stack

- Plain HTML + vanilla JavaScript (no framework, no build step)
- [Tailwind CSS](https://tailwindcss.com) via the Play CDN
- Everything runs client-side; local component state only, no backend, no database, no auth

## Running locally

This is a single static HTML file — no install or build required.

```bash
# Option 1: just open it
open index.html          # macOS
start index.html         # Windows

# Option 2: serve it (recommended, avoids browser file:// restrictions)
npx serve .
```

Then visit the URL `serve` prints (typically `http://localhost:3000`).

## What this MVP deliberately does not include

Authentication, a database, real Jira/Zendesk/Slack integration, user accounts, billing, notifications, duplicate-bug detection, screenshot analysis, team management, or an analytics dashboard. These are noted as future possibilities, not built here — keeping the scope small was a deliberate product decision, not an oversight.

## Potential success metrics (conceptual only — not measured in this prototype)

- % of reports successfully converted into complete tickets
- Average ticket quality score
- Reduction in missing-information follow-ups
- Time from bug submission to engineering-ready ticket
- Copy/export rate

---

Built as a product-thinking and technical-writing portfolio prototype by Tanisha Ray.
