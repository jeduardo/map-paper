# Deploying map-paper

The app is hosted on Cloudflare Pages. GitHub Actions builds and deploys it to the
`map-paper` Pages project when `main` changes. Cloudflare configuration, including
the custom domain and sign-in rules, is managed in the `personal-infra` repo.

## Cloudflare setup

In the `personal-infra` repo, `terraform/19-map-paper.tf` creates the
`map-paper` Pages project, `maps.mikos.org` DNS record, and custom domain. It
also protects these addresses with Cloudflare Access:

- `maps.mikos.org`
- `map-paper-488.pages.dev`
- `*.map-paper-488.pages.dev`, including branch preview URLs

Access uses the same one-time PIN login and allowed email list as the London
Place Finder. Apply the Terraform configuration from `personal-infra` to set up
these resources.

## GitHub Actions setup

Add these repository secrets to `jeduardo/map-paper`:

| Secret | Value |
|---|---|
| `CLOUDFLARE_API_TOKEN` | Cloudflare API token with Pages edit access |
| `CLOUDFLARE_ACCOUNT_ID` | Cloudflare account ID |

The existing workflow runs `npm ci`, `npm run build`, and deploys `dist` with
Wrangler. It deploys the `main` branch to production. It does not automatically
create pull request previews. To deploy a preview manually, build the app and
run:

```sh
npx --yes wrangler@4 pages deploy dist --project-name=map-paper \
  --branch=<branch-name>
```

The wildcard Access application protects the resulting
`*.map-paper-488.pages.dev` URL.

## Telemetry

Telemetry is disabled in `wrangler.toml` with `TELEMETRY_DISABLED = "true"`.
The `/api/event` function returns without writing events to Firestore. No
Firebase secrets are needed for this deployment.

## Local Pages preview

```sh
npm run build
npx wrangler pages dev dist --compatibility-date=2024-11-01
```
