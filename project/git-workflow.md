# Trunk-Based Development

## Branch & Worktree Model

- **`main`** is the single, always-releasable trunk.
- No `develop`/`release/*` branches; releases are tags on `main`.
- One task = one worktree with short-lived branch (`feature/<short-name>`, lifetime ≤ 1–2 days).
- Branches created from `origin/main` and squash-merged back.
- Incomplete features go behind feature flags — trunk must stay safe.

## Pre-Commit Validation Flow

Before each commit, the following validations run:

### 1. `lintstaged` (via Husky)

- `package.json` config: `"*.cs": ["dotnet format"]`
- Automatically formats staged `.cs` files with `dotnet format`
- Runs before the commit message is even entered

### 2. Pre-commit Hook (`.husky/pre-commit`)

- Scans staged `.cs` files for code rule violations
- Checks for: `async void`, `Convert.Parse` without `TryParse`, `float`/`double` for money, `nameof()` usage
- Prints warnings but allows commit if no critical errors
- References `.opencode\skill\code-rules-validation\SKILL.md` for rule definitions

### 3. Code Rules Reference

- `project/code-rules.md` - Complete C# best and bad practices
- Agents can read `.opencode\skill\code-rules-validation\SKILL.md` for skill details
- Ensures trunk stability with enforced coding standards

### Workflow Steps:

1. Create worktree: `git worktree add ../feature/<short-name>`
2. Make changes and `git add` (→ `lintstaged` runs `dotnet format`)
3. Run `git commit` (→ pre-commit hook validates code rules)
4. If warnings: fix and `git commit --amend` or `git commit --fixup`
5. Push and create squash-merge PR

## Feature Flags

- Incomplete features must go behind feature flags
- Trunk (`main`) must always remain in a releasable state
- Feature flags toggled via configuration, not commented-out code

## NuGet Package Versions (CPM)

- Versions declared **only** in `Directory.Packages.props`
- Reference packages in `.csproj` without `Version=`
- Never override package versions in individual `.csproj` files

## Repository & Outbox Pattern

- Interfaces in `Domain/Repositories/`, implementations in `Infrastructure/Repositories/`
- Domain events persisted to `OutboxMessages` within same transaction
- `WorkerService` publishes events to RabbitMQ - never from handlers directly

## Testing

- **Unit tests**: NUnit + FakeItEasy in `Tests/`
- **Integration tests**: xUnit in `IntegrationTests/`
- Do not mix test frameworks per project
- Integration tests: use existing factory as `IClassFixture`, unique test data (no fixed CIs)
- No `dynamic` usage in tests

## Domain Language

- Entities and methods use Spanish (`Paciente`, `Crear`, `PlanAlimenticio`)
- Controllers and API contracts may mix English - maintain consistency