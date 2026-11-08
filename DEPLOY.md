# Deploying map-paper

The app is hosted on Cloudflare Pages. GitHub Actions builds and deploys it to the
`map-paper` Pages project when `main` changes. The custom domain and sign-in rules
are configured in Cloudflare.

## Cloudflare setup

The `map-paper` Pages project has a custom domain and Cloudflare Access rules.
Access protects the custom domain and these Pages hostnames:

- `map-paper-488.pages.dev`
- `*.map-paper-488.pages.dev`, including branch preview URLs

Access uses one-time PIN login and an allowed email list.

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
