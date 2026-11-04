# DIRT FRONTIER

A browser-first open-world off-road driving game for desktop and Android browsers.

## Project status

- **Master architecture:** V1.1 approved as the working specification for Phase 0.
- **Implementation status:** Not started. No game feature is claimed to be implemented or tested.
- **Repository:** This repository is the single source of truth. Do not create a separate prototype or duplicate game.
- **Budget target:** ₹0 initially, using free tiers only where practical.
- **Game modes:** Offline single-player and private 2–4 player cooperative rooms.
- **Monetisation:** Advertising is disabled until an eligible publisher account and provider approval exist.

## Read first

1. [Master architecture](docs/ARCHITECTURE.md)
2. [Engineering review and required clarifications](docs/ENGINEERING_REVIEW.md)
3. [Dependency verification policy](docs/DEPENDENCIES.md)
4. [AI developer handoff rules](docs/AI_HANDOFF.md)

## Core direction

- TypeScript and Babylon.js for the browser client.
- Rapier 3D for vehicle and world physics.
- Vite and npm workspaces for the monorepo.
- IndexedDB for offline saves and a Service Worker for offline asset caching.
- Cloudflare Pages, Workers, Durable Objects, and D1 are the proposed free-tier backend stack.
- WebRTC DataChannels are the proposed transport for private cooperative rooms.

All package versions and provider limits must be verified before implementation or deployment. Do not place credentials, API keys, passwords, identity documents, or payment information in this repository or in prompts to external AI services.

## Development rule

Every AI developer must inspect the current repository, read the architecture and the previous handoff, and modify this same project. Every claimed test must have actually been run and its result recorded.
