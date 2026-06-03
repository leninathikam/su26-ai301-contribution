# Open Source Contribution Log

> CodePath AI301 — My contribution journey from issue selection to merged pull request.
> This is a living document. I update it at the end of every phase.

| | |
|---|---|
| **Name** | Lenin Goud Athikam |
| **GitHub** | @leninathikam |
| **Status** | ✅ Phase I Complete |
| **Project** | [stagewise-io/stagewise](https://github.com/stagewise-io/stagewise) — a developer browser with a built-in coding agent |
| **Issue** | [#929 — Use Claude Agents inside stagewise](https://github.com/stagewise-io/stagewise/issues/929) |

---

## Phase I — Issue Selection

### Issue
[stagewise-io/stagewise #929 — Use Claude Agents inside stagewise](https://github.com/stagewise-io/stagewise/issues/929)

### Project
[stagewise-io/stagewise](https://github.com/stagewise-io/stagewise) — an open-source, developer-focused browser with a coding agent built in. It lets developers select DOM elements in a real browser tab and have an AI agent implement code changes. Built as a TypeScript monorepo (React 19 + Electron, pnpm + Turborepo, Biome, Vitest).

### Why I Chose This Issue
Right now, if you already use Claude as your coding agent, you can't use it inside stagewise — you have to switch tools or lose the higher limits that come with your Claude subscription. This issue is about fixing that: letting people pick a Claude agent right from the agent dropdown and keep working in stagewise with the setup they already pay for.

I picked it for a few reasons. The project is mostly TypeScript and React, which I'm already comfortable with, so I can start contributing without a huge learning curve. At the same time, it pushes me into areas I want to learn — Electron and wiring up an external agent — which I'm excited to dig into with some AI help. And the issue itself looks like a good one to jump on: the maintainer is active, nobody's claimed it yet, there's no open PR, and the setup docs are solid.

One thing I noticed is that the maintainer is still deciding *how* to build this (SDK vs. CLI vs. an embedded terminal view). So my plan for Phase II is to check in with them first, agree on a direction, and start with a small, doable first piece instead of trying to ship the whole feature at once.

### Community Engagement
- [x] Commented on the issue expressing interest
- [x] Noted the issue on the cohort Google Sheet
- [x] Forked the project: https://github.com/leninathikam/stagewise

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
