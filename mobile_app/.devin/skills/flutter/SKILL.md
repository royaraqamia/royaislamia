---
name: flutter
description: Best practices for Flutter development, focusing on avoiding Widget tree hell, state management, and performance.
---

# Flutter

## 1. UI Architecture & Widget Composition
- **Extract, Don't Nest:** Never let a `build()` method exceed 60-80 lines of code. If a Widget tree becomes deeply nested, extract sub-trees into their own separate, named `StatelessWidget` classes.
- **Composition over Functions:** Extract widgets into separate `class` definitions rather than using helper methods like `Widget _buildHeader()`. Classes provide their own element in the tree, which is much better for Flutter's rendering performance and debugging.
- **Const Constructors:** ALWAYS use the `const` keyword for widgets that do not change (e.g., `const SizedBox(height: 16)`, `const Text('Hello')`). This prevents unnecessary rebuilds.

## 2. State Management & Logic Separation
- **No Logic in UI:** The `build()` method must only map state to UI. NEVER put HTTP requests, database calls, or complex business logic inside `onPressed` handlers directly in the UI. 
- **Delegate to Controllers/ViewModels:** UI events should call methods on a Controller/ViewModel (using Riverpod, Provider, or BLoC), which then calls the Data/Repository layer.
- **Keep StatefulWidgets Minimal:** Only use `StatefulWidget` for local, ephemeral UI state (like animations, tab controllers, or text field focus). For global or app-level data, use your designated state management solution.

## 3. Data & Supabase Handling
- **Typed Models:** Always parse JSON/Supabase responses into strongly-typed Dart models using `fromJson` and `toJson` factory methods. NEVER pass raw `Map<String, dynamic>` data into the UI.
- **Repository Pattern:** Wrap all Supabase SDK calls inside a Repository class (e.g., `UserRepository`). The UI should never import the Supabase package directly.

## 4. Performance & Scalability
- **List Rendering:** ALWAYS use `ListView.builder` or `SliverList` for long lists. Never use `ListView(children: [...])` or `Column` for dynamic/long lists, as it renders off-screen items and causes memory leaks.
- **Avoid Blocking the Main Thread:** Use `Isolate.run()` for heavy synchronous computations (like parsing massive JSON files or image processing) so the UI doesn't freeze or drop frames.

## 5. Anti-Patterns to Avoid
- Calling `setState()` synchronously during a build phase.
- Leaving "debugPrint" or "print" statements in production code. Use a proper logging package.
- Creating "God Widgets" that manage the state of an entire feature in a single file.