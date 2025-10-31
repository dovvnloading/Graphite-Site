# Changelog

All notable changes to this project will be documented in this file.

---


### `[Beta v.0.5.6]` - 2025-10-30

This release delivers the first fully functional plugin, "System Prompt," which allows for dynamic, per-conversation customization of the AI agent's behavior. This milestone validates the new plugin architecture and introduces a powerful new layer of user control.

#### Architectural Refactor

*   **Integrated Node Context into AI Agent Logic for Dynamic System Prompts.**
    *   **Problem:** The core `ChatWorker` was stateless and unaware of the canvas, using a single, hard-coded system prompt for all interactions.
    *   **Solution:** The `ChatWorker` and `ChatWorkerThread` have been refactored to accept the `current_node` as context. Before generating a response, the worker now traverses the node's hierarchy to find its root and checks for a connected `SystemPromptConnectionItem`. If found, it uses the content of the attached `Note` as the system prompt for that specific API call, effectively overriding the default behavior for that entire conversation branch.

#### Added

*   **Implemented the "System Prompt" Plugin.**
    *   **Feature:** Users can now add a special "System Prompt" node to any conversation branch via the "Plugins" menu. This node, a specially styled `Note` item, allows the user to write custom instructions that override the default AI persona for that specific branch.
    *   **Implementation:** A new `SystemPromptConnectionItem` class was created to visually and logically link a `Note` to a `ChatNode`. The plugin logic in `PluginPortal` handles the creation of these items. The core AI agent logic in `graphite_agents.py` was then updated to detect and utilize these connections, creating a fully integrated and context-aware system.

#### Changed

*   **Redesigned the System Prompt Connector for Professional Visual Feedback.**
    *   **Problem:** The initial version of the `SystemPromptConnectionItem` was a visually jarring, static dashed line that did not update when nodes were moved.
    *   **Solution:** The connector has been completely redesigned. It is now a smooth, S-shaped Bezier curve that dynamically updates its path when connected nodes are moved. A `QVariantAnimation` was added to create a subtle, continuous pulsing glow effect, visually signifying the flow of instructions from the prompt to the conversation, providing an elegant and intuitive user experience.

#### Fixed

*   **Resolved Multiple Critical Crashes Related to the New Plugin System.**
    *   **Problem:** A `TypeError` occurred because `QVariantAnimation` was incorrectly parented to a `QGraphicsItem`. After fixing this, a `RuntimeError` would occur because the animation was not being stopped when the connection item was deleted, leading it to call methods on a destroyed object.
    *   **Solution:** The animation's constructor was corrected to be parentless. More importantly, the `itemChange` method in `SystemPromptConnectionItem` was overridden to detect when the item is being removed from the scene. It now explicitly calls `.stop()` on the animation, properly managing its lifecycle and preventing any further memory-related crashes.
*   **Fixed a Bug Where System Prompt Connectors Would Not Update When Nodes Moved.**
    *   **Problem:** The scene's update logic was not aware of the new `SystemPromptConnectionItem` type, causing the connectors to remain static when their attached nodes were moved.
    *   **Solution:** The `ChatScene.update_connections` method was updated to iterate over and update `system_prompt_connections`. Additionally, the `Note.itemChange` method was modified to notify the scene of position changes, ensuring all attached connection types are correctly redrawn.

---

### `[Beta v.0.5.6]` - 2025-10-30 (Canvas Interactivity and Alignment Overhaul)

This update introduces a suite of powerful canvas manipulation features, including grid snapping, smart alignment guides, and orthogonal connection routing. These tools provide users with granular control over node layout and improve the organization of complex graphs.

#### Architectural Refactor

*   **Centralized Snapping and Alignment Logic into `ChatScene`.**
    *   **Problem:** Snapping behavior was previously implemented inconsistently within individual `QGraphicsItem` classes (`Note`, `ChartItem`), leading to code duplication and making it impossible to toggle globally.
    *   **Solution:** All snapping and alignment logic has been removed from individual items and consolidated into a single, authoritative `snap_position` method within `ChatScene`. Movable items now delegate their position changes to this method, which intelligently applies grid snapping and smart guide alignment based on the user's settings.

#### Added

*   **Implemented "Snap to Grid" Functionality.**
    *   **Feature:** A new "Snap to Grid" checkbox in the "Controls" panel allows users to toggle grid snapping for all movable items. When enabled, dragging an item will cause it to snap to the nearest grid intersection.
*   **Introduced "Smart Guides" for Dynamic Alignment.**
    *   **Feature:** When the "Smart Guides" checkbox is enabled, moving an item will now display temporary alignment lines. These guides appear when the item's edges or center align with other items on the canvas, enabling precise and intuitive layout adjustments.
*   **Added "Orthogonal Connections" Routing Option.**
    *   **Feature:** A new "Orthogonal Connections" checkbox in the "Controls" panel switches the main `ConnectionItem` rendering style. When enabled, connections are drawn as right-angled lines instead of curves, offering a more structured, schematic-like visual appearance.

#### Changed

*   **Expanded the "Controls" Panel with New Options.**
    *   **UI:** The `GridControl` widget has been redesigned to include a new "Alignment & Routing" section, which houses the checkboxes for Snap to Grid, Smart Guides, and Orthogonal Connections.
    *   **Behavior:** The "Controls" panel is now hidden by default on application startup to provide a cleaner initial workspace. The corresponding toolbar button's state is correctly set to `False` on launch.

#### Fixed

*   **Resolved a Long-Standing Bug Causing a "Stuck" Panning Cursor.**
    *   **Problem:** The panning cursor (a closed hand) would occasionally fail to revert to the default arrow cursor if the middle mouse button was released outside the application window.
    *   **Solution:** The `mouseMoveEvent` in `ChatView` now includes a state-correction check. It verifies the actual mouse button state against the application's internal panning flag and forcibly resets the cursor if a discrepancy is detected, ensuring the UI state remains synchronized with user actions.

---

### `[Beta v.0.5.6]` - 2025-10-30 (Axiom Demo Mode and Rate Limiting)

