# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer activity | Repo-facts block: last 5 default-branch commits and maintainer first-response sample. In live mode, use recent default-branch commits and recent issue responses listed in references/evidence-guide.md. | Pass if there is at least one human-authored default-branch commit within the last 90 days OR a maintainer has responded to a recent issue within 30 days. | required |
| Repository in use | Repo-facts block: archived status, latest release, and last push. In live mode, check the repository archived status, latest release date, and most recent push or commit. | Pass if the repository is not archived and has either a push within the last 180 days or a release within the last 365 days. | required |
| Newcomer-sized scope | Issue body and comment thread. Look for umbrella or tracking issues, unresolved design discussion, support-only questions, explicit core-internals changes, or multiple abandoned attempts. | Pass if the issue asks for one bounded contribution and none of the listed scope-risk signals are present. | required |
| Issue availability | Repo-facts block for assignees and linked PRs, plus the issue comment thread. In live mode, also apply the Path Review house rule from scope.md. | Pass if there is no active assignee or open linked PR already implementing the issue. Claim comments count only when the current scope rules say they count. | required |

## Verdict rule

Accept the issue only if every required check passes.

If any required check fails, reject the issue.

If the evidence needed for a required check is genuinely missing, grade
the check as unclear. An unclear required check counts as a fail.