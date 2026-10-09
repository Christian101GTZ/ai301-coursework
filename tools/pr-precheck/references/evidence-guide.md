# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: the plan's scope and "not in scope" lines and its
deviation notes (eval mode: the `## Plan context` block; live mode:
`plan.md`, including `## Deviations`), read against the diff (eval
mode: the `### Diff` block under `## Candidate PR`, plus the
`### Commits` list; live mode: `git diff main...HEAD` and
`git diff main...HEAD --stat` run in the fork folder). The
description's claims about fidelity ("implements the plan as
posted", "no changes beyond", "also updated X") live in
`### Description` (live mode: `pr_draft.md` below the first line)
and are read against the same diff.

What good looks like: every hunk in the diff maps to a change the plan
names, and every change the plan names has a hunk, or a deviation note
in the plan that the description repeats. In calib-01, the plan says
"remove the first, shadowed file-decoration-style line" in
`themes.gitconfig`, and the diff is exactly one removed line in that
file. An honest deviation re-ties a mismatch: pkg-13 defers a case in
the plan's note and says so again in the description, and that is a
pass. Bad, in either direction: the diff does more than the plan (a
new config option, a rewrite of a nearby function, a rename pass, a
second feature) with no deviation note; the diff does less than the
plan (a promised docs change or second fix is absent) and neither the
plan nor the description says so; or the description claims fidelity
the diff contradicts ("docs now document the limitation" when no docs
file is in the diff).

## Test evidence (harness category: not-tested)

Where it lives: the `### Test evidence` block under `## Candidate PR`
(live mode: `test_evidence.md`), read against the plan's test plan
(the "Test:" or "Test plan" lines in `## Plan context` or `plan.md`)
and the failing case in `## Issue`. The repo's own required checks
are named in `## Repo facts` (the PR template's checklist or the
contribution policy; live mode: `.github/PULL_REQUEST_TEMPLATE.md`
and `docs/CONTRIBUTING.md`).

What good looks like: the evidence re-runs the repro on the path the
issue says is broken and shows the before output and the after output
the plan's test plan named, as commands plus output, a test name that
failed and now passes, or a transcript. In calib-01: the configparser
command, "before: DuplicateOptionError", "after: parsed OK". If the
plan's test plan names a second case, the evidence at least states its
observed outcome (pkg-05: "Same-key redefine prints the one-time
warning"), or the plan and the description both name it as deferred.
The repo's own checks (tests, lint, type check) appear by name with
their outcome ("clippy clean", "614 tests"), either in the test
evidence or as a truthful item in the description's checklist (pkg-08
states "code formatted" in its Requirements line); pasted output is
not required, and a failing check reported with its reason still counts
as run. Bad: "tests pass", "works on my machine", "verified for
a full day" with nothing named; evidence that only exercises a control
or the unchanged path (a GET when the issue's failing case is a POST;
the single-file case when the bug needs two files); a named case
never mentioned at all (calib-04 runs repro 1 and never mentions
repro 2); a repo check never mentioned, or only a blanket "CI is
green" that names no check.

## Diff quality (harness category: unreviewable)

Where it lives: the unified diff itself, hunk by hunk (eval mode:
`### Diff`; live mode: `git diff main...HEAD`), and the commit list
(`### Commits`; live mode: `git log main..HEAD --oneline`). Read it
on its own first, then against the plan's file list.

What good looks like: every hunk serves the planned change, and a
reviewer can read the diff top to bottom and see only that change.
calib-01 is one removed line in one file. Bad: leftover debug output
(`print`, `eprintln!`, `console.log`, `dbg!`), a commented-out first
attempt or commented-out debug line, a variable, function, or import
this diff adds and never uses, whitespace-only or formatting-only
changes to lines the fix did not need to touch, blocks of identical
re-printed lines, or an import-restructure or unrelated hunk folded
in. Debris is a reviewability problem even when the fix underneath it
is correct and evidenced (pkg-12, pkg-15, pkg-18).

## Standards and comms (harness category: standards-wall)

Where it lives: the repo's stated asks in `## Repo facts` (the "pull
requests:" line for the template and its required parts, and the
"contribution policy" line for an AI-disclosure rule; live mode:
`.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`), read
against the PR title and description (`### Title` and
`### Description`; live mode: the first line and the rest of
`pr_draft.md`). Maintainer direction, if any, is in `## Thread
highlights` (live mode: the issue's comments, by author association).

What good looks like: each part the template requires is present and
filled in: the issue reference ("Closes #2211"), the checklist with
true boxes ticked, the type-of-change and testing sections, and "N/A"
with a reason where a section does not apply. If the repo's policy
requires AI-use disclosure, the description says in the author's own
words how AI was used (pkg-11 does this). If the repo states no
template and no AI policy, as calib-01's delta does, a short
description that references the issue is enough. Bad: a required
checklist or "closes #" line visibly absent (pkg-01), template
sections left empty or still holding placeholder text, or a strong
fix with no AI disclosure in a repo whose policy requires one
(pkg-20). A template section filled with words that the diff
contradicts is a fidelity problem, not a standards one.