This update introduces "Axiom" mode, a pre-configured, rate-limited demonstration of the Gemini API. It allows all users, regardless of license status, to immediately test the application's cloud-based AI capabilities without needing their own API key.

#### Architectural Refactor

*   **Expanded the Licensing System to Manage Usage-Based Rate Limits.**
    *   **Problem:** The `LicenseManager` was only designed to track perpetual states like trial periods and license validity. It had no mechanism for tracking transient, usage-based metrics.
    *   **Solution:** The `LicenseManager`'s persistent state file (`session.dat`) has been updated to include an `axiom_usage` object. This object tracks the number of API calls (`count`) and the start of a rolling 4-hour window (`window_start`). New methods (`can_use_axiom`, `record_axiom_usage`, `get_axiom_reset_time_str`) were added to manage this data, ensuring that rate limits are correctly enforced and reset.

#### Added

*   **Introduced "Axiom" Mode for Instant API Access.**
    *   **Feature:** A new "Axiom" option has been added to the mode selection dropdown. This mode utilizes a hard-coded developer API key for Gemini's `gemini-flash-latest` model.
    *   **Implementation:** The core `api_provider` was modified to recognize the "Axiom" provider type. When selected, it initializes the Gemini client with the developer key and routes all chat requests to the pre-configured model, bypassing the need for user-provided settings.
*   **Implemented a Non-Modal Notification Banner for UI Feedback.**
    *   **Feature:** A custom `NotificationBanner` widget now provides unobtrusive feedback, such as rate-limit warnings. It animates up from the bottom of the window and automatically dismisses after a set duration.
    *   **Implementation:** The `send_message` and `regenerate_node` methods in `ChatWindow` now check `license_manager.can_use_axiom()` before proceeding. If the limit is reached, the new banner is displayed with a message indicating when the limit will reset, preventing a disruptive `QMessageBox` from appearing.

#### Changed

*   **Separated User License Status from API Mode Functionality.**
    *   **Clarification:** The Axiom mode's rate limit of 3 messages per 4 hours applies universally to all users. A user with a paid license who requires unlimited API access is expected to use the "API Endpoint" mode and provide their own key.
    *   **Behavior:** The "Settings" button in the toolbar is now disabled when Axiom mode is active, reinforcing that it is a pre-configured demo with no user-editable options.

### `[Beta v.0.5.5]` - 2025-10-30

This release introduces a foundational plugin system and refines the main toolbar's visual design for a cleaner, more professional user experience. These changes create a scalable architecture for future features while improving the application's overall aesthetic.

#### Architectural Refactor

*   **Established a Decoupled Plugin Architecture with a Central `PluginPortal`.**
    *   **Problem:** The application lacked a dedicated, scalable system for adding new tools or features, which would lead to code coupling and maintenance issues.
    *   **Solution:** A new `graphite_plugin_portal.py` module was created, housing a `PluginPortal` class. This class is responsible for discovering, managing, and executing all plugins. It acts as a clean architectural boundary, allowing new features to be added as self-contained "plugins" without modifying the core application logic. The main window instantiates the portal, providing a controlled context for plugins to interact with the application.

#### Added

*   **Added a "Plugins" Dropdown Menu to the Main Toolbar.**
    *   **Feature:** A new "Plugins" menu now appears on the main toolbar, providing a centralized and user-accessible entry point for all integrated tools and extensions.
    *   **Implementation:** The `setup_toolbar` method in `graphite_app.py` was updated to include a `QComboBox` for plugins. This dropdown is dynamically populated with a list of available plugins retrieved from the `PluginPortal`. Selecting an item triggers the execution of the corresponding plugin and resets the menu, creating an intuitive action-oriented experience.

#### Changed

*   **Refined Toolbar UI for a Cleaner, More Professional Aesthetic.**
    *   **Problem:** The main toolbar buttons used solid background colors, which appeared dated and visually busy. Furthermore, the newly added "Plugins" menu was initially implemented as a `QToolButton`, creating a visual inconsistency with the adjacent `QComboBox` used for mode selection.
    *   **Solution:** The stylesheets in `graphite_app.py` and `graphite_styles.py` were updated to give toolbar buttons a transparent background by default. Color is now used exclusively to provide feedback on hover, creating a minimalist and modern appearance. The "Plugins" menu was re-implemented as a `QComboBox` to perfectly match the "Mode" selector, ensuring a visually cohesive and professional toolbar design.

---

### `[Beta v.0.5.4]` - 2025-10-30

This release overhauls the canvas grid system, transforming it from a static background pattern into a dynamic, professional, and fully customizable visual tool. The new grid adapts to the user's zoom level and offers multiple styles for a personalized workspace.

#### Added

*   **Added Comprehensive Grid Customization Options.**
    *   **Feature:** Users can now fully personalize the canvas grid. The `GridControl` widget has been expanded to include new options for changing the grid's visual style (Dots, Lines, or Cross) and its color, in addition to the existing size and opacity controls.
    *   **Implementation:** The `GridControl` widget in `graphite_widgets.py` was updated with new UI buttons and state variables (`grid_style`, `grid_color`). The `drawBackground` method in `ChatView` now reads these properties to render the grid accordingly.

#### Changed

*   **Overhauled Grid Rendering to be Dynamic and Zoom-Aware.**
    *   **Problem:** The previous grid was a simple, static dotted line pattern that did not adapt to the user's zoom level. This resulted in visual clutter when zoomed out and a lack of detail when zoomed in, creating an unprofessional appearance.
    *   **Solution:** The `drawBackground` method in `ChatView` has been completely rewritten. It now renders a multi-level grid with distinct "major" and "minor" lines. These lines intelligently appear and disappear at different zoom thresholds (`LOD_MAJOR`, `LOD_MINOR`), and their thickness scales appropriately. This provides a clean, professional-grade drafting experience that is always visually suitable for the current level of detail.

---

### `[Beta v.0.5.3]` - 2025-10-30

