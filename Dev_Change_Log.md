# Changelog

All notable changes to this project will be documented in this file.

---

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


---


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


---

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

---

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

---

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

---

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

---

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

---

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
