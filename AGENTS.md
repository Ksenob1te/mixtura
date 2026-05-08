# Repository Notes

## Shape
- This root is a superproject: `mixtura-frontend` and every directory under `mixtura-backend/*` are git submodules on `develop`; inspect/commit inside the affected submodule, not only at the root.
- There is no root package manager or root test runner. Run commands from the service directory that owns the change.
- Existing deeper instructions are authoritative when working there: `mixtura-backend/gateway/AGENTS.md`, `mixtura-backend/mixtura-ranker/AGENTS.md`, `mixtura-backend/mixtura-balancer/AGENTS.md`, and `mixtura-backend/mixtura-balancer-tournament/AGENTS.md`.

## Backend Services
- Each Python backend is its own uv project with its own `pyproject.toml` and `uv.lock`; run `uv sync` in that service before verification and keep that service lockfile updated when dependencies change.
- `auth-service`, `server-service`, `mixer-service`, `gateway`, and `mixtura-ranker` run with `uv run python start.py`; `mixtura-balancer` runs with `uv run faststream run balancer_service.app.main:app`; `mixtura-balancer-tournament` runs with `uv run faststream run mixtura_balancer_tournament:app`.
- `auth-service`, `server-service`, `mixer-service`, and `mixtura-ranker` have Alembic configs; run migrations from the owning service directory, not from root.
- `server-service`, `mixer-service`, and `mixtura-ranker` pytest configs load `.env` via `pytest-dotenv`; tests that use `testcontainers` require a working Docker daemon.
- Quick Python syntax verification when no configured tests exist: `uv run python -m compileall -q start.py src` from the service directory.

## Native Balancers
- `mixtura-backend/mixtura-balancer/cpp_balancer` and `mixtura-backend/mixtura-balancer-tournament/nsga_balancer` are local C++/pybind packages pulled through `[tool.uv.sources]`.
- If imports fail after C++ changes, rebuild the owning service environment with `uv sync --force-reinstall`; verify native package tests from the native package directory when present.

## Runtime Env
- Backend configs read process environment via `pydantic-settings`; most services do not configure an env file in code, so shell-export variables or use Docker/pytest env loading.
- Shared infrastructure variable names are `POSTGRES_*`, `REDIS_*`, and `RABBITMQ_*`; `auth-service` also requires SMTP settings for mail flows.