This is a major stability and usability release that restores critical functionality, overhauls key UI components, and refactors core systems for improved modularity. The "Navigation Pins" panel has been completely re-architected into a true floating overlay, and the "Create Frame" feature has been robustly re-implemented.

#### Architectural Refactor

*   **Re-architected the "Navigation Pins" Panel as a True Floating Overlay.**
    *   **Problem:** The `PinOverlay` was previously embedded directly into the main window's layout, permanently consuming screen real estate and creating an intrusive user experience. Its internal collapse logic was flawed and buggy.
    *   **Solution:** The `PinOverlay` has been completely re-architected. It is now a child of the `ChatView`, making it a true floating overlay that sits *on top* of the canvas. Its visibility is now controlled by a dedicated "Pins" button in the main toolbar, providing a global and reliable toggle. All internal collapse/expand logic has been removed in favor of this superior architecture.
*   **Centralized Command Palette Functionality into a Dedicated Module.**
    *   **Problem:** The logic for the command system was split between `graphite_core.py` (for `CommandManager`) and `graphite_dialogs.py` (for `CommandPaletteDialog`), hindering maintainability.
    *   **Solution:** Both classes have been moved into a new, dedicated `graphite_command_palette.py` module. This encapsulation improves modularity and simplifies future development of the command system.

#### Added

*   **Re-enabled "Create Frame" with a New Shortcut (`Ctrl+G`) and Command Palette Integration.**
    *   **Feature:** Users can now once again create frames around selected nodes. This core organizational feature is accessible via the new `Ctrl+G` shortcut and is discoverable in the Command Palette (`Ctrl+K`) as "Create Frame From Selection."
    *   **Implementation:** The feature was re-implemented using a global `QShortcut`, which bypasses any potential event conflicts. The command was also registered with the `CommandManager`, with a corrected condition that makes it available whenever one or more nodes are selected.

#### Changed

*   **Redesigned and Resized the "Navigation Pins" Panel for Professionalism and Usability.**
    *   **Problem:** The panel's visual design was inconsistent, too small to be functional, and lacked a conventional close button.
    *   **Solution:** The panel's styling has been meticulously updated to match the elegant, floating design of the `CommandPalette`. Its default size has been significantly increased, and a dedicated "Close" button has been added to its header. This button correctly interacts with the main toolbar toggle via a signal-and-slot mechanism, ensuring a conventional and intuitive user experience.

#### Fixed

*   **Fixed a series of critical bugs that made the "Navigation Pins" panel unusable.**
    *   **Problem:** The panel's internal collapse button would disappear, making it impossible to re-expand. After being re-architected, the new external close button would then cause a fatal `AttributeError` crash.
    *   **Solution:** The entire flawed collapse mechanism was removed. The crash was resolved by implementing a proper signal-and-slot connection between the panel's "Close" button and the main window, fully decoupling the components and ensuring robust operation.
*   **Fixed a critical regression where the "Create Frame" feature was completely inaccessible.**
    *   **Problem:** A shortcut conflict (`Ctrl+F` being reassigned to "Find") and an incorrect command palette condition made it impossible to create frames.
    *   **Solution:** The conflicting event handler was removed, a new, unambiguous shortcut (`Ctrl+G`) was assigned via `QShortcut`, and the command's availability condition was corrected, fully restoring the feature.
*   **Fixed a visual bug where `CodeNode` connections would not update when moving a parent `Frame`.**
    *   **Problem:** When a `Frame` was moved or resized, the dashed lines connecting `CodeNode`s would remain frozen in their original positions.
    *   **Solution:** The `Frame.mouseMoveEvent` method was updated to correctly iterate through and update all connection types (`connections` and `content_connections`), ensuring all lines are redrawn properly during transformations.
*   **Fixed a fatal `ImportError` crash on startup.**
    *   **Problem:** The refactoring of the Command Palette into its own module was incomplete, as the main application was still trying to import classes from their old locations.
    *   **Solution:** All import statements in `graphite_app.py` were updated to correctly reference the new `graphite_command_palette.py` module, resolving the crash.

### `[Beta v.0.5.1]` - 2025-10-30

This release improves the user's ability to customize their workspace by adding a feature to toggle the visibility of on-canvas controls. It also resolves a critical UI layout bug that affected the application's initial appearance.

#### Added

*   **Added the Ability to Show/Hide On-Canvas View Controls.**
    *   **Feature:** Users can now hide or show the on-canvas widgets for adjusting grid settings and drag sensitivity. This allows for a cleaner, less cluttered workspace when these controls are not needed.
    *   **Implementation:** A new checkable "Controls" button has been added to the main toolbar. This button is connected to a new `toggle_overlays_visibility()` method in the `ChatView` class, which manages the visibility of the `GridControl` and drag sensitivity widgets. The layout logic was also updated to ensure other overlays (like the search bar) correctly reposition themselves when the controls are hidden.

#### Fixed

*   **Fixed a critical UI bug where view control widgets were incorrectly positioned at the top-left on startup.**
    *   **Problem:** On initial application launch, the `GridControl` and drag sensitivity widgets would appear stacked at the `(0,0)` coordinate of the view instead of their correct top-right position. The layout would only correct itself after user interaction.
    *   **Solution:** The root cause was identified as a layout timing issue. The initial positioning logic was being called before the `ChatView` had received its final geometry from the main window. The fix involved overriding the `showEvent` method in `ChatView`. This ensures that the initial positioning of the overlays is now performed only once, at the moment the view is first displayed and guaranteed to have a valid, stable size, resolving the visual bug completely.

### `[Beta v.0.5.0]` - 2025-10-30

This release introduces a centralized "New Chat" command, improving accessibility and standardizing session management. This update also adds dynamic window titles for better user context and completes a robust refactoring of the session clearing logic.

#### Added

*   **Added a Universal "New Chat" Command and Shortcut (Ctrl+T).**
    *   **Feature:** Users can now start a new chat session instantly from anywhere in the application, either by pressing the `Ctrl+T` keyboard shortcut or by searching for "New Chat" in the command palette (`Ctrl+K`).
    *   **Implementation:** The logic for creating a new chat has been centralized into a new `new_chat()` method within the main `ChatWindow` class. This method is now registered with the `CommandManager` and linked to a new `QShortcut`, making it a globally accessible action.

