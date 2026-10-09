# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/117

**Branch**

fix/38-auth-middleware-tests

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Run 1, full run with `--save-run`: **18/20**. Output: `categories: clear-accept 5/7
   not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3` /
   `agreement: 18/20 scored items  (bar: 18/20: PASS)`. The two misses were `pkg-05`
   (`failed: evidence, checks`) and `pkg-08` (`failed: checks`), both clear-accept packages
   my rubric rejected.
2. Partial run, `--only pkg-05,pkg-08 --out results-why.json`, to read the grader's reasons.
   Both failed the same way again. pkg-05 `checks`: "'cargo test -p nu-protocol passes (312
   tests)' is a named result, but 'fmt and clippy clean' is a bare claim with no command or
   output." pkg-05 `evidence`: the plan's second case "has no command or output, only the
   bare line 'Same-key redefine prints the one-time warning'." pkg-08 `checks`: "no
   gofmt/lint output is shown (only the claim 'code formatted'), and 'go test ./... passes'
   is a bare claim with no output."
3. Loosened two rows: `evidence` (the issue's own case needs before/after output; any other
   test-plan case needs its observed outcome stated, or a deferral note; a case "never
   mentioned at all" fails) and `checks` (a repo check named with its outcome counts; pasted
   output not required). Matching edits in the evidence guide and procedure. Canary run,
   `--only pkg-05,pkg-08,pkg-04,pkg-07,pkg-10,pkg-14,calib-04 --include-calibration`:
   6/7. pkg-05 flipped to accept; the four not-tested packages and calib-04 still rejected;
   pkg-08 still failed `checks`, now because "Template's 'code formatted' check is never
   named in the test evidence (only go test, go generate, and the integration test appear);
   formatting is claimed only in the description."
4. Widened `checks` to read the description's checklist or requirements section as well as
   the test evidence. Canary run, `--only pkg-08,pkg-14,pkg-10`: 3/3.
5. Run 2, full run with `--save-run`: **20/20**. Output: `categories: clear-accept 7/7
   not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3` /
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`. This is the run in `eval-run.txt`.

**Package analysis**

`pkg-08` (jesseduffield/lazygit#5883, category clear-accept). In Run 1 my rubric said
**reject**; the gold label is **accept**. It took two revisions to agree, and the grader's
reasons show exactly how my `checks` check was reading the package.

First reason (Run 1): "Repo checklist asks for formatted code but no gofmt/lint output is
shown (only the claim 'code formatted'), and 'go test ./... passes' is a bare claim with no
output." My original row said a check passes only if "its output or result is shown". The
grader treated "go test ./... passes" and "code formatted" as bare claims because no pasted
output sat under them. The gold note accepts the package on "integration test added,
lazygit checklist", so a named check with a stated outcome was meant to be enough.

Second reason (after the first loosening): "Template's 'code formatted' check is never named
in the test evidence (only go test, go generate, and the integration test appear);
formatting is claimed only in the description." My row told the grader to look only in the
test-evidence section, but pkg-08 states "code formatted" in its description's Requirements
line, which is where lazygit's PR template puts that checklist. So the second fix widened
where the check looks. After that, the canary run accepted pkg-08 with this `checks`
evidence: "Description requirements and test evidence name cheatsheets (go generate, no
diff), code formatted, integration test added, i18n, no UserConfig changes; 'go test ./...'
passes."

**Check rationale**

Quoting the `checks` row from `tools/pr-precheck/rubric.md` exactly as it reads now. Its
Evidence column:

"the test evidence and the description's checklist or requirements section, read against the checks the repo asks for (the PR template's checklist, the contribution docs, or the repo-facts block)"

Its Pass condition:

"passes if every check the repo names (unit tests, lint, type check, build, or similar) is named with its outcome in the test evidence or in the description's checklist or requirements section (a command or check name plus pass/fail/clean, a ticked checklist item, a test count, or pasted output); pasted output is not required, and a failing check reported with its reason counts; if the repo names no checks, this passes; fails if a named check is never mentioned, or the evidence offers only a blanket claim that names no specific check ("CI is green", "all tests pass")"

It reads that way because of two revisions. The first version's Evidence column was "the
test evidence, read against the checks the repo asks for", and its pass condition said a
check "was run and its output or result is shown ... fails if a named check was never run,
or is only claimed ("CI is green", "all tests pass") with no output or named result". That
failed pkg-05 and pkg-08, two honest PRs that name every check with its outcome ("fmt and
clippy clean", "go test ./... passes") without pasting output. The first revision made a
named outcome enough and moved the fail line to "never mentioned" or a blanket claim that
names no specific check. The second revision added the description's checklist or
requirements section to where the check looks, because a repo's PR template checklist is
where that repo asks for the statement in the first place. I rejected the other option,
keeping the pasted-output rule and accepting 18/20, because the same rule would grade my own
PR, where I report `make test-unit` by its summary line rather than 428 lines of output, and
it treats honest brevity as if it were hiding something.

**Trade-offs**

Before each confirming run I re-ran canaries with `--only`. After the first loosening:
`--only pkg-05,pkg-08,pkg-04,pkg-07,pkg-10,pkg-14,calib-04 --include-calibration`; all four
not-tested packages and the calib-04 "silent skip" trap still rejected. After widening
`checks` to the description: `--only pkg-08,pkg-14,pkg-10`; pkg-14 ("Tests added; all tests
pass" in its description) and pkg-10 ("verified working on my machine for a full day") still
rejected, because `evidence` holds them no matter what `checks` does.

What the looser `checks` gives up: a PR can now tick a template checklist item ("code
formatted") in its description with no output anywhere, and `checks` will count it. I accept
that because `checks` was never the row that catches untested PRs; `evidence` is, and it
still demands before/after output for the issue's own failing case. The case I know it will
miss: a PR whose checklist is ticked truthfully but whose repro evidence is thin has to be
caught by `evidence` alone.

Two gaps I left alone on purpose, so the fingerprints in `eval-run.txt` stay valid: the live
run on my own PR reported that the procedure doesn't say what to do when the `gh` CLI is
missing (it fell back to a web summary of the issue), and doesn't say how to grade a
template box left unticked with a stated reason. The live run also caught two real problems
in my draft before I opened the PR: a Changes bullet that said both tokens use
`settings.secret_key` (the wrong-secret token doesn't), and two checks CONTRIBUTING names
(black and the frontend job) that I hadn't run. I fixed the bullet, ran both, reported the
frontend's pre-existing timeout honestly, and the re-run came back accept. After opening,
CI showed the opposite picture from my machine: typecheck and frontend green, and
test-integration red because `ci.yml` sets `DATABASE_URL` without the `+asyncpg` driver, a
pre-existing workflow bug my PR was the first to reach. I reported it on the PR with the
root cause rather than changing shared CI config inside a one-file test PR.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
