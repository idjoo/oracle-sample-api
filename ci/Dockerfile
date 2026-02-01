FROM python:3.13-slim AS builder

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /app

ENV UV_COMPILE_BYTECODE=1

ENV UV_LINK_MODE=copy

COPY uv.lock .

COPY pyproject.toml .

RUN uv sync --frozen --no-install-project --no-editable

COPY . /app

RUN uv sync --frozen --no-editable



FROM python:3.13-slim

ENV TZ="Asia/Jakarta"
ENV PYTHONWARNINGS="ignore::UserWarning"

# SQLite configuration via environment variables
ENV DATABASE__KIND=sqlite
ENV DATABASE__NAME=/app/data/sample.db

WORKDIR /app

RUN useradd --uid 1000 --user-group --system app \
  && chown --recursive app:app /app \
  && mkdir -p /app/data \
  && chown app:app /app/data

COPY --from=builder --chown=app:app /app/.venv /app/.venv

COPY --from=builder --chown=app:app /app/db /app/db

USER app

CMD ["/app/.venv/bin/app"]
