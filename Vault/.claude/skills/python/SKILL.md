---
name: python
description: Security-first Python 3.13 builder covering typed functions, FastAPI, pytest, and OWASP-aligned security patterns. Use when writing Python code, scaffolding a FastAPI app, writing pytest tests, or reviewing Python for security and correctness issues.
argument-hint: "[mode: write|api|test|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/python = Python 3.13, type hints, FastAPI, pytest, uv + pyproject.toml, Ruff.
/node for JavaScript servers. /csharp for .NET.

## Non-Negotiable
Type hints on all function signatures — no untyped functions.
Never use `eval()` or `exec()` on any input — arbitrary code execution risk.
Never use `pickle` for untrusted data — use JSON + Pydantic validation.
Never use `subprocess(..., shell=True)` — use `shell=False` with a list of args.
Never hardcode secrets — environment variables only.
`bare except:` banned — always catch specific exception types.

## Modes

**WRITE** — Functions, dataclasses, utilities, scripts, CLI tools.
Python 3.13 defaults: type hints everywhere, `@dataclass` for structured data, `pathlib.Path` (not `os.path`), `match`/`case` for branching, f-strings, `with` for resource cleanup.
Mutable default trap: never `def f(x=[])` — use `def f(x: list | None = None)`.
NON-DEV: explain type hints and dataclasses on first use in each session.
End with: propose `/python api` if HTTP endpoints needed, `/python test` for coverage.

**API** — FastAPI endpoint scaffolding.
Default: FastAPI + Pydantic v2. Auto-generates OpenAPI docs at `/docs`.
Input validation via Pydantic models — never trust raw request data.
Dependency injection via `Depends()`. Async routes by default.
Secrets from environment variables via `pydantic-settings`.
End with: propose `/python test` for endpoint coverage.

**TEST** — pytest scaffolding.
Framework: pytest 9+ with `pytest-mock`. Pattern: Arrange → Act → Assert.
Test discovery: `test_*.py` files, `test_` prefixed functions.
`conftest.py` for shared fixtures. Mock external calls with `monkeypatch` or `mocker`.
End with: propose `/python review` when coverage looks complete.

**REVIEW** — Security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: `eval`/`exec` calls, `pickle` usage, `subprocess(shell=True)`, bare `except:`, hardcoded secrets, missing type hints, mutable defaults, unvalidated input.
End with: propose running `ruff check .` for static analysis. Propose `/qa` when clean.

## Security Gate
Every WRITE and API response ends with this line — never skip it:
`Security pass: ✓ type hints present ✓ no eval/exec ✓ no pickle ✓ subprocess shell=False ✓ no hardcoded secrets ✓ no bare except`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Toolchain
New projects: `uv init` + `pyproject.toml` (not pip/requirements.txt).
Linting: `ruff check .` + `ruff format .` (replaces flake8 + black).
Testing: `uv run pytest`.

## Cross-Skill Integration
- `/node` — JavaScript equivalent; propose if operator prefers JS ecosystem
- `/typescript` — if building a frontend that calls this API
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
WRITE complete → propose `/python api` or `/python test`
API complete → propose `/python test`
TEST complete → propose `/python review`
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never omit type hints on function signatures
✗ Never use `eval()` or `exec()` — always forbidden
✗ Never deserialize untrusted data with `pickle` — use JSON + Pydantic
✗ Never use `subprocess(shell=True)` — use list args with shell=False
✗ Never hardcode credentials or API keys in source
✗ Never use mutable defaults (`def f(x=[])`) — use None sentinel
✗ Never use bare `except:` — catch specific exceptions
✗ Never use `os.path` — use `pathlib.Path`
✗ Never skip the security gate line on WRITE or API output


Every response ends with NEXT MOVE.