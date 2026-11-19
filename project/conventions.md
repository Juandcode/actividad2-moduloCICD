# Conventions

- **Language**: C# .NET 8 (net8.0)
- **Pattern**: Clean Architecture + DDD + CQRS with MediatR
- **Dependencies**: Point inward. Domain references nothing; Application only Domain; Infrastructure and WebApi the previous ones.
- **NuGet versions**: Declared ONLY in `Directory.Packages.props`; reference packages in `.csproj` without `Version=`.

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

## Naming Conventions (spanish)

- **Entities**: `Paciente`, `PlanAlimenticio`, `ConsultaInicial` (Spanish)
- **Methods**: `Crear`, `Obtener`, `Actualizar`, `Eliminar` (Spanish)
- **Commands/Queries**: Records in `Application/<Feature>/` (e.g., `CreatePacienteCommand`)
- **Handlers**: `CreatePacienteHandler`, `ApproveConsultaHandler`
- **Repositories**: Interfaces in `Domain/Repositories/`, implementations in `Infrastructure/Repositories/`

## CQRS

- One handler per use case.
- Commands/queries are records.
- Handlers must not publish to RabbitMQ directly - use the Outbox pattern.
- The `WorkerService` publishes domain events from `OutboxMessages`.

## Repository Pattern

- Interfaces live in `Domain/Repositories/`.
- Implementations live in `Infrastructure/Repositories/`.
- All repository methods must be `Task`-based async.

## Transactional Outbox

- Domain events are persisted to `OutboxMessages` within the same transaction.
- The `WorkerService` publishes them to RabbitMQ.
- Never publish to RabbitMQ directly from a handler.

## Testing

- **Unit tests**: NUnit + FakeItEasy in `Tests/`
- **Integration tests**: xUnit in `IntegrationTests/`
- Do not mix test frameworks per project.
- Integration tests: use existing factory as `IClassFixture`, unique test data (no fixed CIs), deserialize to DTOs in `IntegrationTests/Setup/`, no `dynamic`.

## Formatting

- `dotnet format` is run via Husky pre-commit hook on `*.cs` files.
- Run `dotnet format GestionClinicaNutricionalService.sln --exclude GestionClinicaNutricionalService/GestionClinicaNutricional.Infrastructure/Migrations/ --no-restore` before committing.
- Husky `prepare` script runs `husky` to set up hooks.

## Git

- **Trunk-Based Development**: `main` is the single always-releasable trunk.
- One task = one worktree with short-lived branch (`feature/<short-name>`, lifetime ≤ 1-2 days).
- Created from `origin/main` and squash-merged back.
- Incomplete features go behind feature flags - trunk must stay safe.
- Never commit secrets, connection strings or `.env`.

## Domain Language

- Entities and methods use Spanish (`Paciente`, `Crear`, `PlanAlimenticio`).
- Controllers and API contracts may mix English as already existing - maintain consistency.