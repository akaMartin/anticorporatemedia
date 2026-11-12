# Noncorporate Media — CLAUDE.md

A small, invite-only, adults-only social network for keeping up with friends, built so it never competes for users' attention. Open source (AGPL-3.0), with product and financial decisions made in public.

**Design test:** the site should be a place people visit, not a place that keeps them. If a change would add ranking, recommendations, resharing, infinite scroll, push notifications or engagement tricks, stop and ask.

- Product spec (what and why): `docs/spec.md`. Read it before product-facing work.
- Decisions: `docs/decisions/`

## Current phase

**Phase 1: Core, on a laptop.** See Build phases in `docs/spec.md`. Build only what the current phase calls for.

## Core rules

@docs/core-rules.md

## Stack

- Django (server-rendered templates, light HTMX where needed), PostgreSQL
- Photos: DigitalOcean Spaces via the S3 API (django-storages). Never on local disk in production.
- Email: Resend HTTP API through Django's email backend. Not SMTP: DigitalOcean blocks SMTP ports.
- Prod: Docker (official `docker-ce` + `docker compose` plugin, never `docker-compose` v1), Caddy for HTTPS, Ubuntu 24.04 droplet with 2 GB RAM
- Backups: DigitalOcean droplet backups plus nightly `pg_dump` to Spaces

## Security and privacy requirements

- **Adults only:** date of birth checked at signup; under-18 signups rejected. Store only "confirmed 18+" and the confirmation date, never the birthdate.
- **Photo metadata:** strip all EXIF/XMP/IPTC (GPS, device, timestamps) on upload, before storage. Apply orientation to pixels first. Convert HEIC via `pillow-heif`. Test with a GPS-tagged image. The unstripped original is never written anywhere.
- **Data export (phase 6):** require password re-entry; email the owner on every export; stream the ZIP directly, never store it. Format details in `docs/spec.md`.

## Repo layout (target)

```
config/          # Django settings (base/dev/prod), urls
accounts/        # users, invites, profiles, account deletion
social/          # friendships, blocks, permissions
posts/           # posts, photos, links, tags, comments, likes
feed/            # feed queries, pagination, caught-up marker
moderation/      # reports, admin actions
exports/         # data export
templates/  static/
deploy/          # Dockerfile, compose.yaml, Caddyfile, backup script
docs/            # spec.md, core-rules.md, export-format.md, finances.md
docs/decisions/  # public decision records
tests/
LICENSE  CONTRIBUTING.md  README.md
```

## Commands

- Run tests: `python -m pytest`
- Dev server: `python manage.py runserver`
- Migrations: `python manage.py makemigrations && python manage.py migrate`

## Rules for working here

- **Build only the current phase.** No features, settings or scaffolding for later phases unless asked. Simple and complete beats extensible.
- **Test first for core rules.** Every core rule has a test (especially visibility, blocking and mutual friendship), written before the implementation and failing without it. Never weaken, skip or delete a failing test to make it pass; fix the code or stop and ask. Run the tests yourself before saying something works.
- **One place for visibility.** All "can this user see this?" logic lives in one module (e.g. `social/permissions.py`), and every view, query and template goes through it. The feed is built by one function. Never filter visibility ad hoc in a view.
- **Verify, don't assume.** Check current docs for version-specific APIs (Django, Caddy, Docker, django-storages) and state the version you're targeting. Before writing server config, check installed versions (`lsb_release -a`, `docker version`, `docker compose version`, `caddy version`, `psql --version`) and read actual logs.
- **Stop and ask before:** adding a dependency, changing a core rule or the data model behind one, changing anything in `deploy/`, or making a product or financial decision. Decisions get a record in `docs/decisions/`, linked from the commit or PR that implements them. If a request conflicts with a core rule, say so instead of bending the rule.
- **Production is read-only for Claude.** Run only read-only commands on the server; propose changes, and Martin applies and deploys them.
- **Two strikes, then explain.** If the same approach fails twice, stop and lay out what you tried, what you observed and the likely causes, rather than trying variations.
- **Secure by default.** Every view requires login unless explicitly marked public. Use Django's ORM, forms and CSRF protection; no raw SQL built from strings. Validate uploads (type, size) before processing.
- **Keep git clean.** Secrets live in `.env` (gitignored), with `.env.example` holding placeholders only; never hardcode keys, IPs or email addresses. Databases, dumps, uploads and large data files stay out of git.
- **Migrations:** never edit one that's already committed; create a new one.
- **Dependencies:** prefer Django built-ins, pin versions, and use only AGPL-compatible licenses.

## Ways of working

Claude chat (a Claude project connected to this repo) handles architecture, product decisions and debugging strategy. Claude Code builds and verifies here. Martin approves changes and runs deploys.

- **Style:** simple over clever; complete files over snippets.
- **Small steps:** one feature per branch and pull request, small commits with clear messages.
- **Done means:** tests pass, migrations are included, docs are updated if behavior changed, and the summary tells Martin what changed, how it was verified, and how to check it himself in the browser.
- **Explain new concepts.** Martin is an experienced Python data scientist, not a web developer. When introducing a Django or web concept for the first time (middleware, signals, CSRF, sessions), explain it in a sentence or two.
