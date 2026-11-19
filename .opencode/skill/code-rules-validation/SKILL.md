# Code Rules Validation Skill

**Description**: Validates C#/.NET code rules before commit. Scans staged files for common anti-patterns and best practices.

**When to use**: 
- Before committing C# code in the GestionClinicaNutricionalService project
- When running `git commit` with staged `.cs` files
- During code review preparation for .NET files

**Configuration**:
- Pre-commit hook at `.husky/pre-commit` runs this skill's validation logic
- `package.json` lintstaged config: `"*.cs": ["dotnet format"]`
- Rules documented in `project/code-rules.md`

**Validation Checks**:
1. **async void anti-pattern** - Detects `async void` in staged .cs files (except event handlers)
2. **Convert.Parse vs TryParse** - Flags `Convert.Parse()` without try-catch
3. **Monetary types** - Warns about `float`/`double` used for financial values (recommends `decimal`)
4. **nameof usage** - Checks for `string.IsNullOrEmpty` without `nameof()` pattern
5. **Code rules reference** - References `project/code-rules.md` for full rule set

**Related Files**:
- `.husky/pre-commit` - Main validation script
- `package.json` - lintstaged formatting configuration
- `project/code-rules.md` - C# best and bad practices documentation
- `GestionClinicaNutricionalService/*.cs` - Source files validated

**Example Trigger Keywords**:
- "commit validation"
- "code rules check"
- "staged C# files"
- "dotnet format"
- "lintstaged"