# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis | the plan's stated cause (its Cause / Diagnosis section) | passes if the plan names one specific root cause: a specific function, code path, setting, or step in the program's behavior that produces the bug (e.g. "the post-push refresh does not include the branch-commits view"); fails if there is no cause, if it only restates the symptom ("the color doesn't update"), or if it lists several possible causes without choosing one ("maybe gocui? tcell? not sure") | required |
| evidence | the plan's stated cause, read against every step of the repro evidence (especially control runs, where one thing was changed and the bug did or did not appear) and the thread highlights | passes if at least one repro step or a maintainer comment in the thread points to the stated cause, and no repro step rules it out; fails if any repro step or control run shows the bug still happens without the blamed cause, or does not happen even with it (e.g. the plan blames the tokenizer, but the control run with the same input parses fine); a diagnosis copied from the thread still fails if the repro evidence contradicts it | required |
| scope | the plan's change / scope statement (what's in and what's out), read against the issue and the stated cause | passes if every change in the plan is needed to fix the bug the issue and repro describe, and the plan names what it will not change; fails if the plan adds work the issue did not ask for: refactors, dependency upgrades or migrations, new options or settings, UI redesigns, rewrites of a whole module, or "while I'm in here" cleanups, even if the core fix inside it is correct | required |
| approach | the plan's change section and file list | passes if the plan names where it will edit (a file, a function, or a specific named code path or component, e.g. "the reattach path in `zellij-server`'s client connection handling") and commits to one specific change there, so a stranger could start without making any decision first; a stated unknown about the exact function or line inside that named place is fine; fails if no place is named, if the plan is to "investigate", "profile", or "poke around" before choosing a change, or if it leaves a choice between different places or strategies open ("upstream or vendored, whichever is easier", "gocui? tcell?", "recover() somewhere") | required |
| test | the plan's test plan, read against the repro evidence's steps | passes if the test plan re-runs the repro steps or adds a specific test that recreates the bug, and names the exact result you will see after the fix (an output, a value, a color, an exit code); fails if the result is a feeling ("should feel fast"), a general check ("run the full test suite"), or "no errors" | required |
| conventions | the plan comment, read against (1) the thread highlights and (2) the repo-facts block's contribution / AI policy | passes only if both hold: (1) if a maintainer or owner gave explicit direction in the thread (a named culprit, a requested test, a preferred fix, an existing PR to build on), the comment responds to it directly, by following it or by saying why not; if the thread has no such direction, this part passes; (2) if the repo facts say AI use must be disclosed, the comment contains a disclosure of AI use (treat every plan as AI-assisted); if no such requirement is stated, this part passes; fails if either part is missing | required |

## Verdict rule

Ready (accept) if every required check passes. Hold (reject) if any
required check fails, especially when the diagnosis conflicts with the
repro evidence. An unclear (?) grade on a required check counts as a
fail, because a plan that cannot be confirmed as grounded, bounded, and
testable should be revised before it is posted. This rubric has no
preferred checks; all six checks are required.