#### Changed

*   **The Main Window Title is now Dynamic and Displays the Current Chat Name.**
    *   **Problem:** The window title was static and did not provide context about the currently loaded chat session.
    *   **Solution:** A new `update_title_bar()` method has been implemented in `ChatWindow`. This method dynamically constructs the window title by combining the application name with the title of the current chat session (if one is loaded) and the license status. This method is now called whenever a new chat is created or an existing one is loaded, providing immediate and clear context to the user.

#### Architectural Refactor

*   **Centralized and Hardened the "New Chat" Session Clearing Logic.**
    *   **Problem:** The original logic for starting a new chat was located within the `ChatLibraryDialog` and was incomplete. It would clear the main `ChatScene` but failed to reset other critical state variables and UI elements, such as the `current_node` reference, message input placeholder, and various lists within the scene (`code_nodes`, `pins`, etc.).
    *   **Solution:** All session clearing logic has been consolidated into the new `ChatWindow.new_chat()` method. This method now performs a comprehensive reset of the application state, including explicitly clearing all item lists within the `ChatScene`, resetting the `current_node` reference in the main window, and updating the UI to its default state, ensuring a clean and reliable transition between sessions.

### `[Beta v.0.4.9]` - 2025-10-29

This release addresses critical bugs in the "Regenerate Response" feature and introduces a new, context-aware loading animation that significantly improves the user experience during AI operations.

#### Added

*   **Added a Contextual, Node-Specific Loading Animation.**
    *   **Feature:** The previous full-screen, text-based loading overlay has been replaced with a visually appealing, animated spinner that appears directly next to the node awaiting a response. This provides clear, non-disruptive feedback, indicating exactly which part of the conversation is being processed.
    *   **Implementation:** A new `LoadingAnimation` class, inheriting from `QGraphicsObject`, was created in `graphite_widgets.py`. This class uses `QPropertyAnimation` to render a smooth, multi-arc spinning animation. The main `ChatWindow` logic was refactored to instantiate this item on the `ChatScene` and position it adjacent to the source node whenever a background AI task (e.g., sending a message, regenerating, creating a chart) is initiated.

#### Fixed

*   **Fixed a critical logic error where "Regenerate Response" would fail due to missing context.**
    *   **Problem:** The regeneration feature was incorrectly providing the model with the conversation history of the node *being replaced*, which lacked the essential preceding user query. This caused the model to have zero context, resulting in irrelevant or nonsensical regenerated responses.
    *   **Solution:** The logic in `ChatWindow.regenerate_node()` has been corrected. It now correctly and robustly fetches the `conversation_history` from the parent node of the node being regenerated. This ensures the model always receives the full and accurate conversational context, including the original query it needs to answer again.
*   **Fixed a fundamental architectural bug where regenerating a response would break its connection to the conversation branch.**
    *   **Problem:** The previous implementation would delete the original node and create a new one, visually and contextually severing it from its place in the conversation graph.
    *   **Solution:** The regeneration logic has been completely re-architected. Instead of deleting and recreating the node, the `handle_regenerated_response` method now updates the existing `ChatNode` in-place. A new `update_content` method was added to the `ChatNode` class to handle the replacement of its text, history, and associated `CodeNode`s, fully preserving its position and connections within the graph.
*   **Fixed a fatal `TypeError` crash caused by an incorrect class inheritance in the new `LoadingAnimation`.**
    *   **Problem:** The `LoadingAnimation` was initially created by inheriting from `QGraphicsItem`, which is not a `QObject` subclass and therefore cannot be animated with `QPropertyAnimation`, causing the application to crash.
    *   **Solution:** The `LoadingAnimation` class was corrected to inherit from `QGraphicsObject`, which is the proper Qt class for graphics items that require support for signals, slots, and properties, resolving the crash.
*   **Fixed a `NameError` crash in `graphite_widgets.py` due to a missing import.**
    *   **Problem:** The `LoadingAnimation` class used `QRectF` without importing it, causing a crash whenever it was instantiated.
    *   **Solution:** The missing `QRectF` class has been added to the import statement from `PySide6.QtCore`, resolving the error.

### `[Beta v.0.4.8]` - 2025-10-29

This release introduces a major new feature for handling and displaying code, significantly improving the user experience for developers and technical users. AI-generated responses containing code blocks are now automatically parsed and rendered in a dedicated, syntax-highlighted `CodeNode`, visually separating them from conversational text.

#### Added

*   **Added Automatic Code Block Detection and Syntax Highlighting.**
    *   **Feature:** The application can now automatically detect markdown-style code blocks (e.g., ` ```python...``` `) in AI responses. Each detected block is rendered in a new, specialized `CodeNode` with full syntax highlighting, powered by the `pygments` library. This makes code significantly easier to read, understand, and copy.
    *   **Implementation:**
        *   A new `CodeNode` class was created in `graphite_node.py`, which uses a `QTextDocument` to render HTML generated by `pygments`, providing rich, styled text.
        *   The `handle_response` method in `graphite_app.py` was updated with a regex-based parser to intelligently split incoming AI responses into separate text and code segments.
        *   A new `ContentConnectionItem` was added to `graphite_items.py` to create a distinct visual link (a dashed line) between a parent `ChatNode` and its child `CodeNode`, clarifying their relationship.
        *   The `ChatSessionManager` was enhanced to serialize and deserialize the new `CodeNode`s and their connections, ensuring they are saved and loaded correctly with the chat graph.

#### Architectural Refactor

*   **Integrated `pygments` for Robust Syntax Highlighting.**
    *   **Problem:** Displaying code as plain text is difficult to read and unprofessional. A robust, extensible solution for code rendering was needed.
    *   **Solution:** The `pygments` library has been integrated as a core dependency. A new `CodeHighlighter` utility class was added to `graphite_node.py` to abstract the highlighting logic. This class dynamically generates CSS and HTML from Python code, supporting a wide range of languages and providing a high-quality, professional rendering experience within the new `CodeNode`. The system is designed to fall back gracefully to plain text if a language is not specified or recognized.

