# Dependency Verification Log

**Status:** No dependencies have been installed or tested yet.  
**Policy:** npm workspaces, one root package-lock.json, exact versions after compatibility verification.

## Candidate package set

The versions below are registry listing snapshots observed on 2026-11-04, not an approved compatible package set:

| Package | Listing observed | Decision |
|---|---:|---|
| @babylonjs/core | 9.32.1 | Verify with matching loaders and chosen Node/Vite |
| @babylonjs/loaders | 9.33.2 | Verify compatibility with core before installation |
| @dimforge/rapier3d-compat | 0.21.0 | Verify API and browser initialisation |
| typescript | 7.0.2 | Verify toolchain compatibility; do not assume Vite plugin support |
| vite | 8.3.5 | Verify supported Node version |
| vitest | Not pinned | Select a compatible release |
| @playwright/test | Not pinned | Select a compatible release |
| wrangler | Not pinned | Select a compatible release |

A "latest" tag does not prove compatibility between packages. Do not simply install all latest versions. Select a tested combination and pin exact versions.

## Required record for every dependency

- Exact version installed.
- Official npm/release-note URL.
- Date verified.
- Node.js version used.
- Compatibility notes.
- Relevant commands and real output.
- Whether build and tests passed.

No dependency verification or build is claimed by this document.
