# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

Christian101GTZ

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38#issuecomment-5986182110

Here's my plan for this issue, based on [my reproduction](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38#issuecomment-5882611910).

**What's missing**

`api/middleware/auth.py` (the code that checks the login token on every protected request) has no tests, and `tests/integration/` only has an empty `__init__.py`. So nothing today would catch a change that lets a bad token through.

In my repro on `main` @ `2f4e82f`, all four cases the issue lists already come back as 401:

| Case | What I sent | Result |
|---|---|---|
| No header | no `Authorization` header | 401 |
| Malformed token | `Bearer not.a.real.token` | 401 |
| Expired token | signed with the app's real secret, `exp` 5 minutes in the past | 401 |
| Wrong secret | valid `exp`, signed with a different secret | 401 |

So these tests lock in behavior that already works. They don't fix a bug.

**What I'll change**

One new file: `tests/integration/test_auth_middleware.py`. Nothing else.

- It uses FastAPI's `TestClient` on the real app (`api/main.py`) and a real protected route, so each request goes through the real middleware the way a client would. (rose413's run used a stand-in app with a fake `/protected` route; going through the real routes also proves the middleware is wired to them.)
- The test class is marked `@pytest.mark.integration`, the marker `make test-integration` runs. rose413 asked whether that marker fits, since these tests don't need the database. I'm keeping it because the issue names `tests/integration/` and CI's `test-integration` job is where they'd run; that job starts Postgres anyway, so nothing breaks.
- The client fixture lives in the test file for now, since only one module needs it. If a second integration module shows up, it's a small move to a shared `conftest.py`.
- Four tests, one per case: `test_missing_authorization_header`, `test_malformed_token`, `test_expired_token`, `test_wrong_signing_secret`.
- Each test calls `GET /profiles/00000000-0000-0000-0000-000000000000` and checks two things: status `401`, and the header `WWW-Authenticate: Bearer`. My repro only checked the status; rose413's run above showed the header on all four cases, so I'm checking it too.
- The expired and wrong-secret tokens are built inside the test with `jose.jwt.encode`, using the app's own `settings.secret_key` and `settings.jwt_algorithm`, so they match whatever the app is configured with.

**What I won't change**

- `api/middleware/auth.py`, `core/security.py`, and `tests/conftest.py`: no edits.
- I'm not checking the error message text. In my repro, the expired token returned `"Invalid authentication credentials"`, not the `"Token has expired"` message that's in `auth.py`. From reading the code, I think that message can't be reached, because `jose` rejects the expired token first. I haven't traced every path, so I'll raise it as a separate note instead of changing it here.
- No test for a valid token, and none for the two other paths shreyaadani mentioned (deleted user → 401, database error → 500). All three need a database or session fixture, and none are among the four cases the issue names. Good follow-ups, not this change.

**How I'll test it**

- Before, on `main`: `python -m pytest tests/integration -v -m integration` → `collected 0 items`.
- After, on my branch: the same command → `4 passed`.
- To make sure the tests can actually fail, I'll change one expected status to `200`, confirm that test fails, then put it back.
- `make lint` and `make typecheck`, with no new findings.

**Not sure yet**

I ran my repro with Postgres running. I expect the tests to pass without it, for two reasons: none of the four cases reach the database (every bad token is rejected before the user lookup, which rose413's `db?` column also showed), and the app's startup hook in `api/main.py` logs a database error and continues instead of failing. I haven't tested that yet, so I'll run the suite once with Docker stopped and post the result.

Branch: `fix/38-auth-middleware-tests` on my fork.

---

## Your branch

**Branch**

fix/38-auth-middleware-tests

**Evidence**

My Unit 2 repro step was a pytest run of the new test file. Below I use the directory form the
Makefile uses (`tests/integration -v -m integration`), so the "before" can be measured on
`main`, where the file doesn't exist yet. (shreyaadani's comment on the issue showed that
`collected 0 items` result first; every run below is my own.) Docker (Postgres + Redis) was
off unless the heading says otherwise.

Before, on `main` @ `2f4e82f`, with no test file in `tests/integration/`:

```
$ .venv/Scripts/python -m pytest tests/integration -v -m integration
platform win32 -- Python 3.12.10, pytest-9.1.1, pluggy-1.6.0
rootdir: C:\Users\chris\Desktop\AI_301\pathreview-ai301-fa26-s3
configfile: pyproject.toml
collected 0 items

================= no tests ran in 0.19s =================
```

