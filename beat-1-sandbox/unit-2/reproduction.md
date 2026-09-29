# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

Christian101GTZ

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38#issuecomment-5882440427

I'd like to work on this issue. I plan to add integration tests for the four cases (expired
token, malformed token, missing `Authorization` header, and a token signed with a different
secret) and share what I find along the way.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/38#issuecomment-5882611910

**Environment:** Windows 11 Home, version 25H2 (build 26200.9457). Python 3.12.10. Fork of
`codepath/pathreview-ai301-fa26-s3`, `main` branch, commit `2f4e82f`. Working tree has one new
untracked file, `tests/integration/test_auth_middleware.py` (the test file this issue asks
for, added below); no other changes. Postgres + Redis started with `docker compose up -d`;
app started with `make run` (API on `http://localhost:8000`).

This issue asks for tests covering four cases (expired token, malformed token, missing
header, wrong signing secret), not a bug report. Expected: all four should be blocked with a
401 response. Actual: all four were correctly blocked with 401, on both a quick manual check
and the actual test file below — so this is an honest "no bug found" result for the four
named cases.

**Steps and observed, part 1 — quick check with curl against the running server:**

No Authorization header:
```
$ curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8000/profiles/00000000-0000-0000-0000-000000000000
401
```

Malformed token:
```
$ curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8000/profiles/00000000-0000-0000-0000-000000000000 -H "Authorization: Bearer not.a.real.token"
401
```

The expired and wrong-secret tokens don't exist naturally (a live login only ever issues
valid ones), so I generated them myself with the same `jose` library the app uses:

```python
from datetime import UTC, datetime, timedelta
from jose import jwt

SECRET = "dev-secret-key-change-in-production"  # settings.secret_key
ALG = "HS256"                                    # settings.jwt_algorithm
FAKE_USER_ID = "00000000-0000-0000-0000-000000000000"

expired = jwt.encode(
    {"sub": FAKE_USER_ID, "exp": datetime.now(UTC) - timedelta(minutes=5)},
    SECRET, algorithm=ALG,
)
wrong_secret = jwt.encode(
    {"sub": FAKE_USER_ID, "exp": datetime.now(UTC) + timedelta(minutes=30)},
    "a-totally-different-secret", algorithm=ALG,
)
```