### `[Beta v.0.4.7]` - 2025-10-29

This release introduces the Command Palette, a major new usability feature that provides fast, keyboard-driven access to nearly all application functions. This update also includes a key architectural refactoring of item deletion logic, centralizing it to support the new command system and improve maintainability.

#### Added

*   **Added the Command Palette for Quick, Keyboard-Driven Actions (Ctrl+K).**
    *   **Feature:** Users can now press `Ctrl+K` to open a searchable command palette, similar to those found in modern code editors. This provides instant, text-based access to a wide range of actions, such as "Collapse All Nodes," "Delete Selected Items," "Generate Bar Chart," and "Fit All to View," dramatically speeding up user workflow.
    *   **Implementation:** The feature is powered by a new `CommandManager` class (`graphite_core.py`) responsible for registering commands, their aliases, and context-sensitive availability (e.g., "Delete Selected" is only available when items are selected). A new `CommandPaletteDialog` (`graphite_dialogs.py`) provides the search UI, and the main `ChatWindow` now registers all core application functions as commands.

#### Architectural Refactor

*   **Refactored and Centralized Item Deletion Logic.**
    *   **Problem:** Previously, the logic for deleting different item types was fragmented across the codebase. The complex logic for deleting a `ChatNode` (which involves re-parenting its children) was located exclusively within its context menu, making it non-reusable for a global "Delete Selected" command.
    *   **Solution:** All deletion logic has been centralized within the `ChatScene` class. The `ChatNode` deletion algorithm was moved into a new `delete_chat_node` method. The primary `deleteSelectedItems` method was enhanced to iterate through all selected items and call the correct deletion function for each type (`ChatNode`, `Frame`, `Note`, etc.). This refactoring was essential for implementing the unified "Delete Selected Items" command in the new Command Palette.

### `[Beta v.0.4.6]` - 2025-10-29

This release introduces a powerful new usability feature for focusing on specific conversation paths and completes a significant architectural refactoring to improve modularity and resolve critical import errors.

#### Added

*   **Added "Hide Other Branches" Feature to Focus on Specific Conversations.**
    *   **Feature:** Users can now right-click any node and select "Hide Other Branches" from the context menu. This action visually dims and de-emphasizes all nodes that are not part of the selected node's direct lineage (i.e., its ancestors and descendants). This allows users to instantly focus on a single, coherent line of thought within a complex, branching graph.
    *   **Implementation:** The feature is managed by a new state variable and a `toggle_branch_visibility()` method within the `ChatScene` class. When activated, the method traces the full path of the selected branch and sets an `is_dimmed` flag on all other nodes. The rendering logic in `ChatNode` was updated to draw a semi-transparent overlay when this flag is active. The context menu dynamically updates to "Show All Branches," allowing the user to easily restore the view.

#### Architectural Refactor

*   **Decomposed the monolithic `graphite_items.py` module for improved modularity.**
    *   **Problem:** The `graphite_items.py` file was responsible for both general-purpose canvas items (like `Frame`, `Note`, `Pin`) and the highly complex, multi-component `ChatNode`. This violated the Single Responsibility Principle and made the codebase harder to navigate and maintain.
    *   **Solution:** All classes directly related to the chat node functionality have been extracted into a new, dedicated `graphite_node.py` file. This includes `ChatNode`, `TextBlock`, `ImageBlock`, `ScrollBar`, and `ChatNodeContextMenu`. This refactoring clarifies the separation of concerns, with `graphite_items.py` now serving as a library for generic canvas elements and `graphite_node.py` encapsulating all chat node logic.

#### Fixed

*   **Fixed a fatal application crash (`ImportError`) caused by a circular dependency.**
    *   **Problem:** The architectural refactoring inadvertently created a circular import loop where `graphite_node.py` imported `graphite_items.py`, which in turn imported `graphite_node.py`, causing the application to crash on startup.
    *   **Solution:** The unnecessary import of `ChatNode` from `graphite_items.py` was identified and removed. The classes within `graphite_items.py` do not need compile-time knowledge of `ChatNode`, as they operate on instances passed at runtime. Removing this import successfully broke the dependency cycle and restored application stability.

### `[Beta v.0.4.5]` - 2025-10-29

This release addresses several significant visual and architectural flaws within the `ChatNode` component that were introduced in the previous version. The primary focus was on creating a robust and permanent solution to the layout conflicts between the text content, the scrollbar, and the collapse/expand button, ensuring a clean and professional UI under all conditions.

#### Fixed

*   **Fixed a critical UI rendering bug where the collapse/expand button would overlap the text content.**
    *   **Problem:** Previous attempts to fix the layout resulted in a state where, if a node's content was *not* long enough to require a scrollbar, the collapse button would be drawn directly on top of the text field.
    *   **Solution:** The rendering and layout architecture of the `ChatNode` has been fundamentally corrected. A permanent, reserved space ("gutter") is now established on the right side of every node. The text layout logic was updated to *never* enter this gutter, guaranteeing that its content can never overlap with UI controls. The button's positioning logic was then simplified to reliably place it within this safe, reserved area, resolving the conflict in all states.
*   **Fixed a major regression where the scrollbar would overlap the collapse button.**
    *   **Problem:** An incorrect fix for the text overlap issue reintroduced the original bug where the scrollbar and collapse button would render on top of each other, making both components difficult to use.
    *   **Solution:** The `paint` method was corrected to implement a strict, state-dependent positioning rule. It now explicitly checks if the scrollbar is visible. If it is, the button is placed to the left of the scrollbar; otherwise, it is placed at the far-right edge of the node. This dual-state logic ensures the two components can never occupy the same space.
