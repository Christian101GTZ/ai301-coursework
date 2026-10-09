# Plan: integration tests for auth middleware edge cases (#38)

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38
My repro comment: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38#issuecomment-5882611910

## Diagnosis

This issue is a missing-test gap, not a broken behavior. The cause of
the gap: `get_current_user` in `api/middleware/auth.py` has no tests
at any level, and `tests/integration/` holds only an empty
`__init__.py`, so nothing would catch a change that let one of the
four bad-token cases through.

My repro shows the middleware already rejects all four cases
correctly on `main` @ `2f4e82f`, so the tests lock in current
behavior rather than fix a bug:

```
no Authorization header  -> 401
malformed token          -> 401
expired token            -> 401   body: {"detail":"Invalid authentication credentials"}
wrong signing secret     -> 401
```

And the draft test file, run against the real app:

```
$ .venv/Scripts/python -m pytest tests/integration/test_auth_middleware.py -v
... test_missing_authorization_header PASSED
... test_malformed_token PASSED
... test_expired_token PASSED
... test_wrong_signing_secret PASSED
4 passed, 3 warnings in 0.86s
```

Reading the code backs this up: every failure path raises
`credentials_exception` (`auth.py` lines 27-31, status 401 with
`WWW-Authenticate: Bearer`), or FastAPI's `OAuth2PasswordBearer`
raises its own 401 with the same header when the header is missing.
All four cases stop before the database lookup at line 59, so the
tests do not need a real user in the database. rose413's run in the
thread confirms this independently: their `db?` column was `no` for
all four failure cases and `yes` only for the valid-token control.

One difference from rose413's run: theirs used a stand-in FastAPI app
with a fake `/protected` route, and they noted they had not run it
through the real route modules. My tests go through the real app
(`api/main.py`) and a real protected route (`/profiles/{id}`), which
is what the issue asks for and also proves the middleware is wired to
the routes.

## Scope

In scope: one new test file covering exactly the four cases the issue
names: expired token, malformed token, no `Authorization` header, and
a token signed with a different secret.

Not in scope:
- Any change to `api/middleware/auth.py` or `core/security.py`. In
  particular, the "Token has expired" message at `auth.py` lines
  44-49 looks unreachable (jose already rejects expired tokens in
  `decode_access_token`, so the generic message is returned instead).
  That is a separate behavior question; I'll raise it as its own
  follow-up note, not fix it here.
- Changes to `tests/conftest.py` (the client fixture stays in the new
  file).
- Asserting the `detail` message text, so these tests don't lock in
  the unreachable-message behavior above.
- A valid-token / authenticated-user test (needs a seeded user; not
  one of the four cases).
- The two other middleware paths shreyaadani mentioned in the thread:
  the deleted-user 401 (`auth.py` line 68-69) and the DB-error 500
  (lines 61-66). Both need a database or session fixture and are not
  among the four cases the issue names. Good follow-ups, not this
  change.
- The `integration` marker definition in `pyproject.toml` line 180
  ("require Docker services"). rose413 asked whether these tests
  belong under it since they don't need the database. I'm keeping
  them in `tests/integration/` with the `integration` marker because
  the issue names that path and CI's `test-integration` job is where
  they'd run; that job starts Postgres anyway, so nothing breaks.

## Files I'll touch

- `tests/integration/test_auth_middleware.py` (new). No other files.

## Approach

1. Create a branch `fix/38-auth-middleware-tests` from `main` (the
   `fix/<issue-number>-<slug>` form the course house rules ask for).
2. In `tests/integration/test_auth_middleware.py`, add a module-scoped
   `TestClient(app)` fixture using the real app from `api/main.py`,
   and a `TestAuthMiddlewareEdgeCases` class marked
   `@pytest.mark.integration` (the marker `make test-integration`
   selects). The fixture stays in this file rather than
   `tests/conftest.py` because only one module needs it right now;
   if a second integration module appears, it's a small move to a
   shared `tests/integration/conftest.py`.
