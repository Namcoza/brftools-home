# brftools-home

The public home page for `brftools.uk`, and the static-site pilot for the brftools platform. See [`PRODUCT.md`](PRODUCT.md) for purpose and acceptance criteria.

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

**Never run `wrangler deploy` by hand.** Production changes only through a merged pull request.

## Layout

```
AGENTS.md            rules for AI-assisted changes (CLAUDE.md points here)
PRODUCT.md           purpose, users and acceptance criteria
docs/                intake checklist, deployment profiles, decisions
public/              the site — everything here is published
src/                 local development server only
tests/               node:test tests
scripts/             build helpers
wrangler.jsonc       Cloudflare deployment settings
.gitleaks.toml       secret-scanning rules
.github/workflows/   ci.yml — the required checks
```

## Hosting profile

**Cloudflare Workers, static assets only.** Cloudflare's GitHub integration (Workers Builds) builds and deploys the site. Deployment settings live in [`wrangler.jsonc`](wrangler.jsonc); build settings live in the Cloudflare dashboard:

| Setting (dashboard) | Value |
|---|---|
| Production branch | `main` |
| Build command | `npm run build` |
| Deploy command | `npx wrangler deploy` |
| Non-production branch deploy command | `npx wrangler versions upload` |

Node 24 is picked up from `.nvmrc`. Custom domains are attached in the dashboard under the Worker's **Settings → Domains & Routes**.

## Configuration

None in production. For local development, `PORT` (default `3000`) sets the dev server's port.

## Persistent data

None. The site is rebuilt entirely from this repository; there is nothing to back up beyond Git.

## Deploy, verify, roll back

- **Deploy:** merge a passing pull request to `main`. Cloudflare builds and deploys it.
- **Preview:** pushes to other branches upload a preview version with its own URL, without affecting production.
- **Verify:** the production hostname shows the change; the Worker's **Deployments** tab shows a version built from the merged commit.
- **Roll back to a specific version, never blindly to "previous version".** This Worker's history includes Cloudflare's setup "Hello world" versions, and **Rollback to previous version** picked one of them during the pilot's rollback test.
  1. Worker → **Deployments** → **Versions**: find the version built from the last good merge commit.
  2. Open its preview URL — `https://<first 8 characters of the version ID>-brftools-home.<account subdomain>.workers.dev` — and confirm it shows the expected page.
  3. Deploy that version at 100%.
  4. Revert the bad commit on `main` through a pull request, so the next merge does not re-publish it.

## Repository settings

Ruleset `protect-main` on the default branch: pull request required; status checks `test` and `gitleaks` required; force pushes and deletion blocked. Secret scanning and push protection on.
