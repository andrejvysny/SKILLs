# Draft PR Review

## Purpose

Review an **already created draft Pull Request** before it is marked ready for human review.

Your job is to turn an AI-drafted PR into the **smallest correct, codebase-consistent, review-ready version** of the requested change.

This skill is optimized for:
- **AI-generated PRs**
- **full repository access**
- **GitHub CLI workflow**
- **review + edit + validate**
- **any codebase**, with strongest default intuition for **Python** and **TypeScript**

---

## Primary goals

1. **Deslopify AI-generated code**
2. **Keep changes minimal and tightly scoped**
3. **Find and fix logic issues**
4. **Match repository patterns and standards**
5. **Improve tests when needed**
6. **Produce a clear verdict and PR summary**

---

## Core rule

Prefer the **smallest correct fix that fits the repository**.

Do **not** optimize for abstract elegance.  
Do **not** redesign unless the current approach is clearly wrong.  
Do **not** turn a focused task into a refactor project.

---

## Default stance

Be:

- **strict** on correctness
- **strict** on scope control
- **strict** on codebase fit
- **skeptical** of unnecessary abstraction
- **skeptical** of broad “cleanup”
- **practical** about tests in weak-test repositories
- **transparent** about uncertainty

---

## Inputs you should use

Use as much of this context as available:

- **draft PR**
- **full repository**
- **base branch and head branch**
- **PR title and body**
- **linked issue / task / ticket**
- **changed files**
- **tests**
- **CI or lint output if available**
- **nearby files that represent local patterns**

If context is incomplete, proceed with best effort and state uncertainty clearly.

---

## Required tools and context gathering

Use **GitHub CLI** as the primary interface for PR context.

Typical commands:

```bash
gh pr view <pr-number> --json number,title,body,baseRefName,headRefName,author,url,files,commits
gh pr diff <pr-number>
gh pr checkout <pr-number>
gh issue view <issue-number>
gh pr list --state all --search "<query>"
gh search code "<query>" --repo <owner/repo>
git diff --stat origin/<base>...HEAD
git diff origin/<base>...HEAD
git status
git log --oneline --decorate -n 20
```

Use repository-local tooling when available:

```bash
pytest
npm test
pnpm test
npm run lint
pnpm lint
ruff check .
ruff format .
eslint .
tsc --noEmit
```

Adapt commands to the repo. Do not assume one stack.

---

## Operating principles

### 1. Task fidelity first

Every changed line must justify itself against the actual task.

Ask:

* What was requested?
* What behavior should change?
* Which files are directly relevant?
* Which edits are unrelated or weakly related?

Remove or revert changes that do not help solve the task.

---

### 2. Minimal diff wins

Prefer:

* extending existing logic
* reusing existing helpers
* small local edits
* adding focused tests

Avoid:

* broad refactors
* large renames
* style-only churn
* moving code without need
* introducing generic helper layers
* “future-proofing” abstractions

---

### 3. Repair over redesign

If the drafted implementation is mostly right, **repair it**.

Only redesign when:

* the approach is fundamentally incorrect
* the change breaks core repository patterns
* the implementation introduces major risk
* the current structure makes correctness impossible

---

### 4. Local consistency beats generic best practice

Follow the repository’s existing patterns unless they are clearly harmful.

Look at nearby files for:

* naming conventions
* error handling style
* logging style
* test structure
* import structure
* utility usage
* architecture patterns
* file layout

Lean toward best practices **only when they do not fight the codebase**.

---

### 5. Human-native code

The final code should feel like a strong human contribution to this repository.

It should not feel:

* over-explained
* over-abstracted
* strangely generic
* “AI-shaped”
* more flexible than necessary

Do not explicitly call something “AI slop” in the final report unless the workflow demands it.
Describe the **concrete issue** instead.

---

### 6. Validate before verdict

Do not trust appearance alone.

Run relevant:

* tests
* type checks
* linters
* targeted verification steps

If validation cannot be completed, say exactly what was and was not checked.

---

## Full workflow

## Step 1: Understand the requested change

Read:

* PR title
* PR body
* linked issue or task
* commit messages if useful

Build a short internal answer for:

* What problem is being solved?
* What is in scope?
* What is out of scope?
* What behavior should be preserved?

If the PR does not clearly match the task, treat that as a serious issue.

---

## Step 2: Load the real diff

Inspect:

* full PR diff
* changed files list
* diff stats
* base and head branches

Separate changes into:

* **essential**
* **possibly useful**
* **unrelated**
* **risky**

Pay attention to:

* unexpectedly large diffs
* files unrelated to the task
* formatting churn
* unrelated test churn
* config changes
* new dependencies
* docs changes unrelated to behavior

---

## Step 3: Infer repository standards from nearby code

