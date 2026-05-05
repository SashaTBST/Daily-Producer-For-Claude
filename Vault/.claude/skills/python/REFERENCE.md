# Python Skill — Reference
Python 3.13 / FastAPI / pytest 9+ / uv + Ruff | Last verified: 2026-04-30

## Research Sources
- Python 3.14 what's new: https://docs.python.org/3/whatsnew/3.14.html
- PEP 695 — type parameter syntax: https://peps.python.org/pep-0695/
- FastAPI vs Django vs Flask 2026: https://medium.com/@inprogrammer/fastapi-vs-flask-vs-django-which-to-choose-in-2026-c51b243174b5
- OWASP Deserialization Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Deserialization_Cheat_Sheet.html
- pytest 2026 guide: https://tech-insider.org/pytest-tutorial-python-testing-ci-cd-2026/
- Ruff docs: https://docs.astral.sh/ruff/
- uv projects: https://docs.astral.sh/uv/concepts/projects/config/
- Mutable defaults gotcha: https://docs.python-guide.org/writing/gotchas/

---

## Python 3.13 Patterns — Use by Default

### Dataclasses (prefer over plain classes for data)
```python
from dataclasses import dataclass, field
from typing import ClassVar

@dataclass
class User:
    id: int
    email: str
    name: str
    tags: list[str] = field(default_factory=list)  # ✓ safe mutable default
    _count: ClassVar[int] = 0  # class variable, not instance

    def display_name(self) -> str:
        return f"{self.name} <{self.email}>"
```

### Type Hints — Always
```python
# Every function — parameters AND return type
def find_user(user_id: int, active_only: bool = True) -> User | None:
    ...

# Type alias (PEP 695 — Python 3.12+)
type UserId = int
type UserMap = dict[UserId, User]

# Generic function
def first_item[T](items: list[T]) -> T | None:
    return items[0] if items else None
```

### pathlib — Always (not os.path)
```python
from pathlib import Path

# ✓ pathlib
config_path = Path("config") / "settings.json"
content = config_path.read_text(encoding="utf-8")
exists = config_path.exists()

# ✗ never
import os
config_path = os.path.join("config", "settings.json")
```

### match/case — Pattern Matching
```python
def handle_status(status: str) -> str:
    match status:
        case "ok":
            return "success"
        case "error" | "fail":
            return "failure"
        case str(unknown):
            return f"unknown status: {unknown}"
        case _:
            return "unexpected"
```

---

## FastAPI Template

```python
# main.py
from fastapi import FastAPI, Depends, HTTPException
from pydantic import BaseModel, EmailStr
from pydantic_settings import BaseSettings
import uvicorn

class Settings(BaseSettings):
    database_url: str
    secret_key: str

    class Config:
        env_file = ".env"  # .env is loaded; secrets from env vars in prod

def get_settings() -> Settings:
    return Settings()  # type: ignore[call-arg]

class CreateUserRequest(BaseModel):
    email: EmailStr
    name: str

class UserResponse(BaseModel):
    id: int
    email: str
    name: str

app = FastAPI(title="My API", version="1.0.0")

@app.post("/users", response_model=UserResponse, status_code=201)
async def create_user(
    req: CreateUserRequest,
    settings: Settings = Depends(get_settings),
) -> UserResponse:
    # Pydantic validates req before this function runs
    # Access settings.database_url for DB connection (from env, not hardcoded)
    user_id = 1  # replace with actual DB insert
    return UserResponse(id=user_id, email=req.email, name=req.name)

@app.get("/users/{user_id}", response_model=UserResponse)
async def get_user(user_id: int) -> UserResponse:
    # raise HTTPException for not-found cases
    raise HTTPException(status_code=404, detail=f"User {user_id} not found")
```

---

## pytest Template

```python
# conftest.py — shared fixtures
import pytest
from fastapi.testclient import TestClient
from main import app

@pytest.fixture
def client() -> TestClient:
    return TestClient(app)

# test_users.py
from fastapi.testclient import TestClient

def test_create_user_valid(client: TestClient) -> None:
    # Arrange
    payload = {"email": "alice@example.com", "name": "Alice"}
    # Act
    response = client.post("/users", json=payload)
    # Assert
    assert response.status_code == 201
    data = response.json()
    assert data["email"] == "alice@example.com"

def test_create_user_invalid_email(client: TestClient) -> None:
    payload = {"email": "not-an-email", "name": "Alice"}
    response = client.post("/users", json=payload)
    assert response.status_code == 422  # Pydantic validation error
```

---

## pyproject.toml Template (uv)

```toml
[project]
name = "my-app"
version = "0.1.0"
requires-python = ">=3.13"
dependencies = [
    "fastapi>=0.115.0",
    "uvicorn[standard]>=0.32.0",
    "pydantic>=2.9.0",
    "pydantic-settings>=2.6.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=9.0.0",
    "pytest-mock>=3.14.0",
    "httpx>=0.28.0",
    "ruff>=0.9.0",
]

[tool.ruff]
target-version = "py313"
line-length = 100

[tool.ruff.lint]
select = ["E", "F", "I", "B", "S"]  # pycodestyle, pyflakes, isort, bugbear, bandit security
ignore = ["S101"]  # allow assert in test files

[tool.pytest.ini_options]
testpaths = ["tests"]
```

---

## Subprocess — Safe Pattern

```python
import subprocess
from pathlib import Path

# ✓ shell=False — args as list, no injection risk
result = subprocess.run(
    ["git", "log", "--oneline", "-5"],
    capture_output=True,
    text=True,
    check=True,
    cwd=Path("/path/to/repo"),  # explicit working dir
    timeout=30,
)
output = result.stdout

# ✗ NEVER — shell=True with user input → command injection
# subprocess.run(f"git log {user_input}", shell=True)  # arbitrary code execution
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | Type hints present | All function parameters and return types annotated | PASS/WARN |
| 2 | No eval/exec | `eval(` or `exec(` anywhere in codebase | PASS/FAIL |
| 3 | No pickle on untrusted data | `pickle.loads(` on any external input | PASS/FAIL |
| 4 | subprocess shell=False | `subprocess.*shell=True` — always use list args | PASS/FAIL |
| 5 | No hardcoded secrets | Grep for password, secret, api_key with literal string values | PASS/FAIL |
| 6 | No mutable defaults | `def f(x=[])` or `def f(x={})` — use None sentinel | PASS/FAIL |
| 7 | No bare except | `except:` without exception type specified | PASS/FAIL |
| 8 | pathlib over os.path | `import os.path` or `os.path.join` — use pathlib | PASS/WARN |
| 9 | Input validated | External data validated via Pydantic before use | PASS/WARN |
| 10 | No f-string SQL | f-strings in SQL queries — use parameterized queries | PASS/FAIL |
