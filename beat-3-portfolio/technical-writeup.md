# How I stopped DNA from rewriting AI notes it already had

## Overview
DNA (Dailies Notes Assistant) is an open source tool from the Academy Software Foundation that helps film review teams write notes on shot versions. It has an AI helper that drafts a note for the version on screen, and a setting called "Regenerate notes on version change." With that setting on, going from version A to B and back to A asked the AI to write A's note again, even though A already had one. Each extra switch cost a real AI request and added another note to A's history. I reproduced the bug in a failing test, changed one React hook and the setting's description, and added five tests. The fix merged in [PR #260](https://github.com/AcademySoftwareFoundation/dna/pull/260) after one round of review and closed issue #127.

## What happened
**Picking the issue.** Issue #127 was labeled "good first issue" and was opened by a maintainer. Someone had asked to work on it in May but never opened a PR, so on Oct 15 I commented that I was taking it, named them, and offered to step back. They never replied.

**Finding the cause.** The bug is in `useAISuggestion.ts`. The effect that runs on a version change only checked whether the setting was on. It never checked whether that version already had a note, even though the hook already kept a `notesByVersionId` map.

**Reproducing it first.** Before changing any code I wrote a test: start on A with a note, switch to B (generating once is correct), switch back to A (should not generate). The suite had 100 passing tests before I added it. The new test failed:

```
AssertionError: expected "spy" to be called 1 times, but got 2 times
 Test Files  1 failed (1)
      Tests  1 failed (1)
```

**The fix.** I wrote a plan and had my plan-check tool grade it. It rejected the first version because my evidence was unclear: I said I had reproduced the bug but showed no steps or result. After I added them it accepted all six checks, and it also noticed I had no test for the setting-off case. The fix itself was a few lines in the effect:

```ts
const versionChanged =
  prevVersionRef.current !== null && prevVersionRef.current !== versionId;
const hasNote =
  versionId != null && (notesByVersionId[versionId]?.length ?? 0) > 0;

if (versionChanged && !hasNote) {
  runGenerate().catch(() => {});
}
```

I also changed the setting's description so it matches the new behavior. I put the fix in the hook rather than the shared `aiSuggestionManager.ts`, because the manual Regenerate button depends on that manager always sending.

**A second bug I almost missed.** When I finally ran the real app, I found a gap my tests didn't cover. If a note finished generating after you had already switched to another version, the listener threw it away, so coming back generated again. I added a test, and it failed on the old code:

```
× keeps a note that finishes after the user has switched away
AssertionError: expected null to be 'Late note for A'
 Tests  1 failed | 4 passed (5)
```

I fixed the listener to store the note under the version it belongs to, then opened the PR with the full suite passing (105 app tests, 140 core tests). Looking back, I should have run the app earlier. This case only showed up there.

**Checks before review.** The ship-gate, a check I installed and configured with my own manifest, blocked my PR three times before it passed. Local `main` was a week behind, so it saw 88 changed files. Then it caught a double blank line and trailing whitespace.

**Review.** I opened the PR on Oct 30 at 1:08 PM PT, and the maintainer, jspada200, reviewed it 3 hours later. He approved it with one request on a code comment I had added: "Superfluous comment. Feel free to remove." I removed it, re-ran the tests, pushed, and replied. He merged it on Nov 2 as a single squashed commit.

**Where AI helped.** Claude Code drafted the reproduction test, the scaffolding for the other tests, and edits to my comments and PR description. I typed the fix myself and made the design calls: fix it in the hook, fix the second bug before opening the PR, and leave two already-unformatted upstream files alone so the diff stayed readable.

## Limitations and what's next
- The notes map only lives for the page session. After a reload, the first switch to each version generates once. I predicted this in my plan and confirmed it in the app, and I asked about it in the PR, but nobody answered.
- I tested only with mock data, where every note read "No transcript available." How it behaves with real transcripts is an expectation, not something I observed. I said so in the PR.
- The effect reads `notesByVersionId`, and the work it triggers also writes to it. Course feedback pointed out this pattern can re-fire in surprising ways. I left it, since the five tests pass, but I'd want to derive the condition from something that only changes with the version.
- I bundled the listener fix into the same PR. Feedback said it would have been cleaner as its own PR. I stated the bundling in the description.

Next, I'd like to offer a follow-up PR for something I found along the way. DNA's formatting workflow never runs, because the `paths` filter in `.github/workflows/frontend-formatting.yml` uses brace patterns that don't match. I offered this in the PR and haven't had a reply yet.
