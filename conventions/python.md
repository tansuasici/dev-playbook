# Python Conventions

## Version & Tooling

- **Python 3.11+** — Use modern features (type hints, match statements, ExceptionGroups)
- **FastAPI** for web APIs
- **Pydantic v2** for data validation and settings
- **Ruff** for linting + formatting (replaces flake8, black, isort)
- **pytest** for testing
- **mypy** for type checking

## Project Structure

```text
project/
├── src/
│   └── project_name/
│       ├── __init__.py
│       ├── main.py           ← FastAPI app entrypoint
│       ├── config.py         ← Settings via pydantic-settings
│       ├── api/
│       │   ├── routes/       ← Route handlers
│       │   └── deps.py       ← Dependency injection
│       ├── models/           ← Pydantic models (request/response)
│       ├── services/         ← Business logic
│       └── core/             ← Shared utilities
├── tests/
│   ├── conftest.py
│   ├── test_services/
│   └── test_api/
├── pyproject.toml            ← Single config file (deps, ruff, mypy, pytest)
├── requirements.txt          ← Pinned deps for CI/deployment
└── Dockerfile
```

## Naming

| Element | Convention | Example |
|---------|------------|---------|
| Files/modules | snake_case | `course_service.py` |
| Functions | snake_case | `get_course_by_id()` |
| Classes | PascalCase | `CourseService` |
| Constants | SCREAMING_SNAKE | `MAX_CHUNK_SIZE` |
| Private | leading underscore | `_parse_document()` |
| Type variables | PascalCase | `T`, `ResponseT` |

## Type Hints

**Required everywhere.** No untyped public functions.

```python
def process_document(
    file_path: Path,
    chunk_size: int = 512,
    overlap: int = 50,
) -> list[DocumentChunk]:
    ...
```

Use modern syntax:

- `list[str]` not `List[str]`
- `dict[str, int]` not `Dict[str, int]`
- `str | None` not `Optional[str]`

## Async

- All FastAPI route handlers should be `async def`
- Use `asyncio` for concurrent IO operations
- Use `httpx.AsyncClient` for HTTP calls (not `requests`)
- Never mix sync and async — pick one per service

## Configuration

Use pydantic-settings for all config:

```python
class Settings(BaseSettings):
    database_url: str
    redis_url: str = "redis://localhost:6379"
    debug: bool = False

    model_config = SettingsConfigDict(env_file=".env")
```

## Error Handling

- Use custom exception classes, not bare `raise Exception`
- FastAPI exception handlers for HTTP error responses
- Log exceptions with context, not just the message
- Never silently swallow exceptions with bare `except:`

## Testing

- **pytest** with async support (`pytest-asyncio`)
- Fixtures for shared setup
- Use `httpx.AsyncClient` with `ASGITransport` for API tests
- Naming: `test_<function>_<scenario>_<expected>`

```python
async def test_process_document_with_valid_pdf_returns_chunks():
    ...
```

## Dependencies

- Pin exact versions in `requirements.txt` for reproducibility
- Use ranges in `pyproject.toml` for library projects
- `pip-compile` or `uv` for lock file generation
- Review and update dependencies monthly
