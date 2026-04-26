---
trigger: always_on

---

# Code Rules

## Technologies used in project

The application is to be written in C# using the following frameworks:

{

1. AvaloniaUi

2. ReactiveUI

3. xUnit

}

It is meant to use routing from ReactiveUI and a feature-oriented folder architecture.

## Coding rules

1. You are a highly skilled software engineer who prioritizes clean code.

2. You pay particular attention to keeping functions short. Your primary goal is to write flat code. Instead of building deeply nested structures so if you find that logic cannot be simplified without nesting try extract the inner block into a separate, dedicated function/method.

3. Names should be self-explanatory and communicate intent. Prioritize clarity over brevity, but avoid redundancy and noise words. A name should be as short as possible, but not shorter than what is required to understand its purpose at a glance. For example, numberOfRemainingFreeHours is far superior to h. It is better to have a descriptive, long name than an ambiguous one that fails to communicate intent.

4. Adding comments within a function or method body is strictly forbidden. Logic should be so clear and names so expressive that internal comments are redundant. You still can use XML Documentation comments (///) on top of fucntion/method, but even then, prioritize making the code self-documenting through better naming and structure.

5. Services should remain lean. If a service is unlikely to maintain high cohesion, prefer Use Cases over generic Services

6. Language Requirements
   All naming conventions (variables, functions, classes) and comments within the code must be in English.

7. **Prefer Enums over Constants/Strings:** Whenever a variable can hold a limited set of predefined values (e.g., ShiftType, EmployeeRole, DayOfWeek), **always** use a strongly-typed `enum`. Do not use `string` or `int` constants for these purposes.

8. There must be two blank lines separating the last property (or backing field) from the constructor or the first method. This creates a clear visual boundary between the class state and its behavior.

9. Avoid Legacy Patterns. Never suggest deprecated patterns or "old-school" boilerplate if a modern, cleaner alternative exists (e.g., use `ReactiveUI.Fody` instead of manual `RaiseAndSetIfChanged`).

10. Use suffix DTO only for models which are used for network communication

11. Models shouldn't use suffix "model". Better to name model Malpa than MalpaModel

12. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:

- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:

- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

## Threads and Asynchrony

### Task Cancellation

Always use the `CancellationToken` pattern for canceling asynchronous operations or long-running tasks.
Avoid useing boolean flags or direct `Task` disposal to stop asynchronous operations. Pass a `CancellationToken` to methods that support it.

## Binding strategy

* **Strict Prohibition**: Do not use `{Binding ...}` syntax in XAML (`.axaml`) files for dynamic data. - 

* **Code-Behind Bindings**: All data bindings, command bindings, and event-to-command mappings must be implemented in the View's Code-Behind (`.axaml.cs`) using ReactiveUI's type-safe binding methods. -

* **Required Pattern**: Use `this.Bind()`, `this.OneWayBind()`, and `this.BindCommand()` inside a `this.WhenActivated()` block. - 

* **Memory Management**: Every binding must be followed by `.DisposeWith(disposables)` to ensure proper cleanup and prevent memory leaks.

## Anti legacy rules

### reactive UI

- **ReactiveUI Source Generators (Fody/Generation):** Direct implementation of `INotifyPropertyChanged`, manual `RaiseAndSetIfChanged`, or manual `ReactiveCommand` instantiation is strictly forbidden.
  - **Property Declaration:** Use the `[Reactive]` attribute on private fields.
  - **ReadOnly Properties (OAPH):** Use the `[ObservableAsProperty]` attribute.
  - **Commands:** Use the `[ReactiveCommand]` attribute on private methods. This automatically generates a `ReactiveCommand` property with the appropriate name

Correct Pattern:

```csharp
//...
using System.Reactive.Linq;
using ReactiveUI;
using ReactiveUI.SourceGenerators;

//...

public partial class ExampleViewModel : ViewModelBase
{
    [Reactive]
    private string _firstName = string.Empty;

    [ObservableAsProperty]
    private string? _fullName;

    // ✅ The generator creates "public IReactiveCommand SaveCommand"
    [ReactiveCommand]
    private async Task Save()
    {
        // Business logic for McDonald's Roster
        await Task.Delay(100); 
    }

    public ExampleViewModel()
    {
        this.WhenAnyValue(x => x.FirstName)
            .Select(name => $"User: {name}")
            .ToProperty(this, x => x.FullName);
    }
}
```
