# Code Rules - GestionClinicaNutricionalService

## C# / .NET Best Practices (Essential)

- **Use `nameof()`** instead of hard-coded strings for property names
- **Avoid `async void`**: Only use for event handlers; prefer `async Task`
- **Use `decimal`** for monetary values, not `double` or `float`
- **Configure `await` properly**: Use `ConfigureAwait(false)` in library code
- **Nullable reference types**: Enable and use `?` liberally
- **Use `record` types** for immutable data models (commands, events, DTOs)
- **Prefer `string.Create`** or interpolation `$$` over string concatenation
- **Use `TryParse`** instead of `Parse()` with try-catch
- **Dispose `IDisposable`** resources properly with `using` statements
- **Mark public types as `sealed`** unless inheritance is intentional

## C# / .NET Bad Practices to Avoid (Essential)

- **`async void` methods** (except event handlers) - causes unhandled exceptions and difficult debugging
- **Hard-coded strings** for property names in `INotifyPropertyChanged` or exception messages - use `nameof()`
- **Using `dynamic`** extensively - defeats compile-time type safety and performance
- **`float` instead of `decimal`** for money - leads to precision issues
- **Not disposing `IDisposable`** - resource leaks
- **`Convert.Parse()`** without try-catch; prefer `TryParse()` for safety
- **Returning `null`** from methods that should return collections - prefer returning `empty()` or `Collection.Empty`
- **`throw`** without a message or with generic exceptions - use specific exception types with meaningful messages
- **Deep inheritance hierarchies** - favor composition over inheritance
- **`float`/`double` for financial calculations** - use `decimal`
- **Ignoring `ConfigureAwait(false)`** in library code - can cause performance issues and deadlocks
- **Mixing `string`, `StringBuilder`, and `string.Format`** unnecessarily - use string interpolation `$$` or `string.Create`
- **Overusing static classes** for everything - consider if a static method truly belongs in a static class
- **Suppressing FxCop/CodeAnalysis warnings** without fixing the underlying issue
- **Not leveraging `Span<T>` and `ReadOnlySpan<T>`** for performance-critical code in .NET 8+