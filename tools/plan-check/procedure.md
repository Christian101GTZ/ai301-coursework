# Procedure: how this skill grades a plan package

## Read order

1. Live mode only: read `scope.md`. Confirm the issue URL is in the
   repo on its `Repo:` line; if the line is still a placeholder or the
   issue is in another repo, stop without grading and say why. Note
   the house rules. (Eval mode: skip this step.)
2. Read `rubric.md` and `references/evidence-guide.md`. Write down the
   six check names (diagnosis, evidence, scope, approach, test,
   conventions) and the verdict rule.
3. Read the issue. Write one line for the symptom (what goes wrong,
   and under what input or conditions) and one line for the expected
   behavior.
4. Read the repro evidence next, before the plan. For each step,
   write down what was run and what was observed. Mark every control
   run (a step where one thing was changed compared to the failing
   run) and write what it rules in or out (e.g. "same input without
   -v parses fine: the tokenizer is not the cause"). Reading the
   evidence before the plan means you know what the evidence pins
   down before a confident plan tries to tell you.
5. Read the thread highlights. List every comment by a maintainer,
   owner, member, or collaborator that gives explicit direction: a
   named culprit, a requested test, a preferred fix, or an existing PR
   to build on. If there is none, write "no maintainer direction".
6. Read the repo facts. Quote the contribution policy's AI rule if it
   has one, and write whether it requires AI-use disclosure (yes/no).
7. Only now read the candidate plan, then the candidate plan comment.
   Live mode: the plan is the student's `plan.md` and the comment is
   their draft comment; the repro evidence is their posted repro
   comment on the issue (or the house repro pack as quoted in the
   drafts), and the issue, thread, and repo facts come from GitHub as
   `references/evidence-guide.md` says. Eval mode: every part comes
   from the bundle only.

## Evidence gathering

Gather a quote for every check before grading any of them. Take the
plan side only from the plan and plan comment text; never read the
repo's code or other files to fill a gap in the plan.

1. diagnosis: quote the sentence(s) where the plan states the cause.
   If there is no such sentence, write "no stated cause".
2. evidence: take the stated cause and go through the repro steps
   and control runs from Read order step 4, plus the maintainer
   comments from step 5. Mark each one "supports", "rules out", or
   "neutral" for the stated cause, with a quote.
3. scope: list every change the plan commits to (each thing it says
   it will do), and quote its out-of-scope / "won't change" line. For
   each change, write one line saying how it fixes this bug, or
   "not needed for this bug".
4. approach: quote every place the plan says it will edit (a file
   path, a function, or a named code path or component), and the
   sentence that says what change goes there. Quote any words that
   put off choosing the change or leave a choice between different
   places or strategies open: "maybe", "whichever", "not sure",
   "somewhere", "investigate", "profile", "look into", "poke around",
   "either ... or" between two different fixes, or a question mark
   about the fix. An unknown about the exact function or line inside
   a named place is not one of these; note it as a stated unknown.
5. test: quote the test plan, and quote the exact result it says you
   will see after the fix. If it names no result, write "no
   observable result".
6. conventions: for each maintainer direction from Read order step 5,
   quote the plan comment sentence that responds to it, or write "no
   response". If step 6 found an AI disclosure requirement, quote the
   comment's disclosure sentence, or write "no disclosure".

## Check execution

1. Grade the checks in rubric order: diagnosis, evidence, scope,
   approach, test, conventions.
2. Grade each check using only its pass condition in `rubric.md` and
   the evidence gathered for it above. Give `pass`, `fail`, or
   `unclear`, with the one quote or fact that decided it.
3. Grade all six checks every time, even after one fails.
4. When something the plan or comment should contain is missing (no
   cause, no files, no test result, no disclosure), grade that check
   `fail`: putting it in is the plan's job. Use `unclear` only when
   the issue-side evidence the check needs (the repro steps, the
   thread, the repo facts) is missing or contradicts itself, so you
   cannot decide either way.
5. If diagnosis fails because there is no single stated cause, grade
   evidence `fail` too: there is no cause to check against the repro.
6. Judge the plan's content, not its length or formatting. A short
   plan that meets a pass condition passes; a long, confident one
   that misses it fails.
7. Do not re-read the whole package for each check. Use the gathered
   quotes; re-read only the part named in that check's Evidence
   column if a quote is missing.

## Verdict assembly

1. Turn every `unclear` grade into a fail for the verdict (the
   rubric's verdict rule). Keep the grade as `unclear` in the output.
2. If all six checks pass, the verdict is `accept`. If any check
   failed or was unclear, the verdict is `reject`.
3. Write the summary with one line per check. For a reject, put the
   deciding check(s) first with the quote that decided them; if
   evidence failed, list it first.
4. Live mode only: after the check lines, list any `voice-guide.md`
   rule the draft comment breaks, quoting the rule. This never
   changes the verdict.
5. End with the JSON block from `SKILL.md`, with the check names
   exactly as written in the rubric, and nothing after it.