*   **Fixed a fatal application crash (`QPainter::end: Painter ended with 2 saved states`) and disappearing nodes.**
    *   **Problem:** An incorrect attempt to fix the rendering order led to a critical painter state imbalance, where `painter.save()` was called without a corresponding `painter.restore()`. This corrupted the Qt rendering pipeline, causing the application to crash and `ChatNode` items to fail to render entirely.
    *   **Solution:** The `paint` method has been corrected to use a properly balanced `painter.save()`/`painter.restore()` block. This block now safely encapsulates the clipping and drawing of the text content, ensuring the painter state is always correctly managed and preventing any rendering crashes or visual corruption.

### `[Beta v.0.4.4]` - 2025-10-28

This release enhances canvas usability by introducing a node collapse feature, allowing users to manage complex conversation graphs more effectively. It also includes fixes for visual artifacts that appeared as a result of the new functionality.

#### Added

*   **Added Node Collapse/Expand Functionality.**
    *   **Feature:** Users can now minimize nodes to a compact state, reducing visual clutter and making it easier to navigate large, branching conversations. This is a crucial tool for focusing on specific thought threads while hiding less relevant ones.
    *   **Implementation:** The `ChatNode` class now manages a `is_collapsed` state. The feature is accessible via two intuitive methods: a new "Collapse/Expand" option in the right-click context menu, and a hover-activated icon that appears in the top-right corner of the node for quick, direct interaction. When a node's state is changed, all associated connections and parent frames automatically adjust to the new geometry.
*   **Added Persistence for Node Collapse State.**
    *   **Feature:** The collapsed or expanded state of every node is now saved with the chat session and is fully restored when the session is loaded.
    *   **Implementation:** The `ChatSessionManager` has been updated to serialize the `is_collapsed` boolean for each node into the database and deserialize it upon loading, ensuring a consistent and predictable layout across sessions.

#### Fixed

*   **Fixed a critical UI bug where the scrollbar remained visible on collapsed nodes.**
    *   **Problem:** When a node with scrollable content was collapsed, its scrollbar was not being hidden, leaving a detached and visually disruptive UI element on the canvas.
    *   **Solution:** The logic in the `ChatNode._update_geometry_for_state()` method has been corrected to explicitly set the `ScrollBar`'s visibility to `False` whenever the node enters a collapsed state, completely resolving the visual artifact.

### `[Beta v.0.4.3]` - 2025-10-28

This is a minor release focused on improving the core visual aesthetic of the application's chat canvas, moving from a flat design to one with more perceived depth and professionalism.

#### Changed

*   **Enhanced the visual design of `ChatNode` items for a more professional aesthetic.**
    *   **Problem:** The previous design used solid, flat colors (green and blue) for the node backgrounds. While functional, this created a visually simplistic and less polished look.
    *   **Solution:** The node's rendering logic in the `ChatNode.paint()` method has been updated. The solid color fill has been replaced with a subtle gray gradient, giving the nodes a sense of depth. The node's origin (user or AI) is now indicated by a colored outline (green for user, blue for AI) rather than the entire background, resulting in a cleaner and more professional appearance.

### `[Beta v.0.4.2]` - 2025-10-27

This release completes the major UI overhaul by standardizing all window frames across the application, resulting in a more professional and cohesive user experience. The focus was on eliminating custom, non-native windowing solutions in favor of robust, OS-level implementations.

#### Changed

*   **Unified the Main Window and all Dialogs to use Native OS Window Frames.**
    *   **Problem:** The main application window and several key dialogs (`Library`, `Help`, `About`, `Activation`) were using a custom, frameless implementation that required significant boilerplate code to replicate basic window functions. This created a visual and functional disconnect from standard dialogs and lacked the robustness of native window management.
    *   **Solution:** All custom frameless window implementations have been removed and replaced with standard, native window frames. The `CustomTitleBar` class is now obsolete, and all windows now leverage the host operating system's window manager for a consistent look, feel, and behavior (including features like window snapping and native shadows).

*   **Improved User Help Dialog Content.**
    *   **Problem:** The descriptions in the "Help" dialog were overly brief and often failed to explain *how* or *why* a feature should be used, limiting their value for new users.
    *   **Solution:** All help text has been rewritten to be more detailed, descriptive, and action-oriented. The new content provides clear, step-by-step instructions, explains core application concepts (like "Contextual Replies"), and includes keyboard shortcuts to improve feature discovery and user onboarding.

#### Fixed

*   **Fixed a critical bug where the native close ("x") button was disabled on all refactored windows.**
    *   **Details:** The process of removing the custom frameless hints inadvertently dropped the `WindowCloseButtonHint` flag, causing the window manager to disable the close button. This flag has been explicitly re-added to the main window and all refactored dialogs, restoring essential functionality.
*   **Fixed an architectural flaw by relocating the main window icon logic.**
    *   **Details:** The application's primary icon was being incorrectly set from within the now-obsolete `CustomTitleBar` class. This logic has been moved to the main `ChatWindow`'s `__init__` method, where it properly belongs.

### `[Beta v.0.4.1]` - 2025-10-27

This release is a continuation of the major architectural refactoring initiated in the previous version. The focus was to dismantle the large `graphite_graphics.py` file, further improving modularity and clarifying the separation between UI components.

#### Architectural Refactor

*   **Decomposed the monolithic `graphite_graphics.py` module.**
    *   **Problem:** Following the initial UI refactor, the `graphite_graphics.py` module remained overly large, containing both the individual graphical items (`ChatNode`, `Frame`, `Note`) and the container classes that manage them (`ChatScene`, `ChatView`). This co-location of distinct responsibilities hindered clarity and maintainability.
    *   **Solution:** The single file has been broken down into two new, more focused modules, establishing a clearer hierarchy between the view and its contents:
        *   `graphite_items.py`: This module now exclusively contains all `QGraphicsItem` subclasses. These classes represent the individual, interactive elements on the canvas (`ChatNode`, `ConnectionItem`, `Frame`, `Note`, `ChartItem`, `NavigationPin`, etc.).
        *   `graphite_view.py`: This module houses the container classes (`ChatScene` and `ChatView`), which are responsible for managing, displaying, and orchestrating interactions with the graphical items.

