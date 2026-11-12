I fixed a bug in DNA, an open-source tool for reviewing film shots, where switching back to a version you'd already seen generated a new AI note every time. I opened the pull request with five tests and a short screen recording, the maintainer approved it the same day with one small comment, and it merged three days later.

DNA has an AI helper that drafts review notes. Every extra note it wrote was a real AI request, so the fix saves cost and keeps the note the reviewer already had.

Two things I'd keep from this one. I wrote a test that proved the bug before I changed any code, so I knew exactly when it was fixed. And a second bug only showed up when I ran the real app, not in the tests. Next time the app runs on day one.

The fix is live in DNA's main branch: https://github.com/AcademySoftwareFoundation/dna/pull/260

#OpenSource #React #TypeScript #CodePath
