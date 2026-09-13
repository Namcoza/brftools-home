# Product

## Purpose

A simple public home page at `brftools.uk`. It is also the static-site pilot: the first project to prove that a change made locally goes live on the production hostname only through a pull request merged to `main`.

## Users

Anyone who visits `brftools.uk` or `www.brftools.uk`, from any device or network.

## Hosting profile

Cloudflare, as a static-assets Worker. The site is static HTML and CSS with no server process, data or secrets.

## Access

Public.

## Data

None. The page contains no personal information and collects nothing.

## Acceptance criteria

1. `https://brftools.uk` and `https://www.brftools.uk` serve the home page over HTTPS.
2. A pull request gets passing `test` and `gitleaks` checks, and its branch gets a Cloudflare preview URL.
3. Merging to `main` updates production without logging into any server.
4. A previous deployment can be restored from Cloudflare within 10 minutes.

## Out of scope

- Links to, or names of, family services or family members
- Forms, analytics, cookies or anything that collects input
- Any server-side code in production