#### Fixed

*   **Fixed a critical `ImportError` crash caused by a circular dependency.**
    *   **Details:** The refactoring process inadvertently created a circular import loop: `graphite_widgets` imported `graphite_items`, which imported `graphite_dialogs`, which in turn imported `graphite_widgets`, causing the application to crash on startup. The issue was resolved by restoring a "dummy class" forward declaration for `NavigationPin` within `graphite_widgets.py`, successfully breaking the import cycle.

### `[Beta v.0.4.0]` - 2025-10-27

This release introduces a major architectural refactoring of the user interface layer. The primary goal was to dismantle the monolithic `graphite_ui.py` file to improve modularity, resolve circular dependency issues, and enhance long-term maintainability.

#### Architectural Refactor

*   **Decomposed the monolithic UI module.**
    *   **Problem:** The `graphite_ui.py` file had grown to include over 25 distinct classes, mixing responsibilities such as styling, core widgets, dialogs, and complex graphics scene items. This made the code difficult to navigate, debug, and maintain.
    *   **Solution:** The single file has been broken down into four new, single-responsibility modules:
        *   `graphite_styles.py`: Centralizes the `StyleSheet` class and color constants.
        *   `graphite_widgets.py`: Contains reusable `QWidget` subclasses like `CustomTitleBar`, `LoadingOverlay`, and `PinOverlay`.
        *   `graphite_dialogs.py`: Consolidates all `QDialog`-based classes.
        *   `graphite_graphics.py`: Contains all core `QGraphics...` components, including custom items, the `ChatScene`, and the `ChatView`.

#### Fixed

*   **Fixed multiple critical `ImportError` crashes** that arose during the refactoring process.
    *   **Details:** Resolved errors caused by incorrect import paths for Qt classes. `QAction` was moved from `QtWidgets` to its correct module, `QtGui`. The class `QGraphicsPathStroker` was corrected to its proper name, `QPainterPathStroker`, fixing both the import and its usage within the `ConnectionItem` class.
*   **Fixed a `TypeError` crash related to the Frame animation.**
    *   **Details:** The `valueChanged` signal of the `QVariantAnimation` used for the frame's selection outline was incorrectly connected to the `QGraphicsItem.update` method, passing an unexpected argument. This was resolved by using a `lambda` function to ensure the method is called without arguments, stopping the crash loop.

### `[Beta v.0.3.0]` - 2025-10-27

This release transitions the application from a purely functional prototype to a distributable product by introducing a robust licensing and user agreement framework. The primary focus is on ensuring a professional first-run experience, protecting the software with a trial period, and providing clear developer attribution.

#### Added

*   **Added a 5-Day Trial and Licensing System.**
    *   **Feature:** The application now operates in a fully-featured demo mode for 5 days from the first launch. After the trial period expires, the user must enter a valid license key to continue using the software.
    *   **Implementation:** A new `graphite_licensing.py` module has been created to manage all licensing logic. It creates a persistent state file (`~/.graphite/session.dat`) to securely track the first launch date, EULA acceptance, and activation status. The main application startup sequence in `graphite_app.py` has been modified to enforce these checks before the main window is launched.
*   **Added a Mandatory End-User License Agreement (EULA).**
    *   **Feature:** On the very first launch, users are now presented with a modal EULA dialog. The application will not run unless the user scrolls to the bottom and explicitly agrees to the terms, which include a disclaimer of warranty and a liability clause for generated content.
    *   **Implementation:** A new `EULADialog` was created in `graphite_ui.py` with logic to ensure full review of the text. The `LicenseManager` tracks the acceptance state, preventing the app from running until the EULA is accepted.
*   **Added an "About" Dialog and Developer Credits.**
    *   **Feature:** A new "About" dialog is now accessible from the main toolbar, providing users with application version info, developer credits (name, contact), and a direct link to the project's GitHub repository. It also clearly displays the current license status (Trial, Licensed, or Expired).
    *   **Implementation:** An `AboutDialog` class was added to `graphite_ui.py` and integrated into the `ChatWindow` toolbar.
*   **Added an In-App Activation Dialog.**
    *   **Feature:** Users can now activate their software from within the application using a dedicated "Activate" dialog, which appears automatically when the trial expires or can be accessed from the toolbar.
    *   **Implementation:** The `ActivationDialog` class in `graphite_ui.py` provides an interface for key entry and validation against the logic in `graphite_licensing.py`.

#### Changed

*   **The Main Window Title is now dynamic and reflects the license status.** The title bar will display "Graphite (Trial - X days remaining)" during the trial period and "Graphite (Licensed)" after successful activation, providing clear and persistent feedback to the user.
*   **The main toolbar has been updated** to include buttons for the new "About" and "Activate" dialogs, making these features easily accessible.

#### Fixed

*   **Fixed a bug in the EULA dialog where the "Agree" button would remain disabled** if the agreement text was too short to require scrolling. The logic was corrected to enable the button immediately if the content is fully visible upon opening, ensuring a smooth user experience regardless of screen resolution or content length.

### `[Beta v.0.2.2]` - 2024-05-25

This release introduces support for any OpenAI-compatible API endpoint as an alternative to a local Ollama instance. It also includes a significant user experience overhaul for model and provider selection, resolving critical design flaws and improving usability.

#### Added

