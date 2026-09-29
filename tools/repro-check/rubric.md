# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Exact version and behavior | the repro report's `Environment:` line and its `Expected`/`Actual` description, compared against the issue's own description of the bug | Pass if the report explicitly states the exact version tested, AND either (a) precisely describes the same behavior as the issue, or (b) states they followed the issue's exact steps on that version and clearly reports the behavior did not occur, backed by evidence of the actual attempt. | required |
| Reproducible steps | the repro report's steps section, together with the issue's own description or code, when the report explicitly references it instead of repeating it | Pass if a stranger reading the report together with the issue could follow the steps without guessing any missing commands, input files, or configurations. Referencing an exact detail already stated in the issue (code, file contents, parameters) instead of retyping it does not count as guessing — but a step that's vague, incomplete, or missing entirely still fails. | required |
| Direct evidence | the repro report's raw command/output block, checked against its Expected/Actual description | Pass if the output includes terminal output, logs, or screenshots that directly prove the failure, matching the exact error message or exit code described, OR the report includes actual command output from running the issue's steps that shows normal/expected behavior instead of the failure (an honest non-reproduction). | required |
| Professional and natural tone | the candidate claim comment and the candidate repro report (prose parts, not the raw command/output block) | Pass if the writing sounds like a normal human explanation to a peer — conversational, clear, straightforward English, with no emojis, decorative arrows, excessive bullet points, or overly enthusiastic marketing language. | required |
| Conventions / disclosure | the repo-facts contribution-policy line, compared against the candidate claim comment and repro report's actual content | Pass if the repo states no AI-related policy. If it does state one, pass unless there is a specific sign the comment or report violates that stated rule (for example: the policy requires a disclosure statement and none is present; or the policy requires human-authored comments and something indicates the comment was not human-written). No violation signal found counts as pass. | required |

## Verdict rule

Accept only if every required check passes. If any required check fails or is unclear, reject.
