# Architecture Rules (non-negotiable)

The solution lives entirely in `GestionClinicaNutricionalService/`:

- `GestionClinicaNutricional.Domain` — entities, domain events, repository interfaces
- `GestionClinicaNutricional.Application` — use cases (CQRS with MediatR)
- `GestionClinicaNutricional.Infrastructure` — EF Core, repository implementations, Outbox
- `GestionClinicaNutricional.WorkerService` — Outbox processor → RabbitMQ
- `GestionClinicaNutricionalService.WebApi` — controllers and HTTP contracts
- `Tests/` (NUnit + FakeItEasy) and `IntegrationTests/` (xUnit)

## Dependency direction

Dependencies point inward only:

```
WebApi / WorkerService ──► Application ──► Domain
         ▲                     │
         └── Infrastructure ◄──┘
```

- `Domain` references no other project.
- `Application` references only `Domain`.
- `Infrastructure` and `WebApi`/`WorkerService` reference the inner layers.
- Never introduce reverse references, even "just for convenience".

## Patterns

- **CQRS with MediatR**: one handler per use case (`CreatePacienteHandler`, `ApproveConsultaHandler`, …). Commands/queries are records in `Application/<Feature>/`.
- **Repositories**: interfaces in `Domain/Repositories/`, implementations in `Infrastructure/Repositories/`.
- **Transactional Outbox**: domain events are persisted to `OutboxMessages` within the same business transaction; the `WorkerService` reads them and publishes to RabbitMQ. Never publish to RabbitMQ directly from a handler.

## C# / .NET Code Rules Validation

This project uses a pre-commit validation skill (`.opencode\skill\code-rules-validation\SKILL.md`) that scans staged `.cs` files for common anti-patterns and best practices before each commit. The full rule set is documented in `code-rules.md`.

### Validation Checks (run on `git commit`):

1. **async void anti-pattern** - Detects `async void` in staged files
2. **Convert.Parse vs TryParse** - Flags unsafe parse calls
3. **Monetary types** - Warns about `float`/`double` for financial values
4. **nameof usage** - Checks for proper `nameof()` patterns
5. **Code rules reference** - Full rules in `project/code-rules.md`

### Related Skills:

- `.opencode\skill\code-rules-validation\SKILL.md` - Skill for agents to understand validation flow

### For More Details:

- Review `project/code-rules.md` for complete C# best and bad practices
- Check `.husky/pre-commit` for the validation script implementation
- Consult `package.json` lintstaged config: `"*.cs": ["dotnet format"]`

## Protected folders

Do not modify:

- `GestionClinicaNutricional.Infrastructure/Migrations/` (auto-generated EF Core code)
- `bin/`, `obj/`, `publish/`, `coveragereport/`

## NuGet packages (CPM)

With Central Package Management active, versions are declared **only** in `Directory.Packages.props`. In `.csproj` files, reference packages without `Version=`.

## Scope

Do not add new microservices, projects, packages or CI workflows unless explicitly requested.