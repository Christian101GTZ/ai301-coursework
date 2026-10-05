# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: the plan's stated cause (the "Cause:" or "Diagnosis"
line in `## Candidate plan`; live mode, the diagnosis section of
plan.md), compared against each step of `## Repro evidence` (live mode,
the student's posted repro comment on the issue), especially control
runs where one thing was changed. Maintainer comments in `## Thread
highlights` that confirm or name a culprit also count.

What good looks like: the cause names one specific place or behavior
in the program (not the symptom restated), at least one repro step
points to it, and no step rules it out. In calib-01, step 2 shows the
push really succeeded and step 4 shows re-entering the view fixes the
color, so "the view isn't refreshed after push" is grounded. Bad: the
plan blames something a control run already cleared (the same input
works without the blamed part), or repeats a confident diagnosis from
the thread that the repro steps contradict.

## Scope

Where it lives: the plan's change statement and its "In:" / "Out:" or
"Not in scope" lines in `## Candidate plan` (live mode, the scope
section of plan.md), read against the bug described in `## Issue`.

What good looks like: every change listed is needed to fix the issue's
bug, and the plan says what it will not change. In calib-01, the plan
adds one refresh call in one callback and puts "how push status is
computed" and "other views' refresh behavior" out of scope. Bad: the
fix arrives bundled with a dependency upgrade, a migration, a new
option, a module rewrite, or cleanups the issue never asked for
(pkg-06 turns an empty-tar fix into a containerd upgrade, a preload
rework, and a CI matrix).

## Executability

Where it lives: the file paths and the sentence describing the change
in `## Candidate plan` (live mode, the "files I'll touch" and
"approach" sections of plan.md).

What good looks like: the plan names where the change goes (a file,
a function, or a specific named code path) and the one change it will
make there, so a stranger could start without asking anything. In
calib-01: "the push completion callback in
`pkg/gui/controllers/sync_controller.go` adds the commits context to
its post-push refresh scope." A stated unknown about the exact
function or line inside that named place is fine (pkg-14 names the
client attach path in `zellij-server` and says the exact functions
get pinned while tracing). Bad: no place named, a first step of
"investigate" or "profile" before any change is chosen, or a choice
left open between different places or strategies ("gocui? tcell? not
sure", "upstream or vendored, whichever is easier", "recover()
somewhere").

## Test plan

Where it lives: the "Test:" line or test plan section in `## Candidate
plan` (live mode, the test plan section of plan.md), read against the
steps in `## Repro evidence`.

What good looks like: the test re-runs the repro steps (or adds a
specific test that recreates the bug) and names the exact result that
will be different after the fix: an output, a value, a color, an exit
code. In calib-01: "repro steps above; at step 3 the color must flip
without leaving the view." Bad: "should feel fast", "run the full test
suite", or "make sure nothing breaks", none of which name a result for
this bug.

## Honesty

Where it lives: any "Risks", "Unknowns", or "Not sure" lines in `##
Candidate plan` and the plan comment (live mode, the risks and
unknowns section of plan.md, and its `## Deviations` section after the
build).

What good looks like: real uncertainty is stated as an unknown with a
way to check it (calib-01 says it will also test the main commits
panel and a force push, because both share the callback). Deviations
say what changed from the plan and why, or say in plain words that
nothing changed. Bad: guesses written as facts, or an empty Deviations
section. A plan that is unsure what to build at all is an
executability problem, not honesty.

## Comms

Where it lives: `## Candidate plan comment` (live mode, the student's
draft comment.md), read against two sources: (1) `## Thread
highlights` (live mode, the issue's comments on GitHub) for explicit
direction from a maintainer, owner, member, or collaborator, and (2)
the contribution policy in `## Repo facts` (live mode, the repo's
CONTRIBUTING.md and README) for an AI-use disclosure requirement.

What good looks like: if a maintainer gave direction (a named culprit,
a requested test, a preferred fix, an existing PR), the comment
responds to it directly, by following it or saying why not; if the
policy requires AI disclosure, the comment discloses AI use. In
calib-01 the thread is empty and there is no disclosure rule, so the
comment passes, and it also respects CONTRIBUTING's review-bandwidth
note by keeping the fix minimal. Bad: a plan comment that ignores the
owner's posted culprit and patched build (pkg-04), or a strong plan
with no AI disclosure in a repo that requires one (pkg-20).
