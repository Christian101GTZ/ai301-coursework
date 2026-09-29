# Evidence guide: where proof lives in a reproduction package

<!--
THIS IS THE PART YOU WRITE (new this week: Unit 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; the rubric swap showed you what that feels
like. Write the map you wish your grader had.
-->

## Environment

Where it lives: the repro report's `Environment:` line (eval bundle: the
"Candidate repro report" section; live mode: the student's draft repro
report), read against the issue's own stated version/environment if it
gives one.

What good looks like: the exact software version is named, not "latest"
or left out, and it matches the version the issue targets — or, if it
differs, the report says so explicitly rather than leaving the reader to
notice.

## Steps

Where it lives: the repro report's steps section, plus the issue's own
description or code whenever the report explicitly points back to it
instead of retyping it (a reader sees both in the same thread).

What good looks like: every command, input file, and configuration
value needed to run the steps is stated explicitly, either directly or
by referencing an exact detail already given in the issue; nothing is
left for the reader to guess or infer, and the starting state (what to set up
before step 1) is stated.

## Behavior shown

Where it lives: the repro report's raw command/output block, read
against its own Expected/Actual description and against the issue's
description of the bug.

What good looks like: the output directly shows the same failure the
issue describes (the same error message, exit code, or symptom, not an
adjacent one) — or, for an honest non-reproduction, shows the steps
completing with normal/expected behavior instead of the failure.

## Honesty

Where it lives: the repro report's stated conclusion (its
Expected/Actual lines), read against its own raw output block from the
same report.

What good looks like: the stated conclusion matches exactly what the raw
output shows — a reproduction claim is backed by output showing the
failure, and a cannot-reproduce claim is backed by output showing the
steps ran without it. A conclusion that overstates or contradicts its
own evidence fails here even if the writing sounds confident.

## Comms

Where it lives: the repo-facts contribution-policy line, read against
the candidate claim comment and repro report's actual content, to check
for any specific sign they violate whatever that policy asks for (not
just disclosure — some repos ask for human-authored comments instead,
or something else entirely); the claim comment and repro report's own
prose, read on their own for tone.

What good looks like: no policy stated means this passes automatically;
a stated policy passes unless there's a concrete sign of a violation
(a required disclosure statement is missing, or something indicates a
comment wasn't human-written). The writing reads like a normal
explanation to a peer,
with no emojis, decorative arrows, excessive bullet points, or
marketing-style enthusiasm.
