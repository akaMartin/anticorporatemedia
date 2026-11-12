# Noncorporate Media — Product Spec

Nov 12, 2026 · Martin B

## Purpose and principles

Noncorporate Media (noncorporatemedia.com) is a social network for keeping up with friends and acquaintances, built so that its interests never diverge from those of its users.

Existing social media drifted toward capturing time and attention because investor and revenue pressure rewarded it, at the expense of the user experience. This project rejects that incentive, not the premise of social media or technology itself. We want to foster connection and communication with people in the real world, not farm attention and personal information for ad revenue.

**Design test for every decision:** the network should be a place you visit, not a place that keeps you. Concretely:

- Mutual connections only: content is shared between people who have both chosen to be friends. No one-way following
- Content only from your network: your friends' posts, plus comments and opt-in photo shares that reach you through them
- No algorithmic ranking, recommendations or suggested content
- No easy resharing or broadcast mechanics
- A feed that ends
- Any money it takes in is disclosed, along with what it pays for

## Open source and public decisions

The code, the infrastructure setup, the finances and the product and financial decisions are all public. Private user content is the only exception.

- **License:** AGPL-3.0. Anyone can run or fork it. Anyone who runs a modified version as a service must publish their changes, so no one can quietly close it off.
- **Public repo from day one:** code, deploy config (Dockerfile, Caddyfile, compose file) and docs live on GitHub. Secrets never do: they go in `.env` (gitignored), with secret scanning on every commit.
- **Decision log:** every product or financial decision is a short record in `docs/decisions/`, covering what was decided, why, the alternatives, and what it trades off. Each record links to the change it produced.
- **Public finances:** a ledger of every cost and every dollar received, in the repo and on the About page, updated monthly
- **Open by default, swappable where not:** all software is open source (Django, PostgreSQL, Caddy, Docker Engine). Hosted services (DigitalOcean, Spaces, Resend) are proprietary but use standard interfaces. Photos go through the S3 API, and email goes through Django's email backend, so any of them can be replaced without code changes.
- **What stays private:** posts, messages, account details, and individual reports and moderation cases. Moderation is published only as a periodic aggregate transparency report (counts by type and action taken).
- **Contributions:** open issues and pull requests from anyone. Martin keeps final say on merges and direction, and records significant calls in the decision log.

## Audience and scale

It launches for Martin's friend group and grows only as members invite their own friends.

- **Now:** a small group of mostly non-technical adult friends, on desktop and mobile browsers
- **Later:** friends of friends through invite chains, likely hundreds to low thousands of people
- **Implication:** one modest server is enough for years; the harder problems at that size are funding and abuse handling, not performance
- **Mobile:** a responsive web app, with no native app and no push notifications. Not having push is intentional and fits the design test.

## Core rules

The rules that define the product live in [`docs/core-rules.md`](core-rules.md), the single source shared by this spec and `CLAUDE.md`.

## Features

The launch feature set is deliberately small. Everything else waits until friends are using it.

**At launch**

| Area | Behavior |
| --- | --- |
| Accounts | Invite-only signup by link, adults (18+) only. Username and password login. Profile with name, photo, short bio. Account deletion is handled manually on request until self-serve deletion ships (same rules as below). |
| Friendship | Send, accept, decline or cancel requests. Unfriend. Block, which hides both people from each other and prevents new requests. |
| Posts | Text, a single photo, or a plain link. Delete your own posts. |
| Comments | Anyone who can see a post can comment. The post's author can delete any comment on it. |
| Likes | One like per person per post, with a visible count. Never used for ordering. |
| Feed | Friends' posts, newest first, split into numbered pages instead of infinite scroll. A "you're all caught up" marker at your last visit; older pages remain browsable. |
| Walls | Your posts. |
| Notifications | In-app list: friend requests and comments on your posts. |

**After launch**

| Area | Behavior |
| --- | --- |
| Data export | One button on the account page; see Data export below. |
| Tagging | Tag friends in any post. Photo tags offer an opt-in share to your friends' feeds and your wall, which then also appear in feeds and on walls. Remove a tag of yourself. |
| Account deletion (self-serve) | Removes the profile, posts, photos, likes and friendships; comments remain, attributed to "Deleted user." Export is offered first. |
| Posts | Link preview cards, multiple photos per post, editing your own posts. |
| Notifications | Optional email notifications for each in-app notification, with default set to on but easily modifiable (button within email, easy notification controls in account settings). |
| Invites | Per-user invite limits. |

## Data export (after launch)

One "Export my data" button on the account page downloads everything the user created. There's no federation, but no lock-in either.

**Included:** your posts (text and links), photos you uploaded, comments you wrote, and your profile. Nothing authored by other people, including tags of you or photos others posted.

**Format:** a ZIP containing `data.json` (plain JSON, schema documented in `docs/export-format.md`), an offline `index.html` viewer, and `photos/`.

**Safeguards:** you must re-enter your password to start an export, and an email notice is sent to the account owner every time one runs ("If this wasn't you, change your password"). The archive is built on request and streamed directly to the browser, never stored on the server. No background queue; revisit only if exports take over ~30s.

## Deliberately excluded

