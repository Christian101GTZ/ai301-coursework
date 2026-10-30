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

The AI suggestion feature is not responding correctly: with the
"Regenerate notes on version change" setting on, it creates new AI notes
even when the selected version already has notes. This causes
unnecessary note generation and does not match the behavior the issue
describes.

**Expected Behavior**

When the setting is enabled, the AI should only generate notes when the
user switches to a version that does not already have notes. Existing
notes should stay as they are. The setting's description in the settings
menu should say that this is how it works.

**Current Behavior**

When the user switches back to a version that already has notes, the AI
regenerates the notes again instead of recognizing that the version
already has them. Every switch costs an extra AI call, and the new note
is added on top of the old one, so the screen jumps to the new one.

**Affected Components**

- `frontend/packages/app/src/hooks/useAISuggestion.ts`: the hook where
  note generation is triggered on version change (the effect at lines
  193 to 213 checks only whether the setting is on).
- `frontend/packages/app/src/components/SettingsModal.tsx`: where the
  setting and its description are shown (around lines 999 to 1010).
- `frontend/packages/core/src/aiSuggestionManager.ts`: where the
  request to generate is actually sent (`generateSuggestion`, line 191).
- `frontend/packages/app/src/hooks/useAISuggestion.test.tsx`: my new
  test, which shows the extra generation.

**Toolkit**

- Tool: a scoped orientation run in Claude Code, pointed at only
  `frontend/packages/app/src`, after I had found two of the files
  myself by searching the code for the issue's words ("version change")
  and then for the setting's variable name. The result went into my
  repo memory file, `CLAUDE.local.md`, so every later session starts
  from it.
- What it produced: the three files above, how they connect (the
  settings modal and the hook never import each other; they share one
  TanStack Query key), what the hook does on a version switch, and that
  no tests existed for these files yet.
- Difficulties: none met in the run itself. The one judgment call was
  keeping it scoped to one folder instead of mapping the whole repo,
  which kept it to one short run.
- What changed: nothing in the tool. I wrote the conclusions into the
  memory file and deleted the template's instructions, so the file
  stays under 60 lines.

---

## Reproduction Process

**Environment Setup**

Repo state: AcademySoftwareFoundation/dna, branch main, commit 8df0a00
("Dna docs improvements (#241)"), cloned from my fork Christian101GTZ/dna
on 2026-10-22. Issue #127 in plain words: dna has an AI helper that
writes draft review notes for the shot version you are looking at, and a
checkbox "Regenerate notes on version change." With the box on, clicking
from version A to B and back to A asks the AI to write A's notes again,
even though A already has notes. The issue wants that second request
skipped.

What a stranger needs to get the same result:

- Windows 11 Home, using the Git Bash terminal.
- Node v24.21.0, npm 11.19.0, git 2.54.0.
- `npm install` from `frontend/` (666 packages added).
- The project's own test runner, vitest 2.1.9, run from
  `frontend/packages/app`.
- Not needed: Docker, an AI API key, or the backend. The frontend tests
  swap the server and the AI for fakes, so they run offline in seconds.

Baseline before touching anything: `npm run test:run` from
`frontend/packages/app` gave 16 test files passed, 100 tests passed. So
anything that fails after this is about the issue, not my setup.

Challenges: the clone first landed inside my course repo because my
terminal was still in that folder, so I moved it up one level. npm
printed vulnerability warnings and skipped two packages' install
scripts; I left both alone (running `npm audit fix` would change dna's
lock file for nothing to do with my issue), and the tests ran fine.

**Steps to Reproduce**

Where the behavior lives, from reading the code (three files: a switch,
a watcher, and a messenger):

- The switch: `frontend/packages/app/src/components/SettingsModal.tsx`,
  around lines 999 to 1010, the checkbox and the sentence under it. It
  saves a yes/no value called `regenerate_on_version_change`.
- The watcher: `frontend/packages/app/src/hooks/useAISuggestion.ts`,
  lines 193 to 213. Each time the version changes it asks "is the switch
  on?" and if yes calls `runGenerate()`. It never asks "does this version
  already have a note?" That missing question is the bug.
- The messenger: `frontend/packages/core/src/aiSuggestionManager.ts`,
  `generateSuggestion` at line 191. It always sends the request; its
  existing-note logic only runs after the answer, to decide what to show.

I found them by searching the frontend for the issue's words ("version
change"), which led to the checkbox label; the checkbox's variable name
led to the watcher, the only other file that reads it; the watcher's call
led to the messenger.

1. Clone the repo and run `npm install` from `frontend/`.
2. Add the test file `frontend/packages/app/src/hooks/useAISuggestion.test.tsx`
   (check out branch `fix/127-regenerate-only-when-no-note` from my fork,
   commit be6ab59). The test fakes the API and the suggestion manager,
   pre-loads settings with `regenerate_on_version_change: true`, and acts
   out the story: open version A (expect 0 generate calls), pretend a
   note was generated for A, switch to B (expect exactly 1 call, for B),
   switch back to A (expect the count to stay at 1).
3. From `frontend/packages/app`, run `npx vitest run src/hooks/useAISuggestion`.
4. Observed result: steps 1 to 3 of the test pass; the last check fails
   with "expected spy to be called 1 times, but got 2 times." Going back
   to A asked the AI a second time even though A already had a note.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/Christian101GTZ/dna/commit/be6ab59f66adaa0e9ffa896dd1746fb8b8cf93b7
- **Screenshots/logs:** copied from my terminal:

