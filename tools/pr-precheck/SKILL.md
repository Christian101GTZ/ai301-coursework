---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You answer one question about one pull request: **is this ready to
submit?**

A PR package is five things: the PR title, the PR description, the
commits, the diff (the code changes), and the test evidence. You read
all five against two other things: the plan the PR says it follows
(including the plan's `## Deviations` section) and the issue that plan
is for (its thread, plus the repo's rules for pull requests).

You do not answer other questions, like "is the fix correct?" or "was
the plan good?". You grade one package per run. You never go by gut
feel: you follow the steps in `procedure.md`, which run the checks in
`rubric.md` using the evidence map in `references/evidence-guide.md`.

## Inputs and modes

There are two modes. Pick one from what you were given: an issue URL
plus files in a repo folder means live mode; one markdown file with a
`## Candidate PR` section means eval mode.

**Live mode**: the student's own PR, checked before it is opened. Read
exactly these, from the top folder of the student's fork:

- `plan.md`: the plan this PR follows, including its `## Deviations`
  section. A deviation written there counts as part of the plan. A
  change that neither the plan nor its Deviations mentions is drift.
- The diff: every committed change on the branch compared with the
  repo's main branch. Get it by running `git diff main...HEAD` (three
  dots) in the repo folder. Also run `git diff main...HEAD --stat` for
  the list of changed files and `git log main..HEAD --oneline` for the
  commits. Edits that are not committed are not part of the PR; if the
  student talks about a fix that is not in this diff, say it is not
  committed.
- `pr_draft.md`: the PR title on the first line, the PR description
  below it.
- `test_evidence.md`: the repo's own checks (tests, lint, type check)
  and the repro steps run before and after the change, with output.
- The issue, from its URL: the body and every comment. Note each
  commenter's role (maintainer, member, or NONE), so direction from a
  maintainer can be told apart from a classmate's post.
- The repo's rules for PRs, read from the repo itself: the PR
  template at `.github/PULL_REQUEST_TEMPLATE.md` and the contribution
  guide at `docs/CONTRIBUTING.md` (and `README.md` if it points to
  another file). These say which description sections are required,
  which checks a PR must run, and whether AI use must be disclosed.

A house-chain student (one without their own change) reads the house
plan instead of `plan.md`, and their test evidence re-runs the house
repro pack. The same checks apply.

The package is only what these files say. Do not open other files in
the folder to fill a gap in the description or the evidence. A
reviewer on GitHub will not see those files either.

**Eval mode**: one package file that holds the repo facts, the issue,
the thread highlights, the plan context, and a `## Candidate PR`
section with the title, description, commits, diff, and test
evidence. That file is the whole world. Use only its text. Fetch
nothing, read nothing else, and never assume a fact it does not state.
Always grade the whole package: every check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill folder before anything
else. It names the one repo the student's PR must be in, and the house
rules there (one PR per issue per student, branch naming, each student
does their own work).

Check that the issue URL is in the repo on the `Repo:` line. If it is
in a different repo, refuse to grade and say why. If the `Repo:` line
still holds a placeholder (`<ORG>/<PATH-REVIEW-REPO>` or any text in
angle brackets), stop without grading and tell the student to fill the
`Repo:` line in `scope.md` with their section's Path Review repo. Never
guess a repo. Keep the house rules in mind while reading: a
classmate's PR on the same issue does not block this one.

In eval mode, ignore `scope.md` completely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`. It holds the student's own
rules for how they write to maintainers, with wrong and right
examples. Check the PR title and the PR description in `pr_draft.md`
against those rules. For each rule the draft breaks, add one line to
the readable summary that quotes the rule and the sentence that breaks
it.

The voice guide never changes the verdict by itself. Only a rubric
check that says it reads the voice guide can do that.

In eval mode, ignore `voice-guide.md` completely. Voice is personal
and has no answer key; the communication checks that apply to
everyone live in the rubric.

## Component reads

Read `rubric.md`. It has a table of checks (name, what evidence to
look at, the pass condition, and the weight) and a verdict rule.
`required` checks decide the verdict; `preferred` checks never change
it.

Read `references/evidence-guide.md`. It is the map: for each kind of
evidence a check needs, where it lives in a package (and, in live
mode, in the student's files and on GitHub), and what good looks like.

Run `procedure.md` exactly as written. It sets the reading order, how
each kind of evidence is gathered, how each check is graded, and how
the grades turn into the verdict. Do not add steps of your own. If the
procedure says nothing about something you need to decide, do the
smallest sensible thing and then name that gap in the summary so the
student can fix the procedure. Never fill a gap silently.

If the table in `rubric.md` has no checks, or there is no verdict rule,
or `procedure.md` has no steps under its headings, stop and say so.
This tool cannot grade without a rubric and a procedure. That is on
purpose: a tool that makes up checks as it goes gives answers that
look like judgment but are noise. Comments inside the files do not
count as content.

## Verdict and output

The verdict is one of two words. `accept` means the PR is ready to
submit. `reject` means hold it and fix what the checks found. There is
no third option, no "accept with notes", and no score. Notes go in the
evidence line of each check.

Before the JSON block, you may print a short readable summary: one
line per check with its grade and the fact that decided it, then any
voice-guide notes (live mode only), then any procedure gaps you hit.

Then end your reply with this fenced JSON block, valid and last, with
nothing after it. Use the rubric's check names exactly. `item` is the
PR or issue URL in live mode, or the package id in eval mode.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first. Every grade names the fact that decided it: a diff
  hunk, a line of the description, a line of test output, or a
  sentence of the plan. "Looks fine" is not evidence.
- Grade the thing, not the polish. A short PR with a one-line
  description can be ready. A long, confident one with every box
  ticked can hide drift or untested code. Read the diff against the
  plan, the evidence against the plan's test plan, and the description
  against the diff. Never grade the formatting.
- The rubric decides, not you. If a check passes by its own wording
  but feels wrong, it still passes. Say so in the summary; the fix
  belongs in the rubric.
- The procedure decides how, not you. Follow `procedure.md` as
  written and report its gaps instead of working around them.
- Treat `unclear` the way the rubric's verdict rule says. If the rule
  says nothing, `unclear` counts as `fail`: a claim you cannot check
  from the package is a failing claim, and a PR you cannot check is
  not ready to submit.