*   **Added support for OpenAI-Compatible API Endpoints.**
    *   **Feature:** Users can now switch from using a local Ollama instance to any remote API service that is compatible with the OpenAI API specification (e.g., OpenAI, Groq, OpenRouter, or self-hosted solutions like LiteLLM). (Credits for first conceptual buildout/iteration of implimentations - https://github.com/1818TusculumSt)
    *   **Implementation:** A new `api_provider.py` module was created to act as a router, abstracting all LLM calls and directing them to either Ollama or the configured API endpoint based on the user's selection. All agent classes (`ChatAgent`, `KeyTakeawayAgent`, etc.) were refactored to use this new provider instead of calling `ollama` directly.
*   **Added Per-Task Model Configuration for API Mode.**
    *   **Feature:** When using an API provider, users can configure different models for different tasks (Title Generation, Chat/Analysis, and Chart Data Extraction) to optimize for cost, speed, and capability.
    *   **Implementation:** An `APISettingsDialog` was created, allowing users to enter their API credentials, load a list of available models from the endpoint, and assign a specific model to each task category.

#### Changed

*   **Overhauled the Model and Provider Selection UX.**
    *   **Problem:** The previous UI design for model selection was confusing, with separate and sometimes hidden buttons for different providers. This created a frustrating and non-discoverable user experience.
    *   **Solution:** The toolbar has been redesigned with a clear, two-part system:
        1.  A "Mode" dropdown to explicitly select the provider (`Ollama (Local)` or `API Endpoint`).
        2.  A single, consistently visible "Settings" button. This button is now context-aware and will open the appropriate configuration dialog (`ModelSelectionDialog` for Ollama or `APISettingsDialog` for an API) based on the currently selected mode. This resolves all ambiguity and makes the feature intuitive to use.

#### Fixed

*   **Fixed a critical UI bug where the API settings button was permanently invisible.**
    *   **Problem:** A logic error in the toolbar setup (`self.api_settings_btn.setVisible(False)`) caused the button to configure the API to be hidden, making the entire feature inaccessible.
    *   **Solution:** The erroneous line was removed, and the toolbar was refactored to use the new unified "Settings" button, ensuring the correct dialog is always accessible.
*   **Fixed an architectural violation where UI components were defined outside of `graphite_ui.py`.**
    *   **Problem:** The `APISettingsDialog` was incorrectly defined within the main application file (`graphite_app.py`), breaking the project's modular structure.
    *   **Solution:** The `APISettingsDialog` class has been moved to its proper location within `graphite_ui.py`, restoring the architectural integrity and separation of concerns.

### `[Beta v.0.2.1]` - 2024-05-25

This release focuses on critical stability and functionality fixes, primarily addressing issues within the API provider integration and the core UI rendering system.

#### Fixed

*   **Fixed a critical application crash when loading models from the Google Gemini API.**
    *   **Problem:** The application would crash with a `TypeError` when fetching the model list from Gemini. A deep-dive analysis revealed this was due to a fundamental incompatibility between the installed `google-generativeai` library and the current data structure of the API's response. The previous design, which relied on dynamic model discovery, proved to be too brittle for this provider.
    *   **Solution:** The architecture for the Gemini provider has been completely refactored. The brittle dynamic discovery process has been removed and replaced with a static, hard-coded list of known-stable Gemini models. The API Settings UI now intelligently adapts, hiding non-functional controls (e.g., "Load Models") for the Gemini provider and populating the model list instantly and reliably.

*   **Fixed a critical logic error preventing chat messages from being sent to Gemini models.**
    *   **Problem:** After successfully configuring a Gemini model, all chat attempts would fail with an "Error: message cannot be blank."
    *   **Solution:** The root cause was identified in the `api_provider.chat()` function, where a `pop()` operation was incorrectly removing the user's current message from the conversation history before the payload was sent to the API. This line has been removed, ensuring the complete and correct conversation context is now sent with every request.

*   **Fixed a fatal application crash on startup due to incorrect UI class definition order.**
    *   **Problem:** A regression from a previous refactor caused the application to crash on launch with an `ImportError` and underlying `NameError` exceptions. Classes within `graphite_ui.py` (e.g., `ColorPickerDialog`, `ConnectionItem`) were being referenced before they were defined, halting the module loading process.
    *   **Solution:** The class definitions within `graphite_ui.py` have been meticulously reordered to ensure a correct, top-to-bottom dependency resolution. All classes are now defined before they are instantiated or referenced, resolving the startup crash.

*   **Fixed a fatal UI rendering crash related to the `ChartItem` class.**
    *   **Problem:** The application would enter a crash loop during the UI paint event, raising an `AttributeError` for a non-existent `QPainter.RenderHint` (`HighQualityAntialiasing`).
    *   **Solution:** The erroneous line has been removed from the `ChartItem.paint()` method. Rendering quality is preserved by other existing and correct render hints (`SmoothPixmapTransform`), and the crash is fully resolved.

#### Changed

*   **Updated the static model list for the Google Gemini provider.**
    *   The hard-coded list of Gemini models was updated to include the latest stable releases available through the public API, including the `gemini-2.5-pro` and `gemini-2.5-flash` series, ensuring users have access to current and powerful models.

### `[Beta v.0.2.0]` - 2024-05-23

#### Architectural Refactor

*   This update introduces a major architectural refactoring to improve the project's structure, maintainability, and scalability. The application has been transitioned from a single, monolithic script into a modular, multi-file structure with a clear separation of concerns. The primary goal of this refactor was to decouple the User Interface, Core Logic, and AI Agent services from one another.

*   The new project structure is as follows:
    *   `graphite_app.py`: Serves as the main application entry point. It contains the primary `ChatWindow` class and is responsible for initializing and launching the application.
    *   `graphite_ui.py`: Consolidates all classes related to the User Interface layer. This includes all Qt widgets, dialogs, custom graphics items for the scene (`ChatNode`, `ConnectionItem`, `Frame`, etc.), and view components (`ChatView`, `ChatScene`).
    *   `graphite_core.py`: Manages the application's core logic and data persistence. It contains the `ChatDatabase` class for all SQLite operations and the `ChatSessionManager` for handling the serialization and deserialization of chat sessions.
    *   `graphite_agents.py`: Isolates all logic related to AI model interaction. This module contains the base `ChatAgent` as well as specialized tool agents (`KeyTakeawayAgent`, `ExplainerAgent`, `ChartDataAgent`) and their corresponding `QThread` workers for asynchronous processing.
    *   `graphite_config.py`: A centralized location for application-wide configuration constants, such as task identifiers and default model names.
    *   `api_provider.py`: A dedicated module to abstract away the differences between various AI providers (Ollama, OpenAI-compatible, Google Gemini), presenting a unified interface to the rest of the application.
