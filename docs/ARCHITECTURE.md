# DIRT FRONTIER — Master Architecture V1.1

**Status:** Approved working specification for Phase 0, subject to the clarifications in [ENGINEERING_REVIEW.md](ENGINEERING_REVIEW.md).  
**Source:** Nemotron 3.5 Super VL architecture response supplied by the project owner on 2026-11-04.  
**Repository:** https://github.com/pumeshnaidu143-debug/Gamming  
**Platform:** Browser (desktop and Android)  
**Initial infrastructure budget:** ₹0; free tiers only  
**Implementation status:** No game code has been created or verified by this document.

## 1. Product vision

DIRT FRONTIER is one continuous, expandable open-world off-road driving game playable in a browser. Players explore a procedural natural environment, drive original off-road vehicles with physically based suspension and tyre behaviour, complete missions, and customise vehicles in a garage.

The same codebase supports:
- Offline single-player with local progression.
- Private online cooperative sessions for 2–4 players.
- Desktop and Android browser input and adaptive rendering.

There is no ranked competitive mode in V1.1. Host-authoritative peer-to-peer multiplayer is not a secure competitive architecture.

## 2. Game systems

### World
- Expandable procedural environment with forests, mountains, valleys, beaches and rocky regions.
- Surface types include mud, sand, gravel, grass, rock and wet roads.
- Procedural winding road network.
- Day/night cycle and weather states such as clear, overcast and rain.
- Seeded generation so unmodified terrain can be regenerated.

### Vehicles
- Original, unlicensed vehicle designs.
- Shared vehicle definitions for physical and visual parameters.
- Suspension, tyre forces, drivetrain, brakes and terrain-dependent grip.
- Detailed exterior, interior, wheel and light visuals.
- Garage customisation for paint, tyres, suspension and upgrades.

### Core loop
1. Spawn in the garage or open world.
2. Explore and drive across procedural terrain.
3. Complete missions or free-roam.
4. Earn progression and customise vehicles.
5. Save locally and optionally synchronise a cloud profile.

## 3. Technical stack direction

These are architectural choices; exact package versions remain subject to verification.

| Layer | Selected direction | Responsibility |
|---|---|---|
| Language | TypeScript | Game and backend logic |
| Renderer | Babylon.js via @babylonjs/core | 3D rendering, materials, cameras |
| Physics | Rapier 3D | Rigid-body chassis and vehicle simulation |
| Build | Vite | Browser development and bundling |
| Workspace | npm workspaces | One root package manifest and lockfile |
| Tests | Vitest and Playwright | Unit, integration and browser tests |
| Offline data | IndexedDB | Saves, garage, settings |
| Offline assets | Service Worker / PWA | Application shell and essential asset cache |
| Multiplayer | WebRTC DataChannels | Peer-to-peer game-state transport |
| Signalling | Cloudflare Workers and Durable Objects | Room creation and WebRTC signalling |
| Persistent cloud data | Cloudflare D1 | Player profiles and validated save records |
| Static hosting | Cloudflare Pages | Browser distribution |
| Source control | This GitHub repository | Single source of truth and backups |

No dependency version is approved until its exact version and compatibility are recorded in DEPENDENCIES.md.

## 4. Unified repository

The project is one monorepo. No independent prototypes, duplicate engines or duplicate vehicle systems are permitted.

Proposed layout:

- .github/workflows — CI and deployment workflows.
- package.json and package-lock.json — root npm workspace and one lockfile.
- packages/client — browser application, rendering, physics, world, vehicles, input, audio, networking, saves, UI, PWA and disabled ad integration.
- packages/shared — vehicle/world configuration, network contracts, save schemas, migrations and common math.
- packages/backend — Cloudflare Workers APIs, Durable Object room classes and D1 migrations.
- public — PWA manifest and static shell assets.
- tests — unit, integration and browser end-to-end tests.
- docs — architecture, dependencies, API and developer handoff documents.

## 5. Simulation and rendering

- Physics uses a fixed 60 Hz timestep on desktop and Android.
- Rendering is decoupled and may run at a different frame rate.
- Use an accumulator, bounded catch-up steps and render interpolation.
- Adapt rendering cost, not the physics timestep, when a device is slow.
- Use a single shared vehicle configuration as the source of physical and visual dimensions.
- V1.1 selects raycast-based suspension wheels.
- Suspension raycasts must filter out the vehicle's own chassis.
- Visual vehicle meshes are separate from physics and are updated by a render bridge.

The coordinate handedness, forward axis, wheel axis and sign conventions must be explicitly locked in a shared contract before vehicle implementation; see ENGINEERING_REVIEW.md.

## 6. Procedural environment

- Use seeded heightfield generation with biome modulation.
- Stream terrain chunks around the player; do not generate the whole world at once.
- Use distance-based terrain LOD and background generation where practical.
- Derive biome transitions from environmental parameters such as elevation, slope and moisture.
- Represent roads as spline graphs and ensure the drivable road surface agrees with the terrain and physics surface data.
- Scatter scenery using deterministic placement and instanced rendering where appropriate.
- Save the world seed and player-made changes, not every generated terrain vertex.

## 7. Offline and PWA behaviour

- Offline mode is the default and must not require a network connection to start.
- IndexedDB stores local player progression, garage data, settings and save revisions.
- A Service Worker caches the application shell and essential assets.
- Cache names are versioned; obsolete caches are removed safely.
- Runtime asset caching must respect browser storage quotas.
- When essential cached assets are unavailable offline, show an offline fallback rather than claiming the full game can run.
- Cloudflare CDN caching alone is not an offline strategy.

## 8. Multiplayer V1.1

- Private cooperative rooms, maximum four players.
- Host-authoritative physics: the host runs the shared Rapier simulation.
- Clients send input; the host sends authoritative snapshots.
- Cloudflare Workers and Durable Objects handle room creation, short-lived room coordination and WebRTC signalling, not continuous vehicle simulation.
- WebRTC DataChannels carry game-state messages after peer connections are established.
- Use reliable delivery for critical room events and suitable low-latency delivery for frequent input/snapshot messages.
- Implement protocol versioning, room/session/player IDs, simulation tick numbers, input sequence numbers, acknowledgements, validation, heartbeat, timeouts and reconnection state recovery.
- Apply local prediction and reconciliation carefully; interpolate remote players using a separate network snapshot buffer.
- No guaranteed TURN relay exists in the initial zero-budget plan. Direct P2P may fail on some networks; provide a clear retry or offline fallback.
- Host-authoritative P2P is not cheat-proof. Ranked competitive play is excluded.

## 9. Backend and data

Proposed API paths use /api/v1:
- POST /rooms/create
- POST /rooms/join
- GET /save/load
- POST /save/save
- POST /ads/consent (future, inactive)

D1 is for durable profile and garage data, not per-frame physics telemetry. IndexedDB is the immediate local save store. Cloud sync is optional.

A client-generated guest ID alone is not authentication. Cloud saves require a server-validated temporary guest session, payload/schema validation, rate limiting, ownership checks and save-revision checks. Never trust client-submitted currency, XP or unlocked items without server validation.

No personal information, precise geolocation, email or password is required for guest play by default.

## 10. Asset delivery

- Use compressed GLB assets and compressed browser-friendly textures.
- Maintain an asset manifest with versioned/hash-based filenames.
- Load only the vehicle and world assets needed for the current region.
- Keep the initial download small; the V1.1 target is under 5 MB for the shell, first vehicle and starting chunk (a proposal to measure, not a verified result).
- Keep individual hosted files within the selected provider's limits; use a separately reviewed asset-storage option if required.
- Avoid a monolithic world download.

## 11. Performance goals

Desired targets, not measured guarantees:

| Metric | Desktop | Midrange Android |
|---|---:|---:|
| Render target | 60 FPS | 30 FPS |
| Memory target | Under 1.5 GB | Under 500 MB |
| Draw calls target | Under 500/frame | Under 200/frame |

Adaptive tiers reduce render resolution, terrain detail, foliage, shadow distance, texture quality and effects. The physics timestep remains fixed at 60 Hz. Actual performance must be measured on representative hardware.

## 12. Advertising and safety

- Advertising is disabled by default.
- Ad integration remains a no-op until provider eligibility, publisher approval and policy requirements are satisfied.
- Initial ad placement is limited to appropriate website/landing content, not active driving controls.
- No personalised tracking by default.
- Follow all platform age and eligibility requirements. Where a service requires an adult account holder, a parent or guardian must review and manage that account. Never bypass age checks or use false identity details.
- Do not put credentials, payment details or identity documents in the repository or external AI prompts.

## 13. QA and release gates

Required testing includes:
- Vehicle physics maths and raycast filtering.
- Save schema migration and IndexedDB round-trips.
- Network protocol parsing, stale packet rejection and interpolation.
- Room creation and signalling.
- P2P loss/disconnect/reconnect behaviour.
- Browser launch and build smoke tests.
- Android Chrome compatibility and performance profiling.
- Memory leak checks during extended driving.

A feature is not marked VERIFIED until a real test exists and passes in CI. Expected results are not test results.

## 14. Development phases

1. Phase 0 — Repository foundation, shared contracts, build pipeline and blank Babylon.js + Rapier startup.
2. Phase 1 — Offline core, one vehicle and a small procedural terrain test area.
3. Phase 2 — Garage, progression and local saves.
4. Phase 3 — Larger world, roads, biomes, day/night and weather.
5. Phase 4 — Multiplayer signalling and WebRTC protocol skeleton.
6. Phase 5 — Private 2–4 player cooperative rooms and recovery.
7. Phase 6 — Website content and disabled advertising integration.
8. Phase 7 — Performance, mobile testing, QA and deployment.

Every phase modifies this repository and must pass its acceptance checks before the next phase is treated as complete.

## 15. Free-tier constraints

Cloudflare is the proposed initial provider, not a guarantee of unlimited or permanent free hosting. Current limits must be checked against official documentation and recorded with an access date before deployment. Usage exhaustion should fail safely; it must not trigger unreviewed paid usage.

Official references:
- D1 pricing: https://developers.cloudflare.com/d1/platform/pricing/
- Workers limits: https://developers.cloudflare.com/workers/platform/limits/
- Durable Objects pricing: https://developers.cloudflare.com/durable-objects/platform/pricing/
- Pages limits: https://developers.cloudflare.com/pages/platform/limits/

## 16. AI developer handoff

Every developer must receive:
1. This architecture.
2. The actual current repository and Git history.
3. Current file tree.
4. Shared interfaces and configuration.
5. Previous developer handoff.
6. One exact, testable next assignment.

Every handoff must report changed files, dependencies, integration instructions, tests actually run and their real results, limitations, updated tree and next assignment.

Prohibited: separate repositories/prototypes, unapproved interface changes, unverified dependency claims, invented test results, credential collection or bypassing age restrictions.

## First implementation assignment

MiniMax M3.1 Flash is the primary implementation developer.

Phase 0 only:
1. Inspect the current repository and this architecture.
2. Confirm the repository is empty or map any existing files before changing anything.
3. Establish npm workspaces with one root package-lock.json.
4. Verify and pin compatible dependency versions; record exact versions and verification dates.
5. Configure TypeScript and Vite.
6. Create the smallest browser entry point.
7. Initialise Babylon.js with a camera, hemisphere light and clear background.
8. Initialise Rapier and create an empty world with gravity (0, -9.81, 0).
9. Add CI for clean install, typecheck and production build.
10. Run available commands and report actual output. Do not claim browser verification unless a browser was actually opened and checked.

Do not implement vehicle physics, terrain, multiplayer, PWA asset caching or backend features in Phase 0. Do not create a separate prototype.

**Phase 0 acceptance:** the single repository installs reproducibly, typechecks, builds, and the browser displays the blank initial Babylon.js scene with Rapier initialisation confirmed. No game feature is claimed complete.