Expired token (signed with the app's real secret, but already expired):
```
$ curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8000/profiles/00000000-0000-0000-0000-000000000000 -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJleHAiOjE3OTA2NDU4OTd9.sjuiO4yIXP-YUonBhhZlsR6o6fUqP_jmRDvlTFZZkwg"
401
```

Wrong signing secret (signed with `a-totally-different-secret` instead of the app's real
secret):
```
$ curl -s -o /dev/null -w "%{http_code}\n" http://localhost:8000/profiles/00000000-0000-0000-0000-000000000000 -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJleHAiOjE3OTA2NDc5OTd9.kwaTWl2bxruZ0iwijB-9Iu590d_HbWXp2DIRxAPaXcI"
401
```

Then, checking the actual response body (not just the status code) for the expired-token
case:
```
$ curl -s http://localhost:8000/profiles/00000000-0000-0000-0000-000000000000 -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJleHAiOjE3OTA2NDU4OTd9.sjuiO4yIXP-YUonBhhZlsR6o6fUqP_jmRDvlTFZZkwg"
{"detail":"Invalid authentication credentials"}
```

**Steps and observed, part 2 — the actual test file the issue asks for.** New file,
`tests/integration/test_auth_middleware.py` (not yet committed — this is the file this issue
is asking to be added), one test per case using FastAPI's `TestClient` against the real app
(`api/main.py`):

```python
"""Integration tests for authentication middleware edge cases (issue #38)."""

from datetime import UTC, datetime, timedelta

import pytest
from fastapi.testclient import TestClient
from jose import jwt

from api.main import app
from core.config import settings

PROTECTED_URL = "/profiles/00000000-0000-0000-0000-000000000000"
FAKE_USER_ID = "00000000-0000-0000-0000-000000000000"


@pytest.fixture(scope="module")
def client():
    with TestClient(app) as c:
        yield c


@pytest.mark.integration
class TestAuthMiddlewareEdgeCases:
    def test_missing_authorization_header(self, client):
        response = client.get(PROTECTED_URL)
        assert response.status_code == 401

    def test_malformed_token(self, client):
        response = client.get(PROTECTED_URL, headers={"Authorization": "Bearer not.a.real.token"})
        assert response.status_code == 401

    def test_expired_token(self, client):
        expired_token = jwt.encode(
            {"sub": FAKE_USER_ID, "exp": datetime.now(UTC) - timedelta(minutes=5)},
            settings.secret_key, algorithm=settings.jwt_algorithm,
        )
        response = client.get(PROTECTED_URL, headers={"Authorization": f"Bearer {expired_token}"})
        assert response.status_code == 401

    def test_wrong_signing_secret(self, client):
        forged_token = jwt.encode(
            {"sub": FAKE_USER_ID, "exp": datetime.now(UTC) + timedelta(minutes=30)},
            "a-totally-different-secret", algorithm=settings.jwt_algorithm,
        )
        response = client.get(PROTECTED_URL, headers={"Authorization": f"Bearer {forged_token}"})
        assert response.status_code == 401
```

Run:
```
$ .venv/Scripts/python -m pytest tests/integration/test_auth_middleware.py -v
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_missing_authorization_header PASSED [ 25%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_malformed_token PASSED              [ 50%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_expired_token PASSED                [ 75%]
tests/integration/test_auth_middleware.py::TestAuthMiddlewareEdgeCases::test_wrong_signing_secret PASSED         [100%]
==================================================== 4 passed, 3 warnings in 0.86s ====================================================
```

**Also noticed, unrelated to what this issue asks for:** while checking the expired-token
response body above, it read `"Invalid authentication credentials"` rather than the more
specific `"Token has expired"` message that appears in `api/middleware/auth.py` lines 44-49:

```python
if exp is not None and datetime.utcfromtimestamp(exp) < datetime.utcnow():
    raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Token has expired", ...)
```

Based on reading `core/security.py` lines 78-86, `decode_access_token` calls `jwt.decode(...)`
from the `jose` library, which already validates the `exp` claim itself and raises `JWTError`
for an expired token; that's caught at line 85-86 and turned into `None`. Back in `auth.py`,
that `None` triggers the earlier check at lines 35-36 (`if payload is None: raise
credentials_exception`) before the code ever reaches line 44. So the specific "Token has
expired" message looks unreachable — though I haven't traced every code path, so I'd call
this a strong likelihood based on the code and the observed output, not a certainty. This
doesn't affect the four cases this issue asks about (all four still correctly return 401);
it's a separate, smaller observation worth a follow-up note rather than a fix inside this
issue's scope.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke test (3 packages): 3/3 correct.
2. First full run: crashed — Windows couldn't find the `claude` program. Fixed it.
3. Second full run: 3 packages crashed from a different bug (an emoji broke text encoding).
   Fixed it. The rest: 15/17 correct.
4. Third full run (no crashes this time): 17/20 correct — below the 18/20 needed. 3 packages
   wrong: `pkg-03`, `pkg-05`, `pkg-12`.
5. Fixed the "Reproducible steps" check. Re-tested just `pkg-05`, `pkg-12`, plus `pkg-06` as a
   safety check: all 3 correct.
6. Fixed the "Conventions / disclosure" check. Re-tested just `pkg-03` plus `pkg-20` as a
   safety check: both correct.
7. Final full run: **19/20 correct — passed the 18/20 bar.**

**Package analysis**

`pkg-05`: my rubric said reject, the correct answer was accept. The report described making
a test file ("a list of dependencies plus one bad setting") but didn't show the file's exact
contents. My check treated that as too vague, even though it was actually clear enough to
redo. This is a borderline case my wording still doesn't fully cover.

**Check rationale**

My "Conventions / disclosure" check now says: pass if the repo has no AI policy; if it does
have one, pass unless something clearly breaks that specific rule. I originally only checked
for a missing "I used AI" statement. That broke on `pkg-03`, whose repo rule wasn't about
disclosing AI use — it just required comments to be written by a real person. So I widened
the check to cover any clear rule-break, not just one specific type.

**Trade-offs**

Widening that check fixed `pkg-03` without breaking `pkg-20` (the one package that actually
needed a disclosure statement — I re-tested it and it still correctly fails). The cost: my
check can only catch a violation it can actually point to. A well-disguised AI-written
comment with no other red flags would slip through, since there's nothing concrete for the
check to catch.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