These are left out on purpose. Adding any of them means revisiting the design test.

- Following, subscribing, pages, or any one-way relationship
- Algorithmic or engagement-based feed ordering, "suggested posts," trending
- One-click resharing of other people's posts
- Infinite scroll, autoplay, push notifications
- Native mobile apps
- Demographic or behavioral ad targeting, and selling data to third parties

## Safety and legal minimum

Moderation is light because interactions are between friends and nothing is promoted. A small set of obligations comes from hosting uploaded files.

- [ ] **Report button** on every post, comment and profile, sending to an admin-only queue
- [ ] **Age restriction of 18+** for creating an account. Require date of birth on account creation; store only "confirmed 18+" and the confirmation date, never the birthdate.
- [ ] **Admin tools:** remove content, suspend accounts, view a report's context (Django admin)
- [ ] **CSAM:** if apparent child sexual abuse material is found, remove it, preserve it, and report it to NCMEC's CyberTipline, as 18 U.S.C. § 2258A requires
- [ ] **DMCA:** register a designated agent with the U.S. Copyright Office ($6, renew every 3 years) and publish the contact on the site
- [ ] **Terms of service and privacy page** written in plain language
- [ ] **Photo privacy:** all metadata (GPS location, device, timestamps) is stripped from photos at upload, before storage
- [ ] Per-user invite limits once invites reach beyond the founding circle, to slow abuse through invite chains

Not legal advice. Get a short legal consult if the network grows well beyond the founding group.

## Funding and transparency

Martin covers costs at launch. If usage grows, the network adds voluntary donations or a small number of non-targeted ads (users able to choose donation or ads), and publishes exactly why and where the money goes.

- **About page:** monthly costs by line item (server, storage, email, domain), money received, and the funding method in use with its tradeoffs
- **Donations** are the first choice: simplest to run and the least conflict of interest
- **Ads, if ever:** every user sees ads chosen the same way; nothing we know about a user (profile, posts, friends, activity, location) is used to select ads or shared with advertisers. Ads may come from a direct sponsor or an external network, but ad code is always isolated in a sandboxed iframe or served from our own domain, so it can never read posts or other user content. Clearly labeled, never mixed into the feed's ordering.
- **Principle:** making money is acceptable when users can see how it's made, what it pays for, and what it trades off

## Technical stack and hosting

The stack is a custom Django app on the existing DigitalOcean droplet. The droplet gets rebuilt from a fresh image; nothing is carried over from Friendica. Each choice targets a failure from the Atithi attempt.

| Component | Choice | Why |
| --- | --- | --- |
| App | Django, server-rendered templates, light HTMX where needed | Python (familiar); paged feed is natural server-side; built-in admin covers the report queue |
| Database | PostgreSQL | Standard Django pairing; easy `pg_dump` backups |
| Web server / TLS | Caddy | Automatic HTTPS; replaces the Nginx + Certbot two-stage setup and `http2` syntax issues |
| Containers | Docker from Docker's official repo (`docker-ce` + `docker compose` plugin) | Avoids Ubuntu's legacy `docker-compose` v1 mismatch |
| Photo storage | DigitalOcean Spaces (S3-compatible) | Keeps uploads off the small droplet disk |
| Email | Resend HTTP API | Avoids DigitalOcean's SMTP port block entirely |
| Server | Existing droplet, fresh Ubuntu 24.04 LTS, 2 GB RAM | Enough for hundreds to low thousands of users |
| Backups | DigitalOcean droplet backups plus nightly `pg_dump` to Spaces | Two independent copies |
| Domain / DNS | noncorporatemedia.com | Replaces atithi.social |

## Build phases

It is built locally first, tried by friends on staging, and opened to invites only after the legal minimum is in place. Launch is deliberately small; everything in phase 6 waits until friends are using it.

1. **Core, on a laptop:** invite-only signup (18+), mutual friend requests, unfriend and block, text posts, single-photo posts, plain links, comments (authors can delete comments on their posts), likes, paged feed with caught-up marker, photo metadata stripping on upload. The data model is export-ready from day one: every piece of user content records its author. Tests for every core rule.
2. **Second pass:** report button and admin queue, in-app notifications (friend requests, comments on your posts).
3. **Deploy:** rebuild the droplet, set up Docker, Caddy, Postgres, Spaces and Resend, point DNS, and add backups. Staging first, a few friends test.
4. **Before inviting anyone:** ToS and privacy page, About page with costs, DMCA agent registration.
5. **Launch** to the founding friend group.
6. **After launch, in order of what friends ask for:** data export, tagging with the photo opt-in share, self-serve account deletion, link preview cards, multiple photos per post, email notifications, post editing, per-user invite limits. Until self-serve deletion ships, deletion requests are handled manually under the same rules.

## How it's built

Claude chat (a Claude project connected to this repo) handles architecture, product decisions and debugging strategy. Claude Code builds and verifies in the repo, following `CLAUDE.md`. Martin approves changes and runs deploys.

## Open questions

- [ ] How many invites per user at launch?
- [ ] Should user feedback on proposed decisions happen in public GitHub issues, on the site itself, or both?
- [ ] If users can choose donation or ads, does a donation turn ads off for that user?
