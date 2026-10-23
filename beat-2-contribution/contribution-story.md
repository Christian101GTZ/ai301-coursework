# Contribution: Only auto-generate on version change when no AI note exists for that version

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Christian Gomez Diaz
**GitHub Username:** Christian101GTZ
**Issue:** https://github.com/AcademySoftwareFoundation/dna/issues/127
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built issue-scout, fixed its sort order, then cut its rubric from 14 lines to 8**

I turned my Unit 1 grader into a scout with three searches from my fit profile: llm, embeddings, and fastapi. My first scan accepted 0 of 5. The cause was my sort order. I sorted by most recent update, and a "can I take this?" comment counts as an update, so already-claimed issues rose to the top. I changed only the sort, to fewest comments first, and the next scan accepted 2 of 5. After that, I cut the rubric from 14 lines to 8 as a separate change. I tested it on four issues: one fuzzy rejection flipped to accept, and both canaries stayed rejected. On the Issue Selection site, most good issues were already taken, some by pull requests that weren't linked to the issue. Checking a full page at once found dna #127, which my scout accepted.

**Unit 6: Installed the voice guard, patched it twice for Windows, and wrote dna's memory file**

I added an Upstream section to my voice guide with four rules (two for claims, one for questions, one for pings), installed the guard hook, and wired repro-check, plan-check, and pr-precheck to one guide. Three things broke on Windows: python3 is a dead shortcut, so I changed the hook to call python; the install's link command copies files instead of linking, so I used one-line pointer files instead; and the guard's draft check failed with "the claude CLI is not on PATH," because claude is installed as claude.cmd and Python could not find it, so I patched one line to look it up with shutil.which. After the patch, the wiring test stopped a body-less comment command as expected, and the manual check passed my claim. For the memory file, one scoped orientation run over frontend/packages/app/src found the three files behind issue #127 and the lines where the hook regenerates without checking for an existing note; the file is 60 lines and git does not see it.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

**Claim comment:** https://github.com/AcademySoftwareFoundation/dna/issues/127#issuecomment-6205471051

I found this issue on the course's Issue Selection site. I searched with the tags
bug and help wanted instead of good first issue, because I wanted a real bug or
feature to fix, not just a starter task, and most of the good-first-issue picks
were already taken. After checking a whole page of results at once for assignees,
open pull requests, and "I'm working on this" comments, dna #127 was one of the
few still free. The project is an AI tool for reviewing film dailies, and I wanted
to work on something with AI involved, so it fit what I was looking for.

My scout accepted it on October 15. When I re-graded it on October 22 with the
patched rubric, it was accepted again with a difficulty note of moderate: the
issue names the area (the version-change hook and the settings text) but not the
files, so finding the spot is on me. It fits my profile because it is a behavior
bug in the React and TypeScript frontend of a Python-first repo, and the repo has
a frontend test suite I can add a test to. I hope to learn how to trace a bug
through a codebase I have never seen, test each step before moving on, document
what I did, and run enough tests at the end to be sure nothing else broke.

The scout did not hold up perfectly. On the first re-grade it rejected my own
issue because of my own claim comment, since the availability check only ignored
bots. It also passed a closed issue that a merged pull request had already solved,
because nothing in the rubric asked whether the issue was still open. I fixed both
in the availability check: comments from my own account no longer count, and the
issue must be open with no merged pull request closing it. The scope patch itself
held up: the tracker canary stayed rejected, and the difficulty note matched what
I expected.

One thing I chose with eyes open: someone asked to work on this issue in May and
never followed up. The wild scope says a stranger's claim blocks you, so I did not
claim over them silently. I named them in my claim, said why the issue looked
free, and gave them a way to keep it.

**Toolkit**

- Tool: issue-scout. I used its three searches from my fit profile
  (llm, embeddings, fastapi), sorted fewest comments first, and then
  graded picks from the Issue Selection site one URL at a time against
  my three-check rubric.
- What it produced: accept on dna #127 on October 15, and accept again
  on October 22 with the patched rubric, this time with a difficulty
  note of moderate (the area is named, the files are not, and the
  change touches two spots).
- Difficulties: on the October 22 re-grade it rejected my own issue
  because of my own claim comment, since the availability check only
  ignored bots. It also passed usagetrim #54, a closed issue that a
  merged pull request had already solved, because nothing asked if the
  issue was still open. The scope patch flipped that old canary from
  reject to accept, which turned out to be right: one merged pull
  request did the whole thing, so it was one big piece of work.
- What changed: I replaced my newcomer-scope check with scope-feasible
  and added the difficulty note section, so loose edges raise a
  warning instead of a rejection. Then I fixed availability twice:
  comments from my own account no longer count, and the issue must be
  open with no merged pull request closing it.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
