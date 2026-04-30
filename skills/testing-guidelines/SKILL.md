---
name: testing-guidelines
description: Provides standards for unit and UI-integrated testing in Avalonia projects, including naming conventions and headless testing setup.
---

## When to use this skill

Use this skill when writing or updating unit tests or UI-integrated tests for Avalonia applications.

## How to use it

1. **Headless Testing:** Use `Avalonia.Headless.XUnit` and `Avalonia.Themes.Fluent` for UI-integrated testing.
2. **Bootstrapping:** Utilize the pre-configured `TestAppBuilder` for headless Avalonia application bootstrapping.
3. **Mocking:** Use `xUnit` and `Moq` for standard unit testing and dependency mocking.
4. **Naming Convention:** Follow the `NameOfTestedFunction_testCondition_expectedResult` pattern for all test methods.

## Testing Standards

### Avalonia.Headless

The project is equipped with `Avalonia.Headless.XUnit` and `Avalonia.Themes.Fluent`. You are encouraged to use these for UI-integrated testing and functional verification. 

*Note: A `TestAppBuilder` has been pre-configured for this library to facilitate headless Avalonia application bootstrapping within the test suite.*

### Test Function Naming

When creating test functions, use the following naming convention:

`NameOfTestedFunction_testCondition_expectedResult`

**Example:**
For a function `GetTimeSlotsList`, a test method should be named:
`GetTimeSlotsList_ForDataWhichArenTSorted_ReturnsSortedTimeSlotList`

## Avalonia & ReactiveUI in tests

When writing UI tests for Avalonia and ReactiveUI in a headless environment, you MUST adhere to the following rules to ensure test stability, complete isolation (FIRST principles), and proper framework initialization.



1.  View Activation (The Window Rule)

Controls in Avalonia do not fire lifecycle events (like `AttachedToVisualTree`) until they are placed in a Window.
**Rule:** Always wrap your `View` inside a `Window` and explicitly call `window.Show()`.

2. The Layout Pass (Before Simulating Clicks)

Standard `RaiseEvent` (e.g., for `Button.ClickEvent`) will fail to propagate to ReactiveUI bindings if the visual tree layout hasn't been calculated.
**Rule:** Always call `window.LayoutManager.ExecuteInitialLayoutPass();` after `window.Show();` and before interacting with UI elements. Do not bypass `RaiseEvent` by manually executing ViewModel commands—test the actual UI interaction.

3. UI Thread Synchronization

Changes in Avalonia UI and ReactiveUI are asynchronous.
**Rule:** Always call `Dispatcher.UIThread.RunJobs();` after showing the window and after every simulated user interaction (like `RaiseEvent`) to flush the UI event queue before asserting.

4. Strict Test Isolation (Global State & Memory Leaks)

Tests must never pollute the global state or leave lingering instances. Modifying `Application.Current` or leaving open Windows will cause subsequent tests to fail or leak memory.
**Rule:** You MUST use a `try...finally` block. 

- **In `try`:** Setup local state, run `Act`, and run `Assert`.
- **In `finally`:** Restore any modified global state (e.g., `RequestedThemeVariant`) to its original value, and explicitly call `window.Close();`.

# 

### Canonical Test Template

Use this structure as a baseline for all View tests:

csharp


```csharp
[AvaloniaFact]
public void ControlName_WhenCondition_DoesExpectedBehavior()
{
 // Arrange
 var app = Application.Current!;
 var originalTheme = app.RequestedThemeVariant; // Save global state
var viewModel = new MyViewModel();
var view = new MyView { ViewModel = viewModel };
var window = new Window { Content = view };

try
{
    // Mutate global state if needed for the test
    app.RequestedThemeVariant = ThemeVariant.Light;
    window.Show();

    // Force layout calculation
    window.LayoutManager.ExecuteInitialLayoutPass();
    Dispatcher.UIThread.RunJobs();

    var myButton = view.FindControl<Button>("MyButtonName");

    // Act
    myButton!.RaiseEvent(new RoutedEventArgs(Button.ClickEvent));
    Dispatcher.UIThread.RunJobs();

    // Assert
    // (Your fluent assertions here)
}
finally
{
    // Cleanup global state and memory
    app.RequestedThemeVariant = originalTheme;
    window.Close();
}
}
```
