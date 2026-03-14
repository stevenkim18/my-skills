---
name: create-tuist-apple-project
description: Create a new Apple platform project scaffolded with Tuist. Use when Codex needs to start a fresh iOS, macOS, watchOS, tvOS, or multi-platform app with Tuist, define an initial folder/module layout, generate `Project.swift` or `Workspace.swift`, prepare local and test targets, or give setup commands for a brand-new Tuist-based codebase.
---

# Create Tuist Apple Project

## Overview

Use this skill only for a brand-new Tuist-based Apple app. Gather the minimum project decisions, scaffold the smallest working structure, then validate that Tuist can generate the project cleanly.

This skill is not for migrating an existing Xcode or Swift package repository into Tuist. If the directory already looks like an app codebase, stop and clarify that the task is migration or restructuring instead of fresh scaffolding.

## Workflow

1. Inspect the directory and confirm this is a new-project request.
2. Separate missing inputs into:
   - required before writing files
   - safe defaults you can assume
3. Resolve the exact Tuist version only when the user asked for `latest stable` or said the version does not matter.
4. Create the leanest Tuist structure that satisfies the request.
5. Run `tuist install` when needed, then `tuist generate`, and fix obvious manifest or path issues immediately.

## Required Inputs vs Defaults

Ask only for information that materially changes the scaffold and cannot be safely inferred.

Required before scaffolding unless the user already gave them:

- Product name
- Tuist version choice: a specific version, `latest stable`, or "version does not matter"

Safe defaults when the user gives partial requirements:

- Primary platform: `iOS`
- Minimum OS version: `iOS 17.0`
- App style: `SwiftUI`
- Module layout: one app target plus one unit-test target
- Directory layout: `Projects/App` for simple cases
- Extra modules: no starter `Feature` modules beyond `App` and `Core`
- State-management library: no TCA unless explicitly requested
- Tests: unit tests only

Only ask follow-up questions when the answer changes the generated structure in a meaningful way:

- Ask for the minimum OS version only when the user mentioned a platform but no deployment target and the default might be risky for the stated goal.
- Ask whether to create starter `Feature` modules only after the user chose a modular layout.
- Ask whether to include TCA only when the user mentioned TCA, a reducer-driven architecture, or asked for a prewired dependency graph.

Separate user-provided decisions from your own defaults in the response.

## Stop Conditions

Stop using this skill alone and tell the user the directory does not look empty enough for a fresh scaffold if you find any of these:

- Existing `Project.swift`, `Workspace.swift`, `Package.swift`, or `Tuist.swift`
- Existing `*.xcodeproj` or `*.xcworkspace`
- Existing `Tuist/`, `Sources/`, `Tests/`, or `Projects/` directories that already contain app code
- A Git repository whose root already contains app targets, manifests, or a nontrivial source tree

At that point, explain that the task is no longer "create a new project" and should be handled as migration, extension, or cleanup.

## Implementation Rules

- Prefer the smallest working Tuist setup over speculative architecture.
- Keep naming consistent across folder names, target names, bundle identifiers, schemes, and test targets.
- Do not ask unnecessary confirmation questions when a documented default is safe.
- If the user says the version does not matter, treat that the same as `latest stable`.
- If the user says `latest stable`, resolve the current stable Tuist release from an official source before writing local tooling files or install commands.
- Do not guess the current stable Tuist version from memory.
- Prefer an explicit execution path for the chosen version when local tooling uses `mise`.
- If a version file already exists, update it only when that aligns with the user's request; do not silently replace a pinned version.
- If Tuist is unavailable locally, say so and provide the exact install and generate commands the user should run.
- When local validation is blocked by environment limitations, distinguish that from a manifest error.

## Recommended Project Shapes

For a simple app, prefer:

- `Project.swift`
- `Tuist.swift` only when needed by the chosen setup
- `Workspace.swift` only when it adds clarity
- `Sources/`
- `Tests/`

For a modular app, prefer:

- `Projects/App/`
- `Projects/Core/`
- `Projects/Feature/` only when the user asked for starter feature modules
- additional feature or framework folders only when the user asked for them

If the user wants TCA from the start:

- Add it explicitly as a dependency rather than implying it from `SwiftUI`
- Keep `App` as the composition root
- Scaffold the smallest reducer-based example that proves the wiring works

Read [references/project-patterns.md](references/project-patterns.md) when deciding between single-project and modular layouts, choosing starter targets, or drafting example manifests.
Read [references/current-tuist-dependency-flow.md](references/current-tuist-dependency-flow.md) when the task involves current Tuist dependency setup, `mise` pinning, TCA package wiring, or generation validation.

## Execution

When creating the project:

1. Inspect the current directory so you do not overwrite an existing setup.
2. If the user requested `latest stable` or said the version does not matter, resolve the current stable Tuist CLI release from an official source before writing `mise.toml` or install commands.
3. Detect how Tuist should run locally:
   - use `mise exec -- tuist` when the project already uses `mise` or when you pin Tuist in `mise.toml`
   - otherwise try `tuist` directly
4. Create only the directories and starter files needed for the requested shape.
5. Write manifests with explicit platform, product type, bundle identifier, deployment target, and source or test globs.
6. Add minimal app entry files and one smoke test so the graph is coherent.
7. If the user requested TCA, wire the dependency into the smallest working target set.
8. For current Tuist dependency setups, prefer `Tuist/Package.swift` for Swift Package dependencies and consume products with `.external(name: ...)`.
9. If `Tuist/Package.swift` exists, run `tuist install` before `tuist generate`.
10. Run `tuist generate`.
11. If generation succeeds, report the generated workspace or project file and the next command to open it.

## Validation

After scaffolding:

- Confirm the pinned Tuist version in `mise.toml`, or the chosen execution path, matches the version you resolved earlier.
- Run `tuist install` first when `Tuist/Package.swift` exists, then run `tuist generate`.
- Prefer `xcodebuild -list -workspace ./<Name>.xcworkspace` as a lightweight sanity check when a workspace was generated.
- If available, run a lightweight build or test command for the main scheme.
- If `xcodebuild` fails with CoreSimulator, codesigning, or sandbox-related access errors, treat that as an environment limitation first.
- Check common failures first: missing paths, mismatched target names, invalid bundle IDs, incorrect deployment targets, or missing files that should match the globs.
- If generation emits duplicate-link warnings, check whether the same static package product is linked both directly and transitively.

If local commands cannot run, still verify the file tree and manifest references manually before handing off.

## Response Shape

Keep the final answer compact and operational:

- what structure you created
- which values came from the user
- which values you assumed as defaults
- which command you used to install, generate, and open the project
- whether validation passed or was blocked by the environment
- what the user should decide next, if anything

## Example Use

Example 1: simple iOS app with defaults

- User intent: "Create a new Tuist app named `Acorn` and the exact version does not matter."
- Ask only for the product name if it is still missing.
- Resolve the current stable Tuist version from an official source.
- Default to `iOS 17.0`, `SwiftUI`, one app target, and unit tests only.
- Scaffold the smallest working app and report those defaults explicitly.

Example 2: modular app with TCA

- User intent: "Create a modular iOS Tuist project named `Beacon`, use latest stable, add TCA, and include one sample feature."
- Do not ask again about TCA or modularity.
- Ask about minimum OS version only if the default could be risky for the user's stated constraints.
- Use `Projects/App`, `Projects/Core`, and one starter feature module.
- Wire TCA through `Tuist/Package.swift`, then run `tuist install` and `tuist generate`.
