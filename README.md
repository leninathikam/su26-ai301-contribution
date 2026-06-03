# Open Source Contribution Log

> CodePath AI301 — My contribution journey from issue selection to merged pull request.
> This is a living document. I update it at the end of every phase.

| | |
|---|---|
| **Name** | Lenin Goud Athikam |
| **GitHub** | @leninathikam |
| **Status** | 🟡 Phase I — Issue Selection |
| **Project** | [stagewise-io/stagewise](https://github.com/stagewise-io/stagewise) — a developer browser with a built-in coding agent |
| **Issue** | [#929 — Use Claude Agents inside stagewise](https://github.com/stagewise-io/stagewise/issues/929) |

---

## Phase I — Issue Selection

### Issue
[stagewise-io/stagewise #929 — Use Claude Agents inside stagewise](https://github.com/stagewise-io/stagewise/issues/929)

### Project
[stagewise-io/stagewise](https://github.com/stagewise-io/stagewise) — an open-source, developer-focused browser with a coding agent built in. It lets developers select DOM elements in a real browser tab and have an AI agent implement code changes. Built as a TypeScript monorepo (React 19 + Electron, pnpm + Turborepo, Biome, Vitest).

### Why I Chose This Issue
I chose issue #929, "Use Claude Agents inside stagewise," because today stagewise users can't bring an Anthropic/Claude agent they already use into the app — so they either switch tools or give up the higher usage limits of their existing Claude subscription. Fixing this means a user can pick a Claude agent from the agent-creation dropdown and keep working in stagewise with the agent and subscription they already rely on, which improves adoption and day-to-day workflow.

I'm interested in this because:
1. The codebase is primarily TypeScript and React 19, which match my current skills, so I can be productive in the UI and integration layers right away.
2. It's a great learning goal for me: I want to get hands-on with Electron (desktop main/preload processes) and with integrating an external coding-agent over stdio/SDK — areas I can ramp up on with AI assistance.
3. The maintainer (@glenntws) is active (commented within the last few weeks), the issue is unassigned with no open PR, and the project has strong setup docs (CONTRIBUTING.md + CLAUDE.md), so I can get the project running and get feedback quickly.

**What "fixed" looks like (my current understanding):** a user can select a Claude agent alongside the existing first-class stagewise agents and interact with it inside the app. Because the maintainer is still weighing approaches (SDK vs. CLI/`-p` vs. an embedded terminal view to preserve subscription limits), my first step in Phase II is to confirm the intended direction with the maintainer and scope a small, achievable first slice rather than building the whole feature at once.

### Issue Selection Checklist
<!-- Score yourself on the 6 checks. Keep it 4/6 or higher. -->
| Check | Pass? | Notes |
|---|---|---|
| 1. I understand the problem | ✅ | Let users run a Claude agent (with their own subscription) inside stagewise. |
| 2. Scope fits 3–4 weeks | ⚠️ | It's a multi-part `Feature`; I'll scope a smaller first slice with the maintainer in Phase II. |
| 3. Matches my skills (or learnable with AI) | ✅ | Strong on TypeScript + React; Electron + agent-stdio is my learn-with-AI area. |
| 4. Active and claimable (unassigned, no open PR) | ⚠️ | Unassigned, no open PR, maintainer active recently — but labeled `under investigation` and approach not yet settled. |
| 5. Helpful context (maintainer comments, code links) | ✅ | Maintainer left implementation notes + a follow-up comment. |
| 6. Clear setup docs (README / CONTRIBUTING) | ✅ | CONTRIBUTING.md + CLAUDE.md; `pnpm install && pnpm dev`. |

**Score:** 4 / 6 (with two noted concerns I'll de-risk by confirming scope/approach with the maintainer before building)

### Community Engagement
- [ ] Commented on the issue expressing interest
- [ ] Noted the issue on the cohort Google Sheet
- [ ] Forked the project: https://github.com/leninathikam/stagewise

---

## Phase II — Reproduce & Plan

### Understanding the Issue
_In my own words, what is actually being asked for?_

### Reproduction Process
_Steps I took to set up the project locally and reproduce the problem. Include commands, OS, and what I observed._

### Solution Approach
_My planned fix. Which files/modules are involved? What changes will I make and why?_

---

## Phase III — Build & Test

### Implementation Notes
_What I built, decisions I made, and anything tricky I had to work through._

### Testing Strategy
_How I verified the fix: tests added/run, manual verification steps, edge cases covered._

---

## Phase IV — Pull Request

### Pull Request
_Link to my submitted PR._

### Summary
_What the PR changes and how it resolves the issue._

### Maintainer Feedback Log
<!-- Track review comments and how I responded. -->
| Date | Feedback | My response / change |
|---|---|---|
| | | |
