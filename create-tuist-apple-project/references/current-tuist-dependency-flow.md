# Current Tuist Dependency Flow

Use this reference when the user asks for a current Tuist scaffold, especially with external Swift packages such as TCA.

## Tuist Version Handling

- When the user asks for `latest stable`, resolve the current stable Tuist release from an official source instead of assuming an old default.
- If the machine uses `mise`, prefer pinning the resolved version in a local `mise.toml` file so the project is reproducible.
- Prefer running Tuist through `mise exec -- tuist` when `tuist` is not already on `PATH`.

## External Dependencies

- In current Tuist setups, prefer declaring Swift package dependencies in `Tuist/Package.swift`.
- After adding or changing `Tuist/Package.swift`, run `tuist install` before `tuist generate`.
- In `Project.swift`, reference package products with `.external(name: "...")`.
- If TCA is requested, add it explicitly rather than assuming it from SwiftUI.

## Practical Modular Setup

- A minimal modular iOS setup can start with:
  - `Workspace.swift`
  - `Projects/App`
  - `Projects/Core`
  - `Projects/Features/<SampleFeature>` when the user asked for starter feature modules
  - `Tuist/Package.swift` when external packages are needed
- Keep the first feature small. One sample feature is enough to prove the composition pattern.

## Validation Notes

- `tuist generate` should produce `<Name>.xcworkspace` for a modular workspace setup.
- `xcodebuild -list -workspace ./<Name>.xcworkspace` is a good low-cost check that the workspace is structurally valid.
- A build step is still useful, but sandboxed environments may fail before compilation because Xcode cannot access CoreSimulator services.
- If generation reports duplicate-link warnings for static package products, check whether both the app target and a dependent framework target link the same external package directly.
