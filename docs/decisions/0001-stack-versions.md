# 0001: Django 5.2 LTS, custom user model, first dependencies

- **Date:** 2026-11-12
- **Status:** Pending Tristan review
- **Implemented in:** _(link the PR that builds the project skeleton)_

## Decision

- **Django 5.2 LTS** (latest 5.2.x patch, pinned). Supported with security fixes until April 2028.
- **Python 3.12.** Supported by Django 5.2 and the default Python on Ubuntu 24.04, so laptop and server can match.
- **PostgreSQL** locally and in production, via `psycopg` 3. No SQLite, even in development, so tests run against the same database as production.
- **Custom user model** (`accounts.User`, subclassing `AbstractUser`) created before the first migration, even though it starts with no extra fields.
- **First dependencies,** pinned in `requirements.txt`:
  - `Django` (BSD-3)
  - `psycopg[binary]` (LGPL-3.0)
  - `django-environ` (MIT): reads settings and secrets from `.env`
  - `pytest` (MIT) and `pytest-django` (BSD-3): test runner

All of these are open source, and their licenses are compatible with AGPL-3.0.

## Why

This is a volunteer project with a few hours a week of maintenance. An LTS release only needs patch upgrades until April 2028, instead of a feature-release upgrade every eight months. Phase 1 needs nothing from Django 6.x.

Swapping in a custom user model after the first migration is a known Django pain point. Doing it up front costs nothing, and phase 1 needs it anyway for the "confirmed 18+" fields.

## Alternatives considered

- **Django 6.1** (August 2026): newer features (built-in background tasks, CSP support, template partials), but support ends December 2027, so an upgrade is needed sooner.
- **SQLite for development:** simpler to set up locally, but behaves differently from Postgres in ways that can hide bugs.
- **`python-dotenv`:** fine, but `django-environ` also parses database URLs and typed settings, so it saves glue code.

## Tradeoffs

- No Django 6.x features until the next LTS upgrade (6.2, expected April 2027).
- Developers need Postgres running locally, a little more setup than SQLite.
