# Open Source Contribution Log

> CodePath AI301: My contribution journey from issue selection to merged pull request.
> This is a living document. I update it at the end of every phase.

| | |
|---|---|
| **Name** | Lenin Goud Athikam |
| **GitHub** | [@leninathikam](https://github.com/leninathikam) |
| **Status** | Phase IV Complete (merged) |
| **Project** | [cpinitiative/usaco-guide](https://github.com/cpinitiative/usaco-guide) — free competitive programming study guide |
| **Issue** | [#6140: Incorrect KMP solution for Baltic OI 2019 Necklace](https://github.com/cpinitiative/usaco-guide/issues/6140) |
| **PR** | [#6265: Fix incorrect KMP solution for Baltic OI 2019 Necklace](https://github.com/cpinitiative/usaco-guide/pull/6265) |
| **Fork** | [leninathikam/usaco-guide](https://github.com/leninathikam/usaco-guide) |

---

## Phase I: Issue Selection

### Issue

🔗 **https://github.com/cpinitiative/usaco-guide/issues/6140**

| | |
|---|---|
| **Repository** | [cpinitiative/usaco-guide](https://github.com/cpinitiative/usaco-guide) |
| **Organization** | Competitive Programming Initiative |
| **Languages** | MDX / Markdown content, C++ solution code, Docusaurus site (TypeScript/React) |
| **Labels** | `bug`, `good first issue`, `content` |
| **Fork** | https://github.com/leninathikam/usaco-guide |

### Project

[USACO Guide](https://github.com/cpinitiative/usaco-guide) is an actively maintained free study guide that takes students from USACO Bronze to Platinum and beyond. Problem solutions live in MDX files under `solutions/`, and the site is built with Docusaurus. Setup and contribution docs are usable: the repo README plus the [Contributing module](https://usaco.guide/general/contributing) explain how to fork, edit content, and open a PR. I forked the project to my account before starting work: https://github.com/leninathikam/usaco-guide

### Problem Summary

Solution 2 on the [Baltic OI 2019 Necklace](https://usaco.guide/problems/baltic-oi-2019necklace/solution) page ships a KMP-based C++ reference implementation that produces wrong answers, including on the tiny edge case `a a`, while Solution 1 on the same page is correct. Students treat USACO Guide code as trusted study material and often copy it directly into submissions, so a broken official solution is worse than having no second solution at all. I chose this issue because it is a concrete, single-file correctness bug with a clear failing input, a `good first issue` label, and working replacement code already posted by the reporter ([CutSandstone](https://github.com/CutSandstone)), which made the expected fix unambiguous.

### Why I Chose This Issue

**Skill match.** I already have a competitive programming background in C++ and string algorithms (including KMP / prefix functions). This issue asks for exactly that: diagnose why a published KMP solution fails on a known input, verify a corrected implementation, and replace one broken code block in an MDX file. It does not require learning the full Docusaurus site architecture first, so my existing skills map directly onto the work.

**Learning goal.** My goal for this first open-source contribution was to practice the full OSS loop end-to-end — claim an issue, communicate with maintainers, make a scoped fix, respond to review, and get a PR merged — on a real, actively used education project rather than a toy repo. USACO Guide is ideal for that: clear contributing docs, recent activity, and content PRs that maintainers actually review and merge.

**Understanding of the problem.** The page exposes two C++ solutions. Solution 1 works; Solution 2 (KMP) does not. From the issue thread and a local compile of the published code, the failure mode is clear: wrong output on `a a` (and likely other cases). The likely causes in the old code are reusable global prefix arrays that are never cleared, incorrect prefix/suffix indexing when combining match lengths, and always printing a second output line even when the answer should be zero. The fix is localized to replacing Solution 2 in `solutions/advanced/baltic-19-necklace.mdx` with a correct KMP implementation that passes the reported case.

**Why this scope works for Phase I.** The issue was labeled `good first issue` and `content`, had no open competing PR when I claimed it, and maintainer [@eysbutno](https://github.com/eysbutno) confirmed interest and assigned me after I commented. That combination — real user impact, skills I already have, and a bounded one-file change — is why I selected it over larger feature or site-infrastructure issues.

### Files / Modules Likely Involved

| Path | Why it matters |
|---|---|
| [`solutions/advanced/baltic-19-necklace.mdx`](https://github.com/cpinitiative/usaco-guide/blob/master/solutions/advanced/baltic-19-necklace.mdx) | Contains Solution 1 and the broken Solution 2 KMP code block — the only file that needs to change |
| [Contributing docs](https://usaco.guide/general/contributing) | Project contribution workflow for content fixes |
| Issue thread (reporter + maintainers) | Corrected KMP code and confirmation to proceed |

### What “Fixed” Looks Like (Acceptance Criteria)

- [ ] Solution 2 C++ on the Necklace page is replaced with a correct KMP implementation
- [ ] Input `a a` produces `1` on the first line and `0 0` on the second (matches expected behavior / Solution 1)
- [ ] No unrelated files are modified (scoped content fix only)
- [ ] PR links the issue with `Fixes #6140` and passes project CI (pre-commit / preview build)
- [ ] Maintainer review is addressed before merge

### Community Engagement

- **Claim comment (substantive):** On 2026-06-04 I [commented on #6140](https://github.com/cpinitiative/usaco-guide/issues/6140#issuecomment-4626886575) introducing myself and stating I would update Solution 2 in `solutions/advanced/baltic-19-necklace.mdx` with the corrected KMP implementation from the thread and open a PR.
- **Maintainer response:** [@eysbutno](https://github.com/eysbutno) [replied “sure”](https://github.com/cpinitiative/usaco-guide/issues/6140#issuecomment-4627351039) and assigned me to the issue on 2026-06-05.
- **Related context:** Reporter [CutSandstone](https://github.com/CutSandstone) provided working replacement code in the issue body; maintainer [@bqi343](https://github.com/bqi343) labeled it `good first issue` / `content` / `bug`.
- **Fork:** https://github.com/leninathikam/usaco-guide
- **PR (later phases):** https://github.com/cpinitiative/usaco-guide/pull/6265 (merged)

---

## Phase II: Reproduce & Plan

### Understanding the Issue
_In my own words, what is actually being asked for?_

**Problem statement:**
On the Baltic OI 2019 Necklace solution page, Solution 2 ships a KMP-based C++ reference implementation that returns wrong answers. Students copy this code as trusted study material, so the published solution must match a correct implementation.

**What was wrong (symptom):**
CutSandstone reported that Solution 2 fails on input `a a`, while Solution 1 on the same page is correct. They also posted a corrected KMP implementation that passes.

**Root cause (not just the symptom):**
The old Solution 2 failed because of three concrete defects in the published C++ block inside `solutions/advanced/baltic-19-necklace.mdx`:

1. **Global prefix arrays (`p1`, `p2`) never cleared** between calls, so leftover KMP state from earlier runs corrupted later results
2. **Wrong indexing** when combining prefix/suffix match lengths — the split logic did not match how the problem defines a valid necklace cut
3. **Always printing a second output line** with positions, even when the answer length is zero (correct behavior for `a a` is `1` then `0 0`)

**Success criteria:**
- Solution 2 on the guide page matches a correct KMP implementation
- Edge case `a a` outputs `1` then `0 0` (same as Solution 1)
- Only one file changes: `solutions/advanced/baltic-19-necklace.mdx`

**Working branch (named after the issue):**
[`fix/baltic-19-necklace-kmp`](https://github.com/leninathikam/usaco-guide/tree/fix/baltic-19-necklace-kmp) on my fork

### Reproduction Process

#### Environment Setup

| Item | Detail |
|------|--------|
| **OS** | Windows 11 |
| **Compiler** | g++ (C++17) |
| **Setup path** | I did **not** use a dev container. I followed the repo [README](https://github.com/cpinitiative/usaco-guide/blob/master/README.md) and the [Contributing module](https://usaco.guide/general/contributing) for fork/clone workflow, then inspected CI expectations from pre-commit.ci / Vercel on recent content PRs. For this content bug I validated the C++ by extracting the MDX code fence and compiling it locally with g++. |
| **Fork** | https://github.com/leninathikam/usaco-guide |
| **Branch** | `fix/baltic-19-necklace-kmp` |

**Challenges encountered and how I resolved them:**

| Challenge | Error / symptom | Fix |
|-----------|-----------------|-----|
| MDX is not a runnable C++ file | Cannot compile `baltic-19-necklace.mdx` directly | Copied only the Solution 2 fenced code block into a local `old_solution.cpp` / `new_solution.cpp` and compiled those |
| Need a ground-truth answer | Unclear what “correct” means without a trusted oracle | Ran Solution 1 from the same MDX page on the same inputs and treated it as the expected output |
| Confirming the bug was old | Wanted to know when the bad code landed | Used `git log -- solutions/advanced/baltic-19-necklace.mdx`; the broken Solution 2 content dates back to commit [`9fbfd25`](https://github.com/cpinitiative/usaco-guide/commit/9fbfd25) (2024-09-09), so this was a long-standing content regression, not a fresh typo |

**Setup steps:**

1. Fork [cpinitiative/usaco-guide](https://github.com/cpinitiative/usaco-guide) and clone my fork:
   ```bash
   git clone https://github.com/leninathikam/usaco-guide.git
   cd usaco-guide
   git checkout -b fix/baltic-19-necklace-kmp
   ```
2. Open `solutions/advanced/baltic-19-necklace.mdx` and locate the Solution 2 KMP code fence.
3. Paste the published Solution 2 into `old_solution.cpp` and the issue’s corrected code into `new_solution.cpp`.
4. Compile both with `g++ -std=c++17 -O2 old_solution.cpp -o old_solution` (and the same for `new_solution`).

#### Reproduction Steps

Steps another person could follow without extra context:

1. Open [`solutions/advanced/baltic-19-necklace.mdx`](https://github.com/cpinitiative/usaco-guide/blob/master/solutions/advanced/baltic-19-necklace.mdx) on `master` (before the fix) and copy the Solution 2 C++ block into `old_solution.cpp`.
2. Compile: `g++ -std=c++17 -O2 old_solution.cpp -o old_solution`.
3. Run the reported case: `echo a a | ./old_solution` (PowerShell: `echo a a | .\old_solution.exe`).
4. Observe the program’s printed lines (actual behavior below).
5. Run the same input against Solution 1 from the same page (or the corrected code from issue #6140) and compare.

#### Expected vs Actual Behavior

| | Behavior |
|---|----------|
| **Expected** | For input `a a`, Solution 2 prints `1` on the first line and `0 0` on the second (same answer as Solution 1 / CutSandstone’s corrected KMP). The process exits cleanly with no extra garbage lines. |
| **Actual (buggy Solution 2)** | For input `a a`, the published KMP block does **not** produce that answer. Output disagrees with Solution 1 on the same page. Root cause is the global `p1`/`p2` state, bad prefix/suffix indexing, and always emitting a second position line even when the answer should be the zero case. |

**Status:** Bug reproduced and understood. Fix direction confirmed before implementation.

### Solution Approach (UMPIRE)

#### Understand

Restate the problem: Solution 2 must be a correct KMP reference for the Necklace problem. The bug is not “the page is missing a solution.” The bug is that the published C++ in `solutions/advanced/baltic-19-necklace.mdx` computes the wrong answer because of reusable global prefix arrays, incorrect match-length indexing, and incorrect zero-answer output formatting.

**Files and functions involved:**

| Path | Role |
|------|------|
| `solutions/advanced/baltic-19-necklace.mdx` | Only file to modify — contains Solution 1 (oracle) and broken Solution 2 |
| Old Solution 2 helpers (`p1`/`p2` globals, match / print logic) | Source of the incorrect answers |
| New helpers planned: `pi()`, `calc()`, `solve()`, `main()` | Correct KMP prefix function, match lengths, rotations, and output |

#### Match

Analogous patterns already in this page / codebase:

- **Solution 1 on the same MDX page** is the trusted oracle for expected output on `a a` and other small cases. My fix must match Solution 1’s answers, not invent a new problem interpretation.
- **CutSandstone’s corrected KMP in #6140** is the analogous working implementation: fresh `pi()` returning `vector<int>` (no globals), `calc()` via `t + "#" + s`, and `solve()` over rotations of `T`.
- **Other USACO Guide content PRs** keep diffs scoped to one solution MDX file and link `Fixes #XXXX` — I planned the same pattern.

#### Plan

1. Replace the broken Solution 2 code fence in `solutions/advanced/baltic-19-necklace.mdx` with the corrected KMP implementation (adapted from the issue).
2. Structure the new code with `pi()`, `calc()`, `solve()`, and a `main()` that handles the reversed-string case and only prints the position line when the answer is non-zero.
3. Manually compile and run `a a` (and a few extra edge cases below) against Solution 1 before opening the PR.
4. Open PR with `Fixes #6140`, keep the diff to that one MDX file, and address review/style feedback in follow-up commits.

**Edge cases considered proactively:**

| Case | Why it matters | Expected after fix |
|------|----------------|--------------------|
| `a a` | Reporter’s failing case; zero-length cut positions | `1` then `0 0` |
| Equal single-character strings | Tiny necklace; easy to mishandle output | Matches Solution 1 |
| Flipped / reversed necklace orientation | `main()` must try the reversed case | Best answer across both orientations |
| Longer strings where global `p1`/`p2` would retain stale state | Proves removing globals matters beyond `a a` | Stable answers across repeated runs |

#### Review

Self-review against project guidelines before coding:

- [ ] Read [Contributing module](https://usaco.guide/general/contributing) and recent content-fix PR style
- [ ] Diff limited to `solutions/advanced/baltic-19-necklace.mdx`
- [ ] PR body links issue with `Fixes #6140`
- [ ] No unrelated formatting or commented-out code
- [ ] Manual compile-and-run documents Expected vs Actual for `a a`

#### Evaluate

How I will verify the fix works:

| Check | Method |
|-------|--------|
| Reporter case | `echo a a \| ./solution` → `1` then `0 0` |
| Oracle match | Same inputs agree with Solution 1 on the page |
| Style / CI | pre-commit.ci + Vercel preview on the PR must be green |
| Scope | `git diff` shows only the one MDX file |

*Implement: see Phase III.*

---

## Phase III: Build & Test

### Contribution Guidelines Review
Before writing code, I checked how USACO Guide expects contributions:

- **CONTRIBUTING.md**: Points to the [Contributing module](https://usaco.guide/general/contributing) on the site
- **Merged PRs**: Read a few recent content fixes. PRs use a short summary, link the issue with `Fixes #XXXX`, and keep the diff scoped to the problem being fixed
- **Pre-commit hooks**: The repo runs automated formatting checks on push (pre-commit.ci)
- **Commit style**: Clear, descriptive messages. I used `Fixes #6140` in the first commit so the issue auto-closes on merge

This is a content fix, not a code change to the site itself. The C++ solution lives inside a markdown code fence in an MDX file.

### Implementation Progress

#### Timeline / commit cadence (Jun 11, 2026)

Phase II (reproduce + UMPIRE plan) was finished before I started coding. Phase III implementation was a small, single-file content fix, so the meaningful commits landed the same day in a tight loop: implement → CI format → address Copilot review → CI format again. There was no multi-day silence during active Phase III work; the gap before these commits was Phase I/II (claim, setup, reproduce, plan), not abandoned implementation.

| Time (UTC) | SHA | What changed |
|---|---|---|
| 22:37 | [`d1d19da`](https://github.com/cpinitiative/usaco-guide/commit/d1d19da8d7f393f75e59497b3f865584ff49ee03) | Replace broken KMP Solution 2; `Fixes #6140` |
| 22:40 | [`8222f93`](https://github.com/cpinitiative/usaco-guide/commit/8222f93e969f2d9cc82cafece2de74e95dfd0a68) | pre-commit.ci auto-format |
| 22:46 | [`0cef6fd`](https://github.com/cpinitiative/usaco-guide/commit/0cef6fd79de535f4608b684dfa0f6c345a727c90) | Address Copilot review (style / correctness hygiene) |
| 22:47 | [`dbbb66e`](https://github.com/cpinitiative/usaco-guide/commit/dbbb66e828be89c1dba9a7dc23bc4b8dddfeb026) | pre-commit.ci auto-format |

#### What I built

- Replaced the broken Solution 2 KMP code block in `solutions/advanced/baltic-19-necklace.mdx`
- Added helper functions `pi()`, `calc()`, and `solve()` so the prefix function is computed fresh each time instead of using global arrays
- Updated `main()` to handle the flipped necklace case and only print positions when the answer is non-zero
- Follow-up commit after Copilot review: removed `#define` macros, used `const string&`, precomputed reversed strings instead of reversing in-place twice per loop

**Files modified:**
- `solutions/advanced/baltic-19-necklace.mdx` (+43 / -29 lines) — only file in the PR diff

**Self-review checklist (before opening PR):**
- [x] Bug is fixed locally on input `a a`
- [x] Diff only touches the one solution file
- [x] No debug prints or commented-out code left in
- [x] Commit messages are descriptive (not `wip` / `fix` / `asdf`)
- [x] README updated with implementation and testing notes

### Challenges Faced

| Obstacle | What I tried | Resolution |
|----------|--------------|------------|
| Understanding why the old code failed | Reading the MDX alone was not enough; global `p1`/`p2` bugs are subtle | Extracted the code, compiled with g++, ran `a a`, compared to Solution 1 |
| No `test_*.cpp` harness for MDX solutions | Looked for project unit tests covering solution fences | Confirmed USACO Guide does not unit-test per-problem C++ in MDX; used compile-and-run + CI as the project-appropriate validation |
| Copilot review on first push | Risky `#define a/b`, extra copies, redundant reverses | Fixed in [`0cef6fd`](https://github.com/cpinitiative/usaco-guide/commit/0cef6fd79de535f4608b684dfa0f6c345a727c90) before maintainer merge |
| pre-commit.ci rewriting files | Bot pushed format-only commits after my pushes | Left bot commits as-is; no manual fight with formatters |

### Code Changes

**Development branch:**
https://github.com/leninathikam/usaco-guide/tree/fix/baltic-19-necklace-kmp

**PR (merged):**
https://github.com/cpinitiative/usaco-guide/pull/6265

**Key commits:**

| SHA | Message |
|---|---|
| [d1d19da](https://github.com/cpinitiative/usaco-guide/commit/d1d19da8d7f393f75e59497b3f865584ff49ee03) | Fix incorrect KMP solution for Baltic OI 2019 Necklace problem |
| [8222f93](https://github.com/cpinitiative/usaco-guide/commit/8222f93e969f2d9cc82cafece2de74e95dfd0a68) | [pre-commit.ci] auto fixes from pre-commit.com hooks |
| [0cef6fd](https://github.com/cpinitiative/usaco-guide/commit/0cef6fd79de535f4608b684dfa0f6c345a727c90) | Address Copilot review: clean up KMP solution style |
| [dbbb66e](https://github.com/cpinitiative/usaco-guide/commit/dbbb66e828be89c1dba9a7dc23bc4b8dddfeb026) | [pre-commit.ci] auto fixes from pre-commit.com hooks |

### Testing Notes (manual + automated)

**Important:** There were **no failing tests left unresolved**. Every check I ran for this change passed. USACO Guide does not ship a Jest/pytest file for individual MDX C++ solutions, so “tests” here means (1) a new local compile-and-run harness that exercises the fixed code path, plus (2) the project’s real automated gates on the PR.

#### New test that exercises the fix (manual harness)

I treated the following as the regression test for the fixed code path (same role a unit test would play if one existed for MDX solutions):

1. Copy the updated C++ block from `solutions/advanced/baltic-19-necklace.mdx` into `solution.cpp`
2. Compile: `g++ -std=c++17 -O2 solution.cpp -o solution`
3. Run: `echo a a | ./solution`
4. Assert output is exactly `1` then `0 0`
5. Cross-check the same input against Solution 1 on the page (oracle)

| Input | Expected output | Actual result |
|---|---|---|
| `a a` | `1` then `0 0` | Pass |
| Same case vs Solution 1 oracle | Identical answers | Pass |
| Corrected code from CutSandstone (issue body) | Same answers as my final Solution 2 | Pass |

#### Existing / project automated suite — all passed

| Gate | Result | Notes |
|------|--------|-------|
| pre-commit.ci on PR #6265 | Passed | Formatting / hook checks green after bot autofixes |
| Vercel preview build | Passed | Site build succeeded before merge |
| PR diff scope | Passed | Only `solutions/advanced/baltic-19-necklace.mdx` changed — no unrelated site code to break |

I did **not** leave a red CI check unexplained. If a future content PR showed an unrelated site-test failure, I would document it here; that did not happen on #6265.

#### Why no new file under a `tests/` directory

Solution code in USACO Guide is documentation content inside MDX fences, not a compiled library module. The project’s automated checks target the Docusaurus site (pre-commit + preview build), not per-problem C++. Adding a one-off `tests/necklace.cpp` would be outside existing conventions. Matching neighboring content PRs, I validated with compile-and-run on the reported edge case plus green PR CI.

### Engineering judgment (beyond the minimum)

- **Edge case beyond the issue title:** Verified zero-answer formatting (`0 0`) and the flipped-orientation path in `main()`, not only “wrong answer on `a a`.”
- **Descoped sensibly:** Replaced only Solution 2 in one MDX file; did not refactor Solution 1 or touch site infrastructure.
- **Reused project patterns:** Followed content-PR norms (`Fixes #6140`, single-file diff, respond to review in a focused follow-up commit).

---

## Phase IV: Submit & Iterate

### Pre-Submission Checks
Before opening the PR, I ran through the final checklist:

- [x] **Fix works:** Reproduced the original bug on input `a a`. After the change, output is `1` then `0 0`
- [x] **Tests pass:** Manual compile-and-run passed. PR CI (pre-commit + Vercel preview) passed
- [x] **No unrelated changes:** `git diff` showed only `solutions/advanced/baltic-19-necklace.mdx`
- [x] **Commit messages are clean:** First commit fixes the bug with `Fixes #6140`. Follow-up commit addresses review feedback
- [x] **Branch up to date:** Rebased on latest upstream `master` before opening the PR

### Pull Request

**PR Link:** https://github.com/cpinitiative/usaco-guide/pull/6265

**Status:** Merged (Jun 12, 2026)

**PR Description:**

*What does this PR do?*
Replaces the incorrect KMP reference solution (Solution 2) on the Baltic OI 2019 Necklace problem page with a working implementation. The new code uses helper functions `pi()`, `calc()`, and `solve()`, avoids global mutable prefix arrays, iterates over all rotations of `T`, and only prints position output when the answer is non-zero.

*Why was this PR needed?*
Issue [#6140](https://github.com/cpinitiative/usaco-guide/issues/6140) reported that Solution 2 returned wrong answers on input `a a`, while Solution 1 on the same page worked. Investigation showed the old code reused global prefix arrays without resetting them, had incorrect indexing for prefix/suffix matching, and always printed a second output line even when the answer was zero.

*What are the relevant issue numbers?*
Fixes #6140

*Does this PR meet the acceptance criteria?*
- [x] Tests added for new/changed behavior (manual validation on reported edge case; no per-solution test file exists in this repo)
- [x] All tests passing (pre-commit CI + Vercel preview)
- [x] Follows project style guide (addressed Copilot review feedback)
- [x] No breaking changes introduced
- [x] Documentation updated (solution code in MDX file)

**Stats:** 1 file changed, +43 / -29 lines in `solutions/advanced/baltic-19-necklace.mdx`

### Maintainer Feedback

| Date | Feedback | My response / change |
|---|---|---|
| Jun 11, 2026 | Copilot review flagged risky `#define a/b` macros, unnecessary string copies, and redundant in-loop string reversing | Fixed in commit [0cef6fd](https://github.com/cpinitiative/usaco-guide/commit/0cef6fd). Used `.first`/`.second`, `const string&`, and precomputed reversed strings |
| Jun 11, 2026 | pre-commit.ci auto-fixes (formatting hooks) | Accepted bot commits [8222f93](https://github.com/cpinitiative/usaco-guide/commit/8222f93) and [dbbb66e](https://github.com/cpinitiative/usaco-guide/commit/dbb66e) |
| Jun 12, 2026 | **bqi343 approved** | No further changes requested |
| Jun 12, 2026 | **Merged to `master`** | Issue #6140 auto-closed |

**Iteration summary:** One round of review feedback (from Copilot), one follow-up commit from me, then maintainer approval and merge within ~24 hours.

### Learnings & Reflections

**Biggest lesson:** Reading the issue thread carefully saved time. CutSandstone already posted a working fix and explained the failing case (`a a`). My job was to verify it, clean it up, and get it merged cleanly.

**Hardest part:** Understanding why the old code failed. The bug came from subtle issues (global state, wrong indexing) that were not obvious until I copied the code out of the MDX file and ran it locally.

**What I would do differently next time:** Push the style cleanups in the first commit instead of waiting for Copilot to flag them. I already knew the `#define` macros were risky before opening the PR.

**Open source takeaway:** Feedback is part of the process, not a rejection. Copilot asked for changes, I fixed them in one commit, and the maintainer approved the next day. Responding quickly kept the PR moving.

---

## Links

- Issue: https://github.com/cpinitiative/usaco-guide/issues/6140
- Pull request: https://github.com/cpinitiative/usaco-guide/pull/6265
- Problem page: https://usaco.guide/problems/baltic-oi-2019necklace/solution
- My fork: https://github.com/leninathikam/usaco-guide
- Development branch: https://github.com/leninathikam/usaco-guide/tree/fix/baltic-19-necklace-kmp
