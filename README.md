# brftools-home

The public home page for `brftools.uk`, and the Cloudflare Pages pilot for the brftools platform. See [`PRODUCT.md`](PRODUCT.md) for purpose and acceptance criteria.

**branch = work · pull request = validation · main = live**

---

## Requirements

| Tool | Version | Install (Mac) |
|---|---|---|
| Node.js | 24 LTS (`.nvmrc`) | `brew install node@24 && brew link --overwrite node@24` — `node@24` is not linked onto the PATH by default |
| gitleaks | 8.x | `brew install gitleaks` |

## Commands

| Task | Command |
|---|---|
| Install | `npm ci` |
| Run locally, reloading on change | `npm run dev` → http://localhost:3000 |
| Type check | `npm run check` |
| Test | `npm test` |
| Build | `npm run build` → `dist/public/` is the site |
| Scan for secrets | `npm run secrets` |

The Node server in `src/` exists only for local development and tests. Production serves `dist/public/` as static files — no server code runs.

## Layout

```
AGENTS.md            rules for AI-assisted changes (CLAUDE.md points here)
PRODUCT.md           purpose, users and acceptance criteria
docs/                intake checklist, deployment profiles, decisions
public/              the site — everything here is published
src/                 local development server only
tests/               node:test tests
scripts/             build helpers
.gitleaks.toml       secret-scanning rules
.github/workflows/   ci.yml — the required checks
```

## Hosting profile

**Cloudflare Pages.** See [`docs/deployment-profiles.md`](docs/deployment-profiles.md), Profile A.

| Setting | Value |
|---|---|
| Production branch | `main` |
| Build command | `npm run build` |
| Output directory | `dist/public` |
| Environment | `NODE_VERSION=24` |

## Configuration

None in production. For local development, `PORT` (default `3000`) sets the dev server's port.

## Persistent data

None. The site is rebuilt entirely from this repository; there is nothing to back up beyond Git.

## Deploy, verify, roll back

- **Deploy:** merge a passing pull request to `main`. Cloudflare builds and publishes it.
- **Preview:** every pull request gets a Cloudflare preview URL, posted on the pull request.
- **Verify:** the production hostname shows the change; Cloudflare Pages → Deployments shows the production deployment for the merged commit.
- **Roll back:** Cloudflare Pages → Deployments → choose the last good production deployment → **Rollback to this deployment**. Then revert the bad commit on `main` through a pull request, so the next merge does not re-publish it.

## Repository settings

Ruleset `protect-main` on the default branch: pull request required; status checks `test` and `gitleaks` required; force pushes and deletion blocked. Secret scanning and push protection on.
