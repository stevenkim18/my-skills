# Version Resolution

Use this reference when the user asks for `latest`, `latest stable`, or says the Tuist version does not matter.

## Rule

- Resolve the current stable Tuist CLI version from an official source before creating files or suggesting install commands.
- Do not rely on memory for the version number.

## Preferred Source Order

1. Official Tuist GitHub releases page for the CLI release
2. Official Tuist documentation, if it clearly states the current install target

## Practical Behavior

- If the project uses `mise`, pin the resolved version in `mise.toml`.
- If the user asked for a specific version, use that exact version instead of resolving latest.
- If both CLI and server releases appear in the same release feed, use the CLI version for local project scaffolding.

## Validation

- Before generating the project, check that the version written to `mise.toml` matches the resolved stable CLI version.
- If Tuist gets installed during the task, verify with `mise exec -- tuist version` or `tuist version` and compare it with the resolved version.