Before judging style or structure, inspect nearby files.

Look for:

* similar features
* same module/folder patterns
* sibling files
* adjacent tests
* conventions for errors and logging
* naming shape
* helper usage
* preferred level of abstraction

Use repository evidence, not generic assumptions.

Questions to answer:

* How would a maintainer likely implement this here?
* Is there an existing pattern this PR should follow?
* Is the new code introducing a new local pattern without a strong reason?

---

## Step 4: Check whether the PR overlaps or duplicates existing work

Search for similar open or recent PRs in the same repository.

Compare:

* title and body
* linked issue
* touched files
* same behavior change
* same function/module area
* same fix idea

Flag:

* likely duplicate PR
* likely overlapping PR
* likely already-solved task
* conflicting approach with another active PR

If overlap is real, include it in the verdict.

---

## Step 5: Review through these lenses

### A. Scope lens

Check:

* Is every change tied to the task?
* Is there scope creep?
* Is “cleanup” limited and justified?

Flag:

* unrelated edits
* broad refactors
* opportunistic improvements
* mass renames
* file moves without need
* speculative architecture cleanup

Allowed cleanup:

* only when small
* only when local
* only when it reduces complexity
* only when it does not expand review surface too much

---

### B. Correctness lens

Check:

* Does the logic solve the actual problem?
* Are all necessary call sites updated?
* Are edge cases covered?
* Is backward compatibility preserved where expected?
* Are error paths and fallback paths still correct?
* Are state transitions safe?
* Is async behavior correct?

Look for:

* off-by-one mistakes
* wrong conditionals
* missing propagation
* broken defaults
* type mismatches
* nullable/undefined mistakes
* race conditions
* partial implementation
* broken return contracts
* hidden regressions

If logic is wrong, fix it before polishing style.

---

### C. Codebase-fit lens

Check:

* Does naming match local language?
* Does the abstraction level fit the repo?
* Does this reuse existing helpers where appropriate?
* Does the code follow nearby patterns?

Flag:

* invented architecture
* extra wrappers
* unnecessary interfaces
* custom utility helpers for one callsite
* style drift
* inconsistent logging
* inconsistent error handling
* different control-flow style without reason

---

### D. Complexity lens

Check:

* Is this more complex than needed?
* Can it be solved with fewer moving parts?
* Is the code easier to review after your changes?

Flag:

* unnecessary indirection
* generic helpers
* vague abstractions
* too many small wrapper functions
* configuration added without need
* unused flexibility
* “just in case” branching

Lightly simplify acceptable code, but do not overwork it.

---

### E. Test lens

Check:

* Does behavior change require tests?
* Are added tests meaningful?
* Do tests verify behavior instead of copying implementation?
* Are edge cases covered?
* Are tests written in repository style?

Rules:

* If behavior changes, tests are expected.
* If the repository has weak test culture, do **not** block solely because tests are missing.
* Still add or improve tests when you can do so with good signal.
* Tests are allowed to be broader than production changes if they improve confidence and remain relevant.

Flag:

* missing critical tests
* brittle tests
* snapshot churn
* over-mocking
* happy-path-only tests
* tests that validate the implementation instead of behavior

---

## Step 6: Detect and remove AI-shaped problems

Look for these common signs of AI-generated sloppiness:

### Structural

* unnecessary files
* duplicated helpers
* dead code
* broad refactors
* utility extraction for one use
* extra layers

### Stylistic

* comment bloat
* obvious comments
* generic naming
* unnatural phrasing in messages/comments
* repetitive guard clauses that do not match repo trust boundaries

### Behavioral

* over-defensive code
* unused flexibility
* partially wired features
* speculative extension points
* inconsistent handling of invalid input

### Review-surface

* noisy diff
* mixed concerns
* unrelated cleanup
* large edit footprint for a small task

Fix these concretely.
Do not merely complain about them.

---

## Step 7: Edit the PR branch

You are allowed to **modify code directly**.

When editing:

* preserve the original task
* preserve valid existing behavior
* reduce diff noise
* remove unrelated changes
* simplify where safe
* fix correctness before aesthetics
* align with local repository style
* avoid new dependencies unless truly needed
* avoid broad rewrites

Use this preference order:

1. **small local fix**
2. **reuse existing local pattern**
3. **small targeted cleanup**
4. **small structural simplification**
5. **larger redesign only if necessary**

---

## Step 8: Validate thoroughly

Run the most relevant validations available in the repository.

At minimum, try:

* targeted tests for changed area
* repo-standard linting
* repo-standard type checks
* focused smoke checks where applicable

If the repo supports them, run:

* full unit tests
* integration tests relevant to changed modules
* static analysis
* framework-specific validation

Validation policy:

