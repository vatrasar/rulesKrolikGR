# Screens and components

## Screens

When in instruction for you I use the word "screen", I mean 3 files:
NameViewModel.cs, NameView.axaml.cs, and NameView.axaml.
So, for example, when I say "screen malpa", I mean the files MalpaViewModel.cs, MalpaView.axaml.cs, and MalpaView.axaml.
The files are usually grouped in a single folder and are responsible for the UI of one screen.

Before starting any work on a screen, you MUST read the documentation comment at the top of its `.axaml.cs` file to understand its purpose, functionalities, and context.

Inside screen folder there may be folder ScreenComponents where you can put folders of components used only by this screen

### Screens and models

File with model can only be at the feature or core level. You can't put models in the screens folder

Custom components should by default be placed in ScreenComponents. You can place them in FeatureComponents or GlobalComponents only when i will directly tell you to do that.

### Screen Documentation

Every screen MUST contain documentation in a comment at the top of the class inside its `.axaml.cs` file. This comment should contain documentation about the screen, including:

- Purpose of the screen (what it does).
- List of functionalities and features available on this screen.
- Information about key UI elements (e.g., buttons that trigger specific actions like theme switching).
- **Navigation (From):** List of screens that can navigate to this screen via routing.
- **Navigation (To):** List of screens that this screen can navigate to via routing.
- Any other relevant information for developers or AI agents to understand the screen's role.

**IMPORTANT:** Whenever you modify a screen (logic, UI, or functionalities), or when navigation paths change (e.g., another screen adds a button navigating to this screen), you MUST update the corresponding documentation comment in the `.axaml.cs` file to reflect these changes. Documentation must always stay in sync with the implementation.

## Components

Components are reusable or isolated UI blocks that are NOT used directly in ReactiveUI routing (they do NOT implement `IRoutableViewModel`).

Every component MUST contain documentation in a comment at the top of the class inside its `.axaml.cs` file, which serves as the documentation for that specific component.

### Components types

There are two types of components you must distinguish:

1. **Smart Components:** They have their own complex logic, state, or actions. They require a full MVVM triad (e.g., `NameViewModel.cs` inheriting from `ViewModelBase`, `NameView.axaml`, and `NameView.axaml.cs`).
2. **Dumb Components (Stateless):** They only display data and have no complex logic. They do NOT have their own ViewModel file. They consist only of `NameView.axaml` and `NameView.axaml.cs`, using `x:DataType` bound directly to a Model or a property of the parent's ViewModel.

### Component Documentation

Every component MUST contain documentation in a comment at the top of the class inside its `.axaml.cs` file. This comment should contain documentation about the component, including:

- Purpose of the component (what it does and why it was created).
- How to use it (inputs/outputs, properties it binds to).
- Information about key UI elements within the component.
- **Used In:** List of screens or other components that use this component.
- Any other relevant information for developers or AI agents to understand the component's role.

**IMPORTANT:** Whenever you modify a component (logic, UI, or functionalities), or when its usage changes (e.g., it is added to a new screen), you MUST update the corresponding documentation comment in the `.axaml.cs` file to reflect these changes. Documentation must always stay in sync with the implementation.

Custom components should by default be placed in ScreenComponents. You can place them in FeatureComponents or GlobalComponents only when i will directly tell you to do that.

#### Component Communication and XAML Binding Rules

1. **Smart Components (Components with their own ViewModel)**

When a Screen (Parent) contains a Smart Component (Child), communication MUST strictly follow ReactiveUI patterns to avoid tight coupling.

* **NO Direct Reference:** A Child ViewModel MUST NOT know about its Parent. Never inject the Parent ViewModel into the Child ViewModel.
* **NO MessageBus:** Do NOT use ReactiveUI MessageBus for Parent-Child communication. It is reserved ONLY for decoupled global events.
* **Observing State & Actions:** If the Child manages state, expose it as a `[Reactive]` property. If it performs an action, expose a `ReactiveCommand`. The parent observes these.
2. **Dumb Components (Stateless Views without a ViewModel)**

Dumb components rely entirely on data passed from the parent view. Only way to pass this data is to set it as dataContext of dumb component. Never set parents viewModel as data context of control, control must stay isolated from parent

* **NO Parent Hacking:** NEVER use `ElementName`, Named References (e.g., `#Root.DataContext`), or `$parent[SpecificParentView]` to escape a broken DataContext scope. This destroys component reusability.
3. **Transition from Dumb to Smart Component**
   
   A Dumb Component MUST be promoted to a Smart Component (requiring its own ViewModel) if it needs to trigger actions that affect the Parent or broader application state. 
   
   - **Action Requirement Rule**: If a component contains interactive elements (e.g., Buttons, Toggles) that trigger logic beyond simple visual state changes, it MUST be a Smart Component.
   - **No Code-Behind Events**: Do NOT use standard C# events in the code-behind (`.axaml.cs`) to communicate with the parent. 
   - **ReactiveCommand Pattern**: Actions must be exposed via `ReactiveCommand` within a dedicated ViewModel. The Parent ViewModel then subscribes to these commands or observes the Child's ViewModel state.
   - **Decision Matrix**: 
     - Pure data display? -> **Dumb Component** (UserControl).
     - Button that deletes a roster entry? -> **Smart Component** (ReactiveUserControl + ViewModel).
     - Input that filters a list in real-time with complex logic? -> **Smart Component**.

4. **Base Class Selection (ReactiveUI vs Standard Avalonia)**
* **Smart Components & Screens:** Classes in the `.axaml.cs` file MUST inherit from `ReactiveUserControl<TViewModel>` (or `ReactiveWindow<TViewModel>`). Do NOT use a plain `UserControl` for these. You must update BOTH the root node in the `.axaml` file (including `x:TypeArguments`) and the class definition in the code-behind `.axaml.cs`.
* **Dumb Components:** MUST inherit from the standard Avalonia `UserControl`. Do NOT use `ReactiveUserControl` for these, as they rely entirely on `StyledProperty` bindings and have no `TViewModel` to resolve.