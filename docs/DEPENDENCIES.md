# Dependency Verification Log

**Status:** Registry listing snapshots checked on 2026-11-04. No packages have been installed, typechecked, built, or runtime-tested yet.  
**Policy:** npm workspaces, one root `package-lock.json`, exact versions only after compatibility verification.

## Registry snapshots checked on 2026-11-04

The versions below were visible on the npm package pages at review time. These are candidate registry versions, **not an approved compatible package set**.

| Package | npm listing observed | Official package page | Status |
|---|---:|---|---|
| `@babylonjs/core` | 9.32.1 | https://www.npmjs.com/package/@babylonjs/core | Registry listing checked; compatibility pending |
| `@babylonjs/loaders` | 9.33.2 | https://www.npmjs.com/package/@babylonjs/loaders | Registry listing checked; verify peer compatibility with core |
| `@dimforge/rapier3d-compat` | 0.21.0 | https://www.npmjs.com/package/@dimforge/rapier3d-compat | Registry listing checked; API/typecheck pending |
| `typescript` | 7.0.2 | https://www.npmjs.com/package/typescript | Registry listing checked; toolchain compatibility pending |
| `vite` | 8.3.5 | https://www.npmjs.com/package/vite | Registry listing checked; supported Node version pending |
| `vitest` | Not selected | https://www.npmjs.com/package/vitest | Select a compatible release |
| `@playwright/test` | Not selected | https://www.npmjs.com/package/@playwright/test | Select a compatible release |
| `wrangler` | Not selected | https://www.npmjs.com/package/wrangler | Select a compatible release |

A `latest` npm tag does not prove that different packages work together. In particular, do not assume that the latest Babylon core and loaders versions are a compatible pair merely because both are current. Verify peer dependencies, release notes, TypeScript compatibility, Node.js engine requirements, Vite integration, Rapier WASM initialisation and browser output before pinning.

## Official provider references

- Cloudflare D1 pricing: https://developers.cloudflare.com/d1/platform/pricing/
- Cloudflare Workers limits: https://developers.cloudflare.com/workers/platform/limits/
- Cloudflare Durable Objects pricing: https://developers.cloudflare.com/durable-objects/platform/pricing/
- Cloudflare Pages limits: https://developers.cloudflare.com/pages/platform/limits/

Free-tier quotas are snapshots and must be rechecked before deploying. If a quota is exceeded, the application must fail safely; do not enable paid usage or billing without the account owner's/guardian's explicit review.

## Required record for every dependency

- Exact version installed.
- Official npm/release-note URL.
- Date verified.
- Node.js version used.
- Compatibility notes and peer dependency output.
- Relevant install, typecheck, build and test commands.
- Actual command output and results.

## Verification labels

- **REGISTRY CHECKED:** Version appeared on the official npm package listing.
- **INSTALLED:** Package installation completed.
- **COMPATIBILITY CHECKED:** Peer/API compatibility reviewed.
- **BUILD VERIFIED:** Project build actually completed successfully.
- **RUNTIME VERIFIED:** Browser/runtime behaviour actually exercised.

No package installation, compatibility test, build or runtime verification is claimed by this document.