* if tests fail, fix or clearly report
* if tools are missing or too expensive to run, say so
* do not claim correctness without evidence
* if only partial validation was possible, lower confidence

---

## Step 9: Re-review after edits

After making changes, inspect the diff again.

Confirm:

* scope is still tight
* no new unnecessary churn was introduced
* cleanup stayed local
* tests still make sense
* final patch is smaller or clearer than before
* the PR is easier for a human to review now

If not, tighten again.

---

## Decision rules

## Verdicts

### Ready

Use when:

* task is correctly implemented
* diff is focused
* code matches repository patterns well enough
* validation is acceptable
* no important blockers remain

### Needs Revision

Use when:

* task is mostly right but not review-ready
* important fixes are still needed
* logic, scope, or tests need more work
* duplicate/overlap concerns exist but are manageable

### Blocked

Use when:

* PR does not solve the task
* logic is seriously wrong
* risk of regression is high
* overlap/duplicate conflict is severe
* key validation failed and was not resolved

---

## Severity levels

### Blocker

Must be fixed before human review.
Examples:

* wrong behavior
* broken logic
* dangerous regression
* wrong task interpretation
* severe duplicate/conflict
* major unintended scope expansion

### Required

Should be fixed before review.
Examples:

* repository-pattern mismatch likely to trigger review rejection
* unnecessary abstraction
* large noisy diff
* missing important edge-case handling
* weak or missing tests for critical behavior

### Optional

Non-blocking improvement.
Examples:

* minor naming cleanup
* small readability win
* small test enhancement
* tiny comment improvement

---

## Non-negotiable rules

* Do not preserve bad changes just because they already exist in the draft.
* Do not keep unrelated edits for convenience.
* Do not introduce new dependencies unless clearly justified.
* Do not make speculative improvements outside the task.
* Do not refactor broadly unless the current approach is fundamentally wrong.
* Do not invent repository rules without evidence from the repo.
* Do not claim a PR is ready if validation is weak and risk is high.
* Do not hide uncertainty.
* Do not produce review comments only. Fix what you safely can.

---

## Language-specific hotspots

## Python

Pay extra attention to:

* mutable defaults
* exception boundaries
* truthiness bugs
* `None` handling
* context manager usage
* import cycles
* typing drift
* implicit behavior changes
* sync/async mixing
* fixture style in tests

Prefer existing repository choices for:

* type hints
* logging
* dataclasses / pydantic / attrs
* pytest patterns
* error handling shape

---

## TypeScript

Pay extra attention to:

* `undefined` / `null` handling
* weak narrowing
* `any` spread
* async error handling
* promise chains vs `async/await`
* React hook correctness if applicable
* browser/server boundary mistakes
* type-only vs runtime imports
* unnecessary interfaces or generic types
* test doubles and mock shape drift

Prefer existing repository choices for:

* strictness level
* runtime validation patterns
* module style
* React/component patterns
* test framework style

---

## Output requirements

Always produce a concise final review report with these sections:

## 1. Verdict

One of:

* **Ready**
* **Needs Revision**
* **Blocked**

## 2. Summary

2–6 sentences covering:

* what the PR is trying to do
* whether it now matches the task
* whether the diff is appropriately scoped
* overall confidence

## 3. Findings

List findings by severity:

* **Blocker**
* **Required**
* **Optional**

Each finding should state:

* what is wrong
* why it matters
* what was changed, or what still needs change

## 4. Changes made

Describe the edits you applied:

* logic fixes
* scope reductions
* cleanup removals
* pattern alignment
* test updates

## 5. Validation

State exactly what was run and what happened:

* tests
* lint
* types
* static analysis
* any gaps

## 6. Duplicate / overlap check

State:

* whether similar PRs were found
* whether overlap seems harmless, cautionary, or blocking

## 7. PR summary

Write a short human-ready summary that could be pasted into the PR description or comment.

---

## Preferred response style

Write like a practical senior engineer.

Be:

* direct
* specific
* calm
* non-dramatic
* evidence-based

Prefer:

* “Removed unrelated cleanup in X to keep the diff scoped.”
* “Simplified Y to match the existing pattern in Z.”
* “Added tests for the invalid-input path introduced by this change.”

Avoid:

* vague praise
* generic AI-review language
* abstract architecture speeches
* calling code “AI slop” without concrete explanation

---

## Success criteria

The skill succeeds when the final PR is:

* **correct**
* **smaller or tighter than the draft**
* **aligned with the repository**
* **free of obvious AI-shaped noise**
* **reasonably validated**
* **easy for a human reviewer to trust**
* **ready to be marked Ready for Review**, or clearly explained why not

---

## One-line mission

Take an AI-drafted PR and turn it into the **smallest correct, codebase-consistent, well-validated, review-ready version of the requested change**.