3. Add one test per case, each calling the protected endpoint
   `GET /profiles/00000000-0000-0000-0000-000000000000` and asserting
   `status_code == 401` and `headers["www-authenticate"] == "Bearer"`:
   - no `Authorization` header
   - `Bearer not.a.real.token`
   - a token signed with `settings.secret_key` whose `exp` is 5
     minutes in the past
   - a token with a valid `exp` signed with `"a-totally-different-secret"`
4. Build the tokens in the tests with `jose.jwt.encode` and
   `settings.jwt_algorithm`, so they follow the app's real config.
5. Run `make lint`, `make typecheck`, and the integration suite; add a
   Google-style docstring to the module and class per CONTRIBUTING.
6. Commit as `test(api): add integration tests for auth middleware
   edge cases` with `Closes #38`.

## Test plan

Re-run my Unit 2 pytest step on `main` and on the branch. My Unit 2
run pointed pytest at the test file directly; here I use the directory
form the Makefile uses (`tests/integration -v -m integration`) so the
"before" can be measured on `main`, where the file doesn't exist yet.
shreyaadani's comment in the thread showed that `collected 0 items`
result first; the before-run I record is my own.

- Before (on `main`, no test file):
  `python -m pytest tests/integration -v -m integration` collects
  0 items (no auth middleware tests exist).
- After (on the branch): the same command collects 4 tests and shows
  `4 passed`: `test_missing_authorization_header`,
  `test_malformed_token`, `test_expired_token`,
  `test_wrong_signing_secret`, each asserting 401 and
  `WWW-Authenticate: Bearer`.
- Sanity check that the tests can fail: temporarily change one
  expected status to 200 and confirm that test fails, then revert.
- `make lint` and `make typecheck` report no new findings in the new
  file.

## Risks and unknowns

- Running without Postgres. Locally I ran with Postgres up. Two
  things say the tests should pass without it: none of the four
  cases reach the database (line 59 is after every raise, and
  rose413's `db?` column confirms it), and the app's startup hook
  logs a database error and continues instead of failing
  (`api/main.py` lines 84-91). I have not tested that yet, so I'll
  run the suite once with Docker stopped and record the result. CI's
  `test-integration` job starts Postgres regardless, so CI matches my
  local run either way.
- `datetime.utcnow()` / `utcfromtimestamp` in `auth.py` emit
  deprecation warnings (the "3 warnings" in my run). They don't
  affect results, and I'm leaving them alone (out of scope).
- Other students have plans on this issue; this plan and branch are
  my own work from my own repro.

## Deviations

1. **The Postgres-down prediction was wrong, and the client fixture
   changed because of it.** This plan said the startup hook "logs a
   database error and continues." It logs and then re-raises
   (`api/main.py` line 92 is a bare `raise`). First run with Docker
   stopped: all four tests errored at fixture setup with
   `ConnectionRefusedError`, right after the hook logged
   `application_startup_failed`. Fix: the `client` fixture now returns
   `TestClient(app)` without a `with` block, so the app's startup event
   never runs. The middleware under test never needs the database, so
   nothing is lost. With that one change and Docker still stopped:
   `4 passed`. Nothing outside the test file changed, so the scope
   held. I posted a follow-up on the issue with the real result, since
   my plan comment promised one:
   https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38#issuecomment-5986902992
2. **Two small helpers the plan didn't list**, `_make_token()` and
   `_assert_unauthorized()`, inside the same test file. They remove
   copy-paste between the four tests. Same file, same four tests, same
   two asserts per test.

3. **A wrong guess in Risks and unknowns, corrected:** I wrote that the
   "3 warnings" in my pytest runs come from `utcnow()` in `auth.py`.
   The pytest output names the real sources: `core/config.py` line 7
   (a Pydantic deprecation) and `api/main.py` line 84 plus FastAPI's
   `on_event` (a lifespan deprecation). None come from the new test
   file. Nothing in the build changed because of this; the note is
   here so the plan is true.

Everything else matches the plan: one new file, the four named tests,
`401` plus `WWW-Authenticate: Bearer`, no message-text asserts, and no
edits to `auth.py`, `core/security.py`, or `tests/conftest.py`.
