# Hybrid Greentech API docs

Developer documentation for the Hybrid Greentech platform, built on [Mintlify](https://mintlify.com). Migrated from the Fern-based [`hgt-energy-connect`](../hgt-energy-connect) project.

## Development

Install the [Mintlify CLI](https://www.npmjs.com/package/mint) to preview changes locally:

```
npm i -g mint
```

Run at the repo root, where `docs.json` lives:

```
mint dev
```

View the local preview at `http://localhost:3000`.

## Publishing changes

Install the Mintlify GitHub app from your dashboard to propagate changes from this repo to the deployment. Changes deploy automatically after pushing to the default branch.

## Structure

- `docs.json` — navigation, theme, and site config
- `api-reference/` — AsyncAPI specs and the API reference landing page
- `delegated-operators/`, `ev-chargers/`, `heat-pumps/`, `controller/`, `distributed-resources/` — per-surface guides
- `changelog.mdx` — API changelog
