# Procedure: how this skill grades a PR package

## Read order

1. Live mode only: read `scope.md`. Confirm the issue URL is in the
   repo on its `Repo:` line. If that line is still a placeholder, or
   the issue is in another repo, stop without grading and say why.
   Note the house rules. (Eval mode: skip this step.)
2. Read `rubric.md` and `references/evidence-guide.md`. Write down the
   six check names (fidelity, description, evidence, checks, clean,
   standards) and the verdict rule.
3. Read the issue. Write one line for the failing case it describes
   (what input or action goes wrong) and one line for the expected
   behavior.
4. Read the repo facts (eval mode: the `## Repo facts` block; live
   mode: `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`,
   and `README.md`). Write three lists: the description parts the
   repo requires (issue reference, checklist, sections), the checks
   the repo asks a PR to run (tests, lint, type check, build), and
   whether AI-use disclosure is required (yes/no, with the quote).
   If the repo states none of these, write "none stated" for that
   list.
5. Read the thread highlights. List every comment by a maintainer,
   owner, member, or collaborator that gives direction about the fix
   or the PR. If there is none, write "no maintainer direction".
6. Read the plan (eval mode: `## Plan context`; live mode: `plan.md`
   including its `## Deviations` section). Write down, as separate
   lists: (a) every change the plan commits to, (b) the files it names,
   (c) what it says is out of scope, (d) the test plan: each failing
   case to re-run and the observable result expected after the fix,
   and (e) every deviation or deferral the plan records. Read the plan
   before the diff so you know what was promised before you see what
   was done.
7. Only now read the candidate PR, in this order: the commits, the
   diff (live mode: run `git diff main...HEAD`, `git diff main...HEAD
   --stat`, and `git log main..HEAD --oneline` from the repo folder),
   the test evidence (live mode: `test_evidence.md`), and last the
   title and description (live mode: `pr_draft.md`). Reading the
   description last keeps its claims from coloring how you read the
   diff.

## Evidence gathering

Gather a quote for every check before grading any of them. Take the
PR side only from the diff, commits, description, and test evidence;
never read the repo's code or other files to fill a gap the package
leaves.

1. fidelity: list every hunk in the diff by file and one line saying
   what it does. Next to each, write which plan item (from Read order
   step 6a or 6e) it belongs to, or "not in plan". Then go the other
   way: for each plan item in 6a, write which hunk does it, or
   "missing", and if missing, whether the plan's Deviations (6e) and
   the description both say it was left out.
2. description: list every claim the description makes about what
   changed (each bullet, each "closes", each "no changes beyond",
   each "also updated"). Next to each, quote the hunk that makes it
   true, or write "no matching hunk". Also note any file in the
   `--stat` list that the description never mentions.
3. evidence: quote the commands and output in the test evidence.
   For the issue's own failing case, quote the before output and the
   after output, or write "not shown". For every other case in the
   plan's test plan (6d), quote the line that states its observed
   outcome, or write "not mentioned". Mark any run that only exercises a
   path the issue never said was broken (a control) as "control
   only". Quote any bare claim with no output ("tests pass", "works
   locally").
4. checks: for each check the repo asks for (Read order step 4),
   quote the line in the test evidence, or in the description's
   checklist or requirements section, that names it with its outcome
   (pass, fail, clean, a ticked item, a test count, or output), or
   write "not mentioned". A failing check with its reason stated counts as run.
5. clean: go through the diff hunk by hunk and quote every line that
   is debug output, commented-out code, an unused name this diff
   adds, a whitespace-only or formatting-only change, a duplicated
   block, or a change in a file or area the plan does not touch.
   Write "none found" if there are none.
6. standards: for each required description part from Read order
   step 4, quote where the description fills it in, or write
   "missing" or "left empty". If AI disclosure is required, quote
   the disclosure sentence, or write "no disclosure".

## Check execution

1. Grade the checks in rubric order: fidelity, description, evidence,
   checks, clean, standards.
2. Grade each check using only its pass condition in `rubric.md` and
   the quotes gathered for it above. Give `pass`, `fail`, or
   `unclear`, with the one quote or fact that decided it.
3. Grade all six checks every time, even after one fails.
4. When something the PR should contain is missing (no before/after
   output, no disclosure, an empty template section, a planned
   change with no hunk and no note), grade that check `fail`: putting
   it in is the PR author's job. Use `unclear` only when the plan or
   the repo facts themselves are missing or contradict each other, so
   the comparison cannot be made either way.
5. Apply the honest-shortfall rule exactly as the rubric states it: a
   difference from the plan passes fidelity and evidence only when
   both the plan (Deviations or note) and the description record it.
   One of the two is not enough.
6. A hunk that is unplanned work is a fidelity problem; a hunk that
   is junk (debug line, dead code, churn) is a clean problem. The same
   hunk can fail both; grade each check on its own condition.
7. Judge the content, not the length or formatting. A one-line
   description that is true passes description; a full checklist that
   claims something the diff lacks fails it.
8. Do not re-read the whole package for each check. Use the gathered
   quotes; re-read only the part named in that check's Evidence column
   if a quote is missing.

## Verdict assembly

1. Turn every `unclear` grade into a fail for the verdict (the
   rubric's verdict rule). Keep the grade as `unclear` in the output.
2. If all six checks pass, the verdict is `accept`. If any check
   failed or was unclear, the verdict is `reject`.
3. Write the summary with one line per check. For a reject, put the
   failing check(s) first with the quote that decided them.
4. Live mode only: after the check lines, list any `voice-guide.md`
   rule the PR title or description breaks, quoting the rule and the
   sentence. This never changes the verdict. Then list any step where
   this procedure was silent and what you did instead.
5. End with the JSON block from `SKILL.md`, with the check names
   exactly as written in the rubric, and nothing after it.