```
FAIL  src/hooks/useAISuggestion.test.tsx > useAISuggestion: regenerate on version change (issue #127) > does not generate again when switching back to a version that already has a note
AssertionError: expected "spy" to be called 1 times, but got 2 times
 ❯ src/hooks/useAISuggestion.test.tsx:141:52
    140|     rerender({ versionId: 'A' });
    141|     expect(aiSuggestionManager.generateSuggestion).toHaveBeenCalledTimes(1);

Test Files  1 failed (1)
     Tests  1 failed (1)
```

- **My findings:** the bug is real on today's main. With the setting on,
  every version change asks the AI to write, including a change back to
  a version that already has a note in the current session. The new
  result is added to that version's note history and the screen jumps to
  it, so it feels like the old notes were replaced. The code confirms it:
  the watcher has no existing-note check and the messenger always sends.
  Not yet checked: the running app in a browser, which needs Docker and
  an AI key; I plan to do that next and record a GIF, since the issue
  asks for one on UI changes. Also noted: the sentence under the checkbox
  ("Automatically regenerate the AI note when switching to a different
  version in review.") describes today's behavior, not the desired one,
  so it changes with the fix.

**Toolkit**

- Tool: repro-check, in live mode with the new wild scope. I graded
  claim-draft.md alone first (step 7), then claim-draft.md and
  repro-draft.md together (step 12), against the real issue.
- What it produced: accept on the claim alone. On the full package, a
  first verdict of reject on Reproducible steps: my report told a
  stranger to run a test file that existed only on my disk, so they would
  get "no test files found." After I fixed that, accept on all five
  checks.
- Difficulties: the reject was the tool doing its job, not a flaw. One
  thing it kept repeating as a voice note is that my plain-words
  explanations ("a test is a tiny program") are more than a maintainer
  needs; that is true for a posted comment, but this report is never
  posted, so I kept the style for the story and will trim it in the PR.
- What changed: I pushed the test to branch
  fix/127-regenerate-only-when-no-note in my fork (commit be6ab59, signed
  off as dna requires) and pasted the full test file into the report,
  with both ways to get it. Nothing changed in the tool itself.

---

## Solution Approach

**Analysis**

The problem comes from useAISuggestion.ts. Its version-change effect (lines 193
to 213) checks whether the "Regenerate notes on version change" setting is
enabled, but it never checks whether the version already has notes stored in
notesByVersionId. Because of this, switching back to a version that already has
notes still calls runGenerate() and creates another AI note. SettingsModal.tsx
only displays the setting and its description, and aiSuggestionManager.ts sends
the request whenever the hook tells it to; its own memory of the latest note is
private and only used after the server answers. So the problem starts in the
hook, because it does not look at the existing notes before triggering
generation.

**Proposed Solution**

Most of the work goes in useAISuggestion.ts. In the version-change effect, before
calling runGenerate(), I will check whether notesByVersionId already holds a note
for the version being switched to, and skip generation if it does. Switching to
a version with no note still generates, as today. I will also update the
description text in SettingsModal.tsx so it clearly says that notes are only
generated for versions that do not already have one.

What I am deliberately not changing: aiSuggestionManager.ts (the shared core
package; changing it would affect every caller), the manual Regenerate button
(it must always send, with requireFresh), and the regenerate_on_transcript_update
setting. The hook has one caller, AssistantPanel.tsx, so the change reaches one
screen. One known limit, predicted from reading the code and not yet run:
notesByVersionId is session memory, so after a page reload the first switch to a
version should generate once. I will check that in the running app, and I will
ask the maintainer in the PR whether they want the check to use a persisted note
instead.

**Implementation Plan**

1. On the existing branch fix/127-regenerate-only-when-no-note, add a check in
   the version-change effect of useAISuggestion.ts: if notesByVersionId has a
   note for the new version, do not call runGenerate(). Add notesByVersionId to
   the effect's dependency list.
2. Update the description text in SettingsModal.tsx to: "When you switch to a
   version that has no AI note yet, generate one automatically. Versions that
   already have a note are left as they are."
3. Keep the reproduction test in useAISuggestion.test.tsx and format it with
   Prettier.
4. Run the test and confirm it goes from failing to passing: its last check,
   that generateSuggestion is not called again on the switch back to a version
   that already has a note, currently fails with "expected 1 times, but got 2
   times". Run the full frontend tests to confirm nothing else broke.
5. Run the manifest checks (tests, Prettier on the new file) and record the
   evidence in test_evidence.md, plus a GIF of the behavior in the running app.
6. Sign the EasyCLA, sign off every commit with git commit -s, and bring the
   branch up to date with dna's main before opening the PR.

Deviations: none yet. Recorded here as they happen.

**Toolkit**

- Tool: plan-check, in live mode with the wild scope, run on plan.md (a
  copy of this section) and plan-comment.md (a short comment written as
  if for the maintainer, never posted).
- What it produced: a first verdict of reject with five checks passing
  and the evidence check unclear: my comment said "reproduced this with
  a test" but showed no steps and no observed result, and nothing is
  posted on the thread (Unit 6 said not to post the repro). Second run:
  accept on all six checks. It also noted that the issue's third
  acceptance criterion (setting off, nothing generates) is not covered
  by my test yet, and that my course practice of disclosing AI help was
  missing from the comment.
- Difficulties: the reject was fair, not a tool flaw. Two procedure gaps
  it reported about itself: its first step looks for a "Repo:" line that
  the wild scope file does not have, and it does not say whether
  direction written in the issue body counts as thread direction.
- What changed: in the comment, I folded in the test's steps, the branch
  and commit, and the observed "expected 1 times, but got 2 times"; I
  marked the page-reload point as a prediction with how I will check it
  (my own "mark predictions as predictions" rule); and step 4 of the
  plan now names the exact assertion. In the tool, nothing yet; the two
  procedure gaps go on the list for the Unit 9 portfolio.

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
