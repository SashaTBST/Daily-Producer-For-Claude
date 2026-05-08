---
name: python
description: Security-first Python 3.13 builder covering typed functions, FastAPI, pytest, and OWASP-aligned security patterns. Use when writing Python code, scaffolding a FastAPI app, writing pytest tests, or reviewing Python for security and correctness issues.
argument-hint: "[mode: write|api|test|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/python = Python 3.13, type hints, FastAPI, pytest, uv + pyproject.toml, Ruff.
/node for JavaScript servers. /csharp for .NET.

## Non-Negotiable
Type hints on all function signatures â€” no untyped functions.
Never use `eval()` or `exec()` on any input â€” arbitrary code execution risk.
Never use `pickle` for untrusted data â€” use JSON + Pydantic validation.
Never use `subprocess(..., shell=True)` â€” use `shell=False` with a list of args.
Never hardcode secrets â€” environment variables only.
`bare except:` banned â€” always catch specific exception types.

## Modes

**WRITE** â€” Functions, dataclasses, utilities, scripts, CLI tools.
Python 3.13 defaults: type hints everywhere, `@dataclass` for structured data, `pathlib.Path` (not `os.path`), `match`/`case` for branching, f-strings, `with` for resource cleanup.
Mutable default trap: never `def f(x=[])` â€” use `def f(x: list | None = None)`.
NON-DEV: explain type hints and dataclasses on first use in each session.
End with: propose `/python api` if HTTP endpoints needed, `/python test` for coverage.

**API** â€” FastAPI endpoint scaffolding.
Default: FastAPI + Pydantic v2. Auto-generates OpenAPI docs at `/docs`.
Input validation via Pydantic models â€” never trust raw request data.
Dependency injection via `Depends()`. Async routes by default.
Secrets from environment variables via `pydantic-settings`.
End with: propose `/python test` for endpoint coverage.

**TEST** â€” pytest scaffolding.
Framework: pytest 9+ with `pytest-mock`. Pattern: Arrange â†’ Act â†’ Assert.
Test discovery: `test_*.py` files, `test_` prefixed functions.
`conftest.py` for shared fixtures. Mock external calls with `monkeypatch` or `mocker`.
End with: propose `/python review` when coverage looks complete.

**REVIEW** â€” Security + correctness audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: `eval`/`exec` calls, `pickle` usage, `subprocess(shell=True)`, bare `except:`, hardcoded secrets, missing type hints, mutable defaults, unvalidated input.
End with: propose running `ruff check .` for static analysis. Propose `/qa` when clean.

## Security Gate
Every WRITE and API response ends with this line â€” never skip it:
`Security pass: âœ“ type hints present âœ“ no eval/exec âœ“ no pickle âœ“ subprocess shell=False âœ“ no hardcoded secrets âœ“ no bare except`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Toolchain
New projects: `uv init` + `pyproject.toml` (not pip/requirements.txt).
Linting: `ruff check .` + `ruff format .` (replaces flake8 + black).
Testing: `uv run pytest`.

## Cross-Skill Integration
- `/node` â€” JavaScript equivalent; propose if operator prefers JS ecosystem
- `/typescript` â€” if building a frontend that calls this API
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
WRITE complete â†’ propose `/python api` or `/python test`
API complete â†’ propose `/python test`
TEST complete â†’ propose `/python review`
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never omit type hints on function signatures
âœ— Never use `eval()` or `exec()` â€” always forbidden
âœ— Never deserialize untrusted data with `pickle` â€” use JSON + Pydantic
âœ— Never use `subprocess(shell=True)` â€” use list args with shell=False
âœ— Never hardcode credentials or API keys in source
âœ— Never use mutable defaults (`def f(x=[])`) â€” use None sentinel
âœ— Never use bare `except:` â€” catch specific exceptions
âœ— Never use `os.path` â€” use `pathlib.Path`
âœ— Never skip the security gate line on WRITE or API output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.