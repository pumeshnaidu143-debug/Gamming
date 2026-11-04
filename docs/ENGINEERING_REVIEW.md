# Architecture V1.1 — Engineering Review Notes

**Review date:** 2026-11-04  
**Status:** V1.1 is accepted as the working architecture for Phase 0. The notes below are required clarifications for implementation. They do not authorise a second architecture or separate project.

## Required clarifications

### 1. Transform interpolation
Do not linearly blend an entire transform as if position and rotation were the same data type. Interpolate translation with vector lerp and orientation with quaternion slerp (or a validated normalized quaternion interpolation). Scale can be interpolated separately if needed.

### 2. Determinism wording
A fixed timestep improves repeatability but does not guarantee universal deterministic simulation. Rapier's npm documentation explicitly says cross-platform determinism is not guaranteed. Describe the local simulation as repeatable under a matched runtime/build, initial state and input sequence to the extent validated; do not promise bit-exact cross-device results.

### 3. Network interpolation clock
Remote-player interpolation must use a dedicated snapshot buffer and network interpolation delay. It must not reuse the local physics accumulator alpha as though network snapshots arrive at physics cadence.

### 4. Network tick and sequence validation
Use input sequence numbers to reject duplicate or out-of-order input messages. Treat client tick/timestamp as hints, not authoritative time. The host owns the simulation tick, validates acceptable input windows, buffers or schedules inputs, and rejects implausibly future or stale messages. Do not assume client and host clocks are synchronised.

### 5. Coordinate contract before vehicle code
The V1.1 architecture must explicitly lock handedness, up axis, forward axis, wheel axle axis, wheel index order, steering sign and yaw convention in a shared contract before physics or vehicle visuals are implemented. Previous drafts used differing assumptions; no developer should silently choose one.

### 6. Guest session credential handling
A random guest ID is not authentication. Use a server-issued short-lived, signed session token and validate it for every cloud save/load action. Do not log tokens. Prefer an in-memory bearer token with a deliberate renewal flow; if persistence is required, document the threat model and mitigations. Client-submitted progression remains untrusted.

### 7. Advertising activation
A publisher ID is not a secret and is not proof of approval. The ad module must remain disabled until a guardian-managed eligible publisher account is approved and provider requirements are met. Keep ad-provider scripts/configuration out of active gameplay until then.

### 8. Physics tick budget
The 60 Hz physics rate remains fixed by the V1.1 decision. Low-end devices must be profiled; if they cannot sustain it, reduce the amount of simulation work per tick, vehicle/world complexity or visual cost. Do not quietly lower the simulation rate or claim it is validated without measurements.

### 9. Dependency candidates
The V1.1 dependency ranges are explicitly pending. In particular, the candidate references to Babylon 7.x and Rapier 0.14.x should not be treated as current approved versions. npm listings observed on 2026-11-04 showed newer release lines (Babylon core 9.32.1, loaders 9.33.2, Rapier compat 0.21.0, TypeScript 7.0.2, Vite 8.3.5). These are listing snapshots only, not a compatible tested set. MiniMax must verify peer/runtime compatibility and Node requirements, then pin one working set in the root package manifest and lockfile.

## Current Cloudflare references

Figures below are snapshots from official documentation viewed 2026-11-04 and must be checked again before deployment:
- D1 Free: 5 million rows read/day, 100,000 rows written/day and 5 GB total storage. https://developers.cloudflare.com/d1/platform/pricing/
- Workers Free: 100,000 requests/day and 10 ms CPU time per invocation. https://developers.cloudflare.com/workers/platform/limits/
- Durable Objects Free: SQLite-backed objects are available on Free; 100,000 requests/day and 13,000 GB-seconds/day. https://developers.cloudflare.com/durable-objects/platform/pricing/
- Pages Free: 500 builds/month, one concurrent build, 20,000 files/site and 25 MiB maximum per site asset. https://developers.cloudflare.com/pages/platform/limits/

The free plan has hard quotas. Exceeding free D1 limits can make queries fail; it must not be treated as unlimited capacity. Durable Objects Free supports the SQLite storage backend; do not assume the key-value backend is available on Free.

## Phase 0 is permitted

The above notes do not block the first minimal project foundation. They must be reflected in the shared decisions before the affected systems are implemented. Phase 0 should avoid vehicle, world and networking assumptions beyond package initialisation.
