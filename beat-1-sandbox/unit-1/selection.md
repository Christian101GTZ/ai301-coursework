# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38

**Verdict output**

issue-select — live mode, single candidate

Read `scope.md`: candidate source is `codepath/pathreview-ai301-fa26-s3` — issue #38 is
in scope. No house-rule conflicts (no claim comments present at all). Read `rubric.md`:
four required checks — Maintainer activity, Repository in use, Newcomer-sized scope,
Issue availability.

- **Maintainer activity — pass.** Last 3 default-branch commits all human-authored
  (Andrew Burke), most recent 2026-09-16T21:42:18Z, 5 days before this grading
  (2026-09-21) — within the 90-day window.
- **Repository in use — pass.** Not archived; `pushed_at` 2026-09-16T21:50:20Z, 5 days
  ago — within the 180-day window.
- **Newcomer-sized scope — pass.** Issue names one file to change
  (`api/middleware/auth.py`) and one new test file
  (`tests/integration/test_auth_middleware.py`), lists four concrete edge cases
  (expired token, malformed token, missing `Authorization` header, wrong signing
  secret), and states an estimated effort of 3–5 hours. No umbrella structure,
  unresolved design debate, support-only question, core-internals change, or
  abandoned-attempt history.
- **Issue availability — pass.** 0 comments, no assignees, 0 cross-referenced/linked
  PRs in the issue timeline.

All four required checks pass → verdict: accept.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38",
  "checks": [
    {"name": "Maintainer activity", "grade": "pass", "evidence": "Last 3 default-branch commits human-authored (Andrew Burke), most recent 2026-09-16, 5 days before grading."},
    {"name": "Repository in use", "grade": "pass", "evidence": "Not archived; pushed_at 2026-09-16, 5 days before grading."},
    {"name": "Newcomer-sized scope", "grade": "pass", "evidence": "Names api/middleware/auth.py and tests/integration/test_auth_middleware.py, lists 4 concrete edge cases, estimated 3-5h; no scope-risk signals present."},
    {"name": "Issue availability", "grade": "pass", "evidence": "0 comments, 0 assignees, 0 linked/cross-referenced PRs in the issue timeline."}
  ],
  "verdict": "accept"
}
```

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. Smoke test after patching the Windows subprocess bug, `--limit 1` (issue-01 only,
   diagnostic only, not a scored rubric run): `agreement: 0/1 scored items`.
2. First full 20-issue run, no `--save-run`: `agreement: 18/20 scored items (bar: 18/20:
   PASS)`. Categories: `claimed 4/4  clear-accept 6/8  dead-repo 3/3  policy 1/1  scope
   4/4`. Misses: issue-01 and issue-19, both failing `Newcomer-sized scope`.
3. Final full 20-issue run, `--save-run eval-run.txt`, after removing a leftover empty
   template block from `rubric.md` (no check wording changed): `agreement: 18/20 scored
   items (bar: 18/20: PASS)`. Same categories, same two misses. This is the run recorded
   in the committed `eval-run.txt`.

**Issue analysis**

`issue-01` (conda/conda#16475, category `clear-accept`): gold label is `accept`; my
rubric produced `reject`, failing the `Newcomer-sized scope` check. The issue is a fully
specified documentation task — it names exact pages to add and update (a new task page,
plus edits to `manage-pkgs.rst`, `pip-interoperability.rst`, and `new-features.md`) and
spells out the content each page needs. None of my check's own listed scope-risk signals
actually apply: it is not an umbrella/tracking issue, there is no unresolved design
debate, it is not a support question, it does not touch core internals, and there is no
history of abandoned attempts. My best explanation is that the grading model read
"touches five files" as scope creep by itself, even though my check's pass condition
never says multi-file work fails — it only fails on the five named signals. That is a
gap in how the check is worded, not in how it was applied.

**Check rationale**

From `rubric.md` (`tools/issue-select/rubric.md`), the `Newcomer-sized scope` row:

> Issue body and comment thread. Look for umbrella or tracking issues, unresolved design
> discussion, support-only questions, explicit core-internals changes, or multiple
> abandoned attempts. | Pass if the issue asks for one bounded contribution and none of
> the listed scope-risk signals are present.

I wrote it this way because the four families named in lecture reduce, for scope, to "is
this one piece of work with a decided design," and the five signals are the concrete,
checkable ways an issue fails that test — each one is something a grader can point to in
the text rather than a feeling. Keeping the list closed (only these five signals count)
was deliberate: it stops the check from becoming "reject anything that looks like more
than an hour of work," which would wrongly kill legitimate multi-file but well-specified
issues like real documentation or refactor tasks.

**Trade-offs**

I considered narrowing the check by adding a line like "touching several files for one
coherent, fully specified change is not by itself a scope-risk signal," which is aimed
directly at fixing `issue-01` and `issue-19`. I did not make or test that change: my
rubric's `scope` category was already at its floor of 4/4 correct on this run, and
loosening the pass condition risks turning some of those correctly-rejected reject-gold
scope issues into false accepts — a risk I could only rule out by re-running the full 20
again, not just the two I was trying to fix. Since 18/20 already clears the pass bar with
every category floor met, I left the check exactly as written and accept that it will
keep missing a small number of well-specified, multi-file "arguable scope" issues like
these two.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. Fit to my interests and time available: issue #38 is Python backend/API work,
   directly on the list of things I said I wanted for a first issue (APIs, Python,
   testing, backend), and it builds on the Path Review work I've already done there
   (JS/TS skill detection and tests), so I'm not starting cold on this codebase. The
   estimated 3–5 hours is realistic alongside my other coursework.

2. What the verdict identified correctly, and what I weighed that the rubric could not:
   the rubric confirmed the objective facts — the maintainer is active, the repo is
   live, the issue is unclaimed, and the scope is bounded to one file plus one new test
   file with four named edge cases. What I weighed personally, which the rubric has no
   check for, is that those four edge cases (expired token, malformed token, missing
   header, wrong signing secret) give me a concrete mental checklist to start from on
   day one, which lowers my actual ramp-up risk beyond anything "bounded scope" alone
   captures, and that auth-adjacent testing is a skill I specifically want more of.

3. Anticipated difficulty in claiming it: low for the claim itself — the Path Review
   house rule means other students' claims don't block me, and #38 currently has no
   comments or assignees at all, so there's nothing to negotiate around. The real
   difficulty will be before I write any tests: understanding how `api/middleware/auth.py`
   actually validates a token before I can write edge cases that test the real
   validation logic rather than a guess at it, and wiring new fixtures into
   `tests/conftest.py` correctly.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
