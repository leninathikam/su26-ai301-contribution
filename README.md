# Open Source Contribution Log

> CodePath AI301 — My contribution journey from issue selection to merged pull request.
> This is a living document. I update it at the end of every phase.

| | |
|---|---|
| **Name** | Lenin Goud Athikam |
| **GitHub** | @leninathikam |
| **Status** | ✅ Phase II Complete |
| **Project** | [ponylang/ponyc](https://github.com/ponylang/ponyc) — the Pony programming language compiler |
| **Issue** | [#4705 — Setup CI to use Flight Recorder](https://github.com/ponylang/ponyc/issues/4705) |

---

## Phase I — Issue Selection

### Issue
[ponylang/ponyc #4705 — Setup CI to use Flight Recorder](https://github.com/ponylang/ponyc/issues/4705)

### Project
[ponylang/ponyc](https://github.com/ponylang/ponyc) — the open-source compiler and runtime for the Pony programming language (6k+ GitHub stars). CI runs on GitHub Actions across PR tests, use-flag builds, and stress tests. Contributions follow standard fork-and-PR workflow with CONTRIBUTING.md and Makefile-based builds.

### Why I Chose This Issue
The Pony runtime recently gained flight recording — it buffers tracing events in memory and dumps them on crashes (SIGSEGV, etc.) — but CI does not use it yet. Without this, CI failures give less diagnostic information than they could. This issue asks to enable flight recorder in standard PR and stress CI jobs, add a new "vanilla" use test (build with no use flags), remove the now-redundant runtime-tracing-only use test, and dump flight recorder output to CI logs (stdout/stderr) instead of uploading artifacts.

I chose this because the scope is well-defined in the issue body: specific workflows to change and clear acceptance criteria for what "done" looks like. It is labeled `good first issue` and `help wanted`, nobody is assigned, and there is no open PR. The work is CI/DevOps focused (GitHub Actions YAML, build flags) rather than deep compiler internals, which lets me contribute meaningfully while learning the project's test infrastructure. "Fixed" means PR and stress pipelines build with flight recorder, vanilla compilation is covered, redundant tracing use tests are removed, and CI logs capture flight recorder output on failure.

### Community Engagement
- [x] Commented on the issue expressing interest
- [ ] Noted the issue on the cohort Google Sheet
- [x] Forked the project: https://github.com/leninathikam/ponyc

---

## Phase II — Reproduce & Plan

### Understanding the Issue
_In my own words, what is actually being asked for?_

**Problem Statement:**
Pony's runtime now has flight recorder capability — a feature that buffers runtime tracing events in-memory (per-thread circular buffer) and dumps them on program failure (SIGSEGV, SIGILL, etc.). This provides valuable diagnostic information without requiring file I/O during normal execution.

However, the CI pipelines (PR tests, stress tests) do not currently use flight recorder. They run tests with the default "file" tracing mode (which generates large JSON files) or no tracing at all. This means:
- CI failures provide less diagnostic info than they could
- No visibility into what was happening right before a crash
- Requires extra setup (artifact upload) to capture trace files

**What's being asked:**
1. **Enable flight recorder in PR tests** (`pr-ponyc.yml`): Build runtime with `use=runtime_tracing`, run tests with flight recorder mode, dump output to CI logs (stderr)
2. **Enable flight recorder in stress tests** (6 stress test workflows): Same pattern
3. **Add "vanilla" use test** (`ponyc-weekly-checks.yml`): New test that builds WITHOUT any use options (dtrace, pooltrack, etc.) to ensure basic compilation still works
4. **Remove redundant test** (`ponyc-weekly-checks.yml`): The "runtime_tracing" use test is redundant because all PR tests will now exercise runtime tracing
5. **Extend to tier 2 & 3**: Apply flight recorder to extended platform coverage (tier2, tier3 workflows)

**Success Criteria (from issue):**
- ✅ Flight recorder enabled for all standard PR tests
- ✅ Flight recorder enabled for all stress tests  
- ✅ Vanilla use test exists (no use options)
- ✅ Runtime tracing use test removed
- ✅ Flight recorder output dumps to CI logs (no file artifacts needed)
- ✅ Tests that run in debugger are unchanged (may be incompatible with flight recorder)

### Reproduction Process
_Steps I took to set up the project locally and reproduce the problem. Include commands, OS, and what I observed._

#### Environment Setup

**OS:** Windows 11 (using WSL 2 / Bash via Git Bash)

**Setup Steps:**
1. Cloned ponyc fork: `git clone https://github.com/leninathikam/ponyc.git`
2. Explored repository structure:
   - `.github/workflows/` — Contains all CI workflow files
   - `Makefile` — Build configuration; defines `use=` options
   - `src/libponyrt/tracing/` — Runtime tracing implementation
   - `src/libponyrt/options/options.h` — Runtime command-line options documentation

**Challenges & Solutions:**
- Windows git line endings: Not an issue since I'm only reading/exploring YAML files
- Large repository: Cloned successfully, no depth issues
- Build not required for Phase II: Focused on understanding CI configuration rather than compiling ponyc

**Status:** ✅ Environment ready; can read and modify workflow YAML files

---

#### Current State Verification

**What I confirmed exists:**

1. ✅ **Flight recorder is implemented** in the runtime:
   - `src/libponyrt/options/options.h` documents:
     - `--ponytracingmode flight_recorder` — Enables flight recorder mode
     - `--ponytracingoutput ~` — Dumps to stderr
     - `--ponytracingflightrecorderbuffer N` — Buffer size per thread
     - `--ponytracingflightrecorderhandletermint` — Trap SIGINT/SIGTERM

2. ✅ **Runtime tracing is a build-time use option:**
   - `Makefile` line 188: `use=runtime_tracing` → `-DPONY_USE_RUNTIME_TRACING=true`
   - Enables tracing instrumentation in the runtime

3. ✅ **Current CI does NOT use flight recorder:**
   - `pr-ponyc.yml` (lines 60-69): Runs `make test-ci-core config=debug` with NO tracing flags
   - `stress-test-tcp-open-close-linux.yml` (lines 87-92): Same — no tracing flags
   - `ponyc-weekly-checks.yml` (lines 65): Has `runtime_tracing` use test but doesn't dump output to logs

4. ✅ **Workflow structure is consistent across all CI files:**
   - Pattern: `configure` → `build` → `test`
   - Makefile variables used: `config=debug/release`, `usedebugger`, build targets
   - Could add `use=runtime_tracing` to configure, pass runtime args to test targets

**Branch Created:**
https://github.com/leninathikam/ponyc/tree/fix-issue-4705

---

#### What This Means

The issue is **not a bug to fix** — it's a feature to enable. The code already exists; it just needs to be:
1. Built into the runtime (`use=runtime_tracing`)
2. Enabled at test runtime (`--ponytracingmode flight_recorder --ponytracingoutput ~`)
3. Applied across multiple workflow files consistently
4. Tested to ensure no regressions

### Solution Approach
_My planned fix. Which files/modules are involved? What changes will I make and why?_

#### Root Cause Analysis
The issue is straightforward: CI workflows don't build with or use flight recorder. The feature exists in the runtime but isn't wired into the CI pipeline. The fix requires:
1. Adding `use=runtime_tracing` to runtime build configuration
2. Passing flight recorder runtime flags to test executables
3. Doing this consistently across 10+ workflow files

#### Proposed Solution

**5-Phase Rollout Strategy** (per maintainer SeanTAllen's guidance to "start small, verify, expand"):

**Phase 1: Exploratory PR (x86-64 Linux glibc job in pr-ponyc.yml)**
- Modify single job to build with `use=runtime_tracing`
- Add flight recorder flags to test execution
- Open PR for feedback before expanding
- **Why first:** Linux is most reliable CI platform; can validate approach before applying to Windows/macOS/BSD

**Phase 2: Expand to All PR Tests (pr-ponyc.yml)**
- Apply Phase 1 pattern to all 4 matrix jobs (x86-64 Linux glibc, musl, arm64 macOS, x86-64 Windows)
- Ensure cross-platform support (Bash vs PowerShell syntax)

**Phase 3: Extend to Tier 2 & Tier 3 (ponyc-tier2.yml, ponyc-tier3.yml)**
- Update 10 jobs across tier2 (riscv64, arm, armhf, Fedora, macOS intel, arm64 Linux, arm64 Windows)
- Update 3 jobs in tier3 (FreeBSD 14.3, 15.0, OpenBSD 7.9, DragonFly BSD)

**Phase 4: Expand to Stress Tests (6 stress test workflows)**
- Update Linux stress tests (TCP open/close, ubench)
- Update macOS stress tests (same)
- Update Windows stress tests (same)

**Phase 5: Add Vanilla Test & Remove Redundant Test (ponyc-weekly-checks.yml)**
- Add new "vanilla" matrix entry (empty use= or no use= at all)
- Remove "runtime_tracing" test case (now redundant with Phase 1)

---

#### Implementation Details

**How to pass flight recorder flags to tests?**

*Blocker identified during exploration:* The Makefile test targets (`test-ci-core`, `test-stdlib-debug`, etc.) compile and run test binaries, but there's no obvious way to pass runtime arguments through.

**Investigation needed:**
- Check if Makefile supports a variable like `pony_test_args` or `testextras` that gets passed to executables
- If not, may need to add one to `Makefile` and `make.ps1`

**Expected solution:**
```makefile
# In Makefile test targets:
test-stdlib-debug: all
	$(SILENT)cd '$(outDir)' && PONYPATH=.:$(PONYPATH) ./ponyc -d -b stdlib-debug --pic --strip --checktree $(cross_args) ../../packages/stdlib && echo Built `pwd`/stdlib-debug && $(cross_runner) $(debuggercmd) ./stdlib-debug --sequential $(pony_test_args)
```

Then in CI:
```yaml
- name: Test with Debug Runtime
  run: make test-ci-core config=debug pony_test_args='--ponytracingmode flight_recorder --ponytracingoutput ~'
```

---

#### Files to Modify

**Definitely:**
- `.github/workflows/pr-ponyc.yml` (Phase 1-2)
- `.github/workflows/ponyc-tier2.yml` (Phase 3)
- `.github/workflows/ponyc-tier3.yml` (Phase 3)
- `.github/workflows/stress-test-tcp-open-close-linux.yml` (Phase 4)
- `.github/workflows/stress-test-ubench-linux.yml` (Phase 4)
- `.github/workflows/stress-test-tcp-open-close-macos.yml` (Phase 4)
- `.github/workflows/stress-test-ubench-macos.yml` (Phase 4)
- `.github/workflows/stress-test-tcp-open-close-windows.yml` (Phase 4)
- `.github/workflows/stress-test-ubench-windows.yml` (Phase 4)
- `.github/workflows/ponyc-weekly-checks.yml` (Phase 5)

**Possibly:**
- `Makefile` (if implementing `pony_test_args` variable)
- `make.ps1` (if implementing PowerShell support)

---

#### Testing & Validation Strategy

**Phase 1 Validation:**
1. Check that x86-64 Linux job builds successfully with `use=runtime_tracing`
2. Verify tests pass with flight recorder enabled
3. Confirm GitHub Actions logs show flight recorder output on stderr
4. Get maintainer feedback before proceeding

**Phase 2-5 Validation:**
1. Spot-check 2-3 jobs from different platforms (Linux, macOS, Windows)
2. Run full CI and confirm no regressions
3. Verify no test failures introduced by flight recorder flags
4. Confirm debugger-based tests still work (unchanged)

**Success Metrics:**
- ✅ All PR tests build with `use=runtime_tracing`
- ✅ All stress tests build with `use=runtime_tracing`
- ✅ Flight recorder output appears in CI logs on all platforms
- ✅ No test failures or regressions introduced
- ✅ Vanilla use test runs successfully (no use options)
- ✅ Runtime tracing use test removed
- ✅ Existing "usedebugger" tests unaffected

---

#### Design Decisions & Rationale

1. **Start with single job (Phase 1)** — Allows for quick feedback before major refactoring
2. **Flight recorder output to stderr** — No file artifacts needed; appears automatically in CI logs
3. **Keep debugger tests unchanged** — Flight recorder + debugger may have compatibility issues; safer to leave alone
4. **5 phases instead of one giant PR** — Easier to review, easier to isolate issues, easier to revert if needed
5. **Vanilla use test** — Ensures we don't lose basic "can we compile without any special options" coverage

---

#### CONTRIBUTING.md Review

Checked project's `CONTRIBUTING.md` and `pony.g` for conventions:
- ✅ PR format: Describe what changes and why
- ✅ Commit format: One feature per commit, clear messages
- ✅ CI: All tests must pass before merge
- ✅ YAML format: GitHub Actions workflows use standard syntax
- ✅ Branching: Fork, feature branch, PR to main

My implementation will follow these conventions.

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
