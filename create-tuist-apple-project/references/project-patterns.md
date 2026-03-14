# Tuist Project Patterns

## Use This Reference For

- Choosing between a single-project setup and a modular workspace
- Naming initial targets and folders
- Drafting a minimal manifest set for a fresh Apple-platform project

## Single App Pattern

Use this when the user wants to start quickly and has not asked for modularization.

Recommended shape:

```text
MyApp/
├── Project.swift
├── Sources/
│   └── App/
├── Tests/
│   └── AppTests/
```

Recommended targets:

- `MyApp`: app target
- `MyAppTests`: unit-test target
- `MyAppUITests`: optional UI-test target only when requested

Good trigger examples:

- "iOS 앱 프로젝트를 Tuist로 시작해줘"
- "SwiftUI 앱 뼈대만 빨리 만들어줘"

## Modular Pattern

Use this when the user explicitly wants feature separation, shared modules, or a larger team-oriented structure.

Recommended shape:

```text
MyApp/
├── Workspace.swift
├── Projects/
│   ├── App/
│   │   ├── Project.swift
│   │   ├── Sources/
│   │   └── Tests/
│   └── Core/
│       ├── Project.swift
│       ├── Sources/
│       └── Tests/
```

Start with only these modules unless the user asked for more:

- `App`: application target and composition root
- `Core`: shared models, clients, utilities

Add feature modules only when there is a concrete reason, such as:

- a requested domain split
- reusable UI/framework logic
- parallel work across multiple areas

## Default Decisions

If the user is vague, prefer:

- Tuist version: latest stable
- platform: iOS
- UI framework: SwiftUI
- dependency strategy: no external dependencies in the first scaffold
- tests: unit tests only
- bundle identifier: derive from the provided organization or use a clearly marked placeholder

## Manifest Checklist

Ensure each generated manifest answers these points:

- Which platform does the target build for?
- What is the product type?
- What is the deployment target?
- Which source files belong to the target?
- Which test target depends on which app/framework target?
- Which bundle identifier is used?

## Common Mistakes

- Creating `Workspace.swift` for a trivial one-target app
- Adding many empty modules up front
- Using source globs that do not match any files
- Forgetting to align scheme, target, and folder naming
- Creating tests without a valid dependency on the app/framework target