After, first attempt on the branch (fixture used `with TestClient(app)`, which runs the app's
startup hook). This is the run that showed my plan's Postgres prediction was wrong:

```
$ .venv/Scripts/python -m pytest tests/integration -v -m integration
collected 4 items

tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_missing_authorization_header ERROR [ 25%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_malformed_token ERROR              [ 50%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_expired_token ERROR                [ 75%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_wrong_signing_secret ERROR         [100%]

api\main.py:88: in startup_event
    await init_db()
core\database.py:44: in init_db
    async with engine.begin() as conn:
...
E       ConnectionRefusedError: [WinError 1225] The remote computer refused the network connection
---- Captured stdout setup ----
2026-10-04 17:59:23 [error    ] application_startup_failed     error='[WinError 1225] The remote computer refused the network connection'

================= 3 warnings, 4 errors in 6.68s =================
```

After, final (the `client` fixture returns `TestClient(app)` without a `with` block, so the
startup hook doesn't run), still with Docker off:

```
$ .venv/Scripts/python -m pytest tests/integration -v -m integration
platform win32 -- Python 3.12.10, pytest-9.1.1, pluggy-1.6.0
rootdir: C:\Users\chris\Desktop\AI_301\pathreview-ai301-fa26-s3
configfile: pyproject.toml
collected 4 items

tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_missing_authorization_header PASSED [ 25%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_malformed_token PASSED              [ 50%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_expired_token PASSED                [ 75%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_wrong_signing_secret PASSED         [100%]

================= 4 passed, 3 warnings in 0.72s =================

$ .venv/Scripts/ruff check tests/integration/test_auth_middleware.py
All checks passed!

$ .venv/Scripts/black --check tests/integration/test_auth_middleware.py
All done! ✨ 🍰 ✨
1 file would be left unchanged.
```

Same command with Docker up (this matches CI's `test-integration` job, which starts Postgres):

```
$ docker compose up -d
 ✔ Container pathreview-ai301-fa26-s3-redis-1     Started
 ✔ Container pathreview-ai301-fa26-s3-db-1        Started
 ✔ Container pathreview-ai301-fa26-s3-vector-db-1 Started

$ .venv/Scripts/python -m pytest tests/integration -v -m integration
collected 4 items
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_missing_authorization_header PASSED [ 25%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_malformed_token PASSED              [ 50%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_expired_token PASSED                [ 75%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_wrong_signing_secret PASSED         [100%]
================= 4 passed, 3 warnings in 0.82s =================
```

Deliberate-fail check from my test plan (changed the shared assert to expect `200`, ran, then
put it back to `401` before committing):

```
$ .venv/Scripts/python -m pytest tests/integration -v -m integration
collected 4 items
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_missing_authorization_header FAILED [ 25%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_malformed_token FAILED              [ 50%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_expired_token FAILED                [ 75%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_wrong_signing_secret FAILED         [100%]
E       assert 401 == 200
E        +  where 401 = <Response [401 Unauthorized]>.status_code
================= 4 failed, 3 warnings in 0.87s =================
```

The 3 warnings in every run are pre-existing deprecation warnings in `core/config.py` and
`api/main.py`, not from the new file. The commit's pre-commit hooks (ruff, black, mypy) all
passed.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Run 1, full run with `--save-run`: **18/20**. Output: `categories: clear-accept 5/7
   scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4` /
   `agreement: 18/20 scored items  (bar: 18/20: PASS)`. The two misses were `pkg-13` and
   `pkg-14`, both clear-accept packages my rubric rejected. The table said `failed: approach`
   for pkg-13 and `failed: evidence, approach` for pkg-14.
2. Partial run, `--only pkg-13,pkg-14 --out results-why.json`, to read the grader's reasons:
   pkg-13 came back accept on its own with no file changes (so it sits right on the edge of
   my `approach` check), and pkg-14 still failed `approach` with the reason "No specific file
   or function named; 'exact functions to be pinned in the PR after tracing the query
   issuance with debug logs', and 'consuming or draining' left open." Its `evidence` check
   passed this time.
3. Revised the `approach` row (and the matching Executability section of the evidence guide
   and step 4 of Evidence gathering in the procedure) so a named function or code path
   counts, and a stated unknown inside a named place is not a fail. Partial run with
   canaries, `--only pkg-13,pkg-14,pkg-10,pkg-17,pkg-18`: 5/5. pkg-13 and pkg-14 flipped to
   accept; the three unbuildable canaries still rejected, and each still failed `approach`
   itself.
4. Run 2, full run with `--save-run`: **20/20**. Output: `categories: clear-accept 7/7
   scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4` /
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`. This is the run in `eval-run.txt`.

**Package analysis**

`pkg-14` (zellij-org/zellij#5174, category clear-accept). In Run 1 my rubric said **reject**;
the gold label is **accept**. The deciding check was `approach`, and the grader's reason was:
"No specific file or function named; 'exact functions to be pinned in the PR after tracing the
query issuance with debug logs', and 'consuming or draining' left open."

My rubric read it that way because the original `approach` row said the plan must name "the
file(s) it will edit" and told the grader to flag words that leave a decision open. pkg-14
names a component, not a file ("the client attach path in `zellij-server`'s client connection
handling"), and it says the exact functions will be pinned while tracing. My check treated
that honesty about a detail the same as pkg-18's "upstream or vendored, whichever is easier",
which is a real unchosen strategy. The gold note itself calls pkg-14 "arguable on the
deferral", so it's one of the borderline ones. After the revision, Run 2 accepted it with the
reason "Names the client attach path in zellij-server and commits to draining OSC responses
before pane input is wired; exact functions are a stated unknown within that place."

**Check rationale**

Quoting the `approach` row from `tools/plan-check/rubric.md` exactly as it reads now:

"passes if the plan names where it will edit (a file, a function, or a specific named code path
or component, e.g. "the reattach path in `zellij-server`'s client connection handling") and
commits to one specific change there, so a stranger could start without making any decision
first; a stated unknown about the exact function or line inside that named place is fine;
fails if no place is named, if the plan is to "investigate", "profile", or "poke around"
before choosing a change, or if it leaves a choice between different places or strategies
open ("upstream or vendored, whichever is easier", "gocui? tcell?", "recover() somewhere")"

It reads that way because the first version said "passes if the plan names the file(s) it
will edit and commits to one specific change in them" and failed any plan that "leaves a
choice open". That wording couldn't tell "I haven't decided what to do" (pkg-17, pkg-18)
from "I've decided what to do, and I'm honest that the exact line may move" (pkg-13, pkg-14).
So I changed three things: the place can be a file, a function, or a named code path; a stated
unknown inside that place is explicitly fine; and the fail condition is a choice between
*different* places or strategies, with the real quotes from pkg-17 and pkg-18 as examples. I
rejected the other option, keeping the file-path rule and accepting 18/20, because the same
strictness grades my own plan in live mode, and it punishes exactly the honesty the evidence
guide's Honesty section asks for.

**Trade-offs**

Before the confirming full run I re-ran the three unbuildable packages as canaries with
`--only pkg-13,pkg-14,pkg-10,pkg-17,pkg-18`. All three still rejected, and each still failed
`approach` on its own: pkg-10 "No place named to edit; steps are 'Profile', 'Investigate',
'Look into', 'Optimize whatever the profiling turns up'", pkg-17 "No edit location named;
'gocui? tcell? not sure which layer' and 'Investigate' leave the choice open", pkg-18
"'recover() somewhere' and 'either upstream or in the vendored copies, whichever turns out to
be easier' leave place and strategy open". Each of them also fails four or five other checks,
so no verdict rested on `approach` alone.

What the looser check gives up: a plan can now name a component and call its unchosen fix a
"stated unknown" ("exact function TBD") and pass `approach`. The check depends on the grader
telling an unknown detail apart from an undecided fix, and pkg-13 flipping between Run 1 and
the partial run with the same files shows that line has some grader variance. I accept that
miss because the other checks (`diagnosis`, `scope`, `test`) catch a plan that is vague all the
way through, as the canaries show. One more thing I left alone on purpose: the live runs on my
own plan reported five procedure gaps (how to identify maintainers live; that
`CONTRIBUTING.md` lives at `docs/CONTRIBUTING.md`; how to treat evidence from a peer
contributor's repro that the plan leans on; how the Deviations section gets graded; and
whether an issue body written by a collaborator counts as "direction" when no collaborator
commented in the thread).
Fixing them would change the fingerprints in `eval-run.txt`, so they stay as known gaps for
next unit. The live run also caught two wording problems in my plan (a follow-up comment
claimed before it was posted, and a "before" step credited to my Unit 2 repro when it used a
different pytest form), which I fixed before submitting.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
