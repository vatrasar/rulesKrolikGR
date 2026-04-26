---
name: animations-rules
description: Use this skill when you need to create or edit UI animations 
---

## When to use this skill
Use this skill when you need to create or edit UI animations in Avalonia applications, ensuring high‑frequency updates are synchronized with VSync and avoiding legacy approaches like `DispatcherTimer`.

## How to use it
1. Follow the animation best‑practice guidelines below.
2. Use Avalonia's built‑in `Animation` class with `KeyFrames` or `Transitions` for property‑based animations.
3. For continuous updates, prefer `CompositionTarget.Rendering` or `TopLevel.RequestAnimationFrame` instead of timers.
4. Ensure background tasks are cancelled in `OnDetachedFromVisualTree` and manage visibility changes via `OnPropertyChanged`.
5. When animating brushes, avoid placing XAML `<Animation>` inside a `VisualBrush.Visual`.

## Guidelines (derived from animations‑rules.md)

### UI Animations and Timers
- Using `DispatcherTimer` for high‑frequency UI animations (e.g., rotating brushes, moving elements at 60 FPS) is strictly forbidden. This approach is legacy, inefficient, and not synchronized with the screen refresh rate (VSync).
- **Animations:** Use Avalonia's built‑in `Animation` class with `KeyFrames` or `Transitions` for property‑based animations.
- **Continuous Updates:** If you must perform custom rendering updates from code‑behind, use `CompositionTarget.Rendering` or `TopLevel.RequestAnimationFrame` to ensure the logic ticks in sync with the display's VSync.

### UI Component Cleanup
- In Avalonia controls, ensure all background tasks are cancelled in `OnDetachedFromVisualTree`. If a task is tied to visibility, manage it in `OnPropertyChanged` when `IsVisible` changes.

### VisualBrush and animations
- When working with Avalonia UI animations and brushes, remember that XAML `<Animation>` definitions do **not** tick or update continuously if they are placed on elements inside a `VisualBrush` (or `DrawingBrush`). Elements inside a `Visual` property of a `VisualBrush` are not fully connected to the main window's visual tree's render clock. If you need an animated brush (like a rotating gradient or moving element), do **not** put the XAML `<Animation>` inside a `VisualBrush.Visual`.
