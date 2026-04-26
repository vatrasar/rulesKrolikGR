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
