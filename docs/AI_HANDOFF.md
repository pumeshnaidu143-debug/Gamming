# AI Developer Handoff Rules

## Single project rule

DIRT FRONTIER has one repository: https://github.com/pumeshnaidu143-debug/Gamming

All AI models are developers in one team. They must modify the same project and respect the approved architecture. No separate demos, prototype repositories or duplicate game engines.

## Before starting an assignment

1. Read docs/ARCHITECTURE.md.
2. Read docs/ENGINEERING_REVIEW.md.
3. Read docs/DEPENDENCIES.md.
4. Inspect the current branch, file tree, latest commits and working files.
5. Read the previous handoff.
6. State what exists, what is incomplete and what the exact assignment requires.
7. Do not infer repository contents from a prompt.

## External AI privacy rule

Only provide public project requirements, architecture and sanitised code necessary for the task. Never send passwords, GitHub tokens, Cloudflare API tokens, private keys, cookies, identity documents, payment information or other private account data to external AI models.

Do not ask the user to paste secrets. Use platform secret stores and environment variables, never committed files or browser bundles.

## Required handoff format

Every assignment ends with:
1. Summary.
2. Complete changed files or an exact commit/PR.
3. Dependency changes.
4. Integration instructions.
5. Commands actually run and their complete relevant results.
6. Known limitations and unresolved issues.
7. Updated file tree.
8. Next exact assignment.

## Verification labels

- VERIFIED: a specified test was actually run and passed.
- PARTIAL: some behaviour or checks passed, but acceptance is incomplete.
- FAILED: a check ran and failed.
- NOT RUN: no execution occurred.
- BLOCKED: required access or input was unavailable.

Never claim tests, browser checks, deployments or integrations succeeded without actually performing them.

## Phase 0 assignment — MiniMax M3.1 Flash

Create the unified npm-workspace foundation only. Inspect the repository first. Verify and pin compatible dependencies. Configure TypeScript, Vite and CI. Initialise a blank Babylon.js scene and Rapier world. Run typecheck/build and, only if browser execution is available, verify the page and console output.

Do not create vehicle physics, terrain, roads, multiplayer, backend features or ads during Phase 0.
