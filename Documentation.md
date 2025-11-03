
# Graphite Application: System & Feature Documentation

## 1. Introduction

Graphite is a node-based productivity and ideation environment designed for visual thinking and structured AI interaction. The core principle of the application is the **Canvas**, an infinite workspace where conversations, code, documents, and annotations are represented as interconnected nodes. This architecture allows users to visually trace the flow of information, explore multiple lines of thought simultaneously, and organize complex projects with clarity and precision.

This document provides a comprehensive and rigorous overview of the application's features, architecture, and capabilities.

---

## 2. Core Concepts

The Graphite ecosystem is built upon three fundamental components: The Canvas, Nodes, and Connections.

### 2.1 The Canvas
The Canvas is the primary workspace, representing an infinite 2D plane. It is managed by two distinct layers:
*   **`ChatScene`**: The logical container for all graphical items. It manages the data model, relationships between items, and back-end functionalities like search and layout algorithms.
*   **`ChatView`**: The interactive viewport through which the user observes and interacts with the scene. It handles user input for navigation (panning, zooming) and rendering of the scene content.

### 2.2 Nodes & Items
Nodes are the primary objects on the canvas, each serving a specific function. They are the containers for all information within the system.

### 2.3 Connections
Connections are visual lines that represent the relationships and data flow between nodes. They are fundamental to understanding the structure and history of a conversation.

---

## 3. The Graphite Canvas (View & Scene)

The canvas is the central hub of user activity, equipped with a suite of tools for navigation, organization, and visualization.

### 3.1 Canvas Navigation

*   **Panning:** Hold the **Middle Mouse Button** and drag to move the view across the canvas. The panning sensitivity can be adjusted via the "Controls" overlay.
*   **Zooming:** Hold **Ctrl** and use the **Mouse Wheel** to zoom in and out. The zoom is centered on the cursor's position.
*   **Zoom to Selection (Box Zoom):** Hold **Shift**, then click and drag to draw a selection rectangle. Upon releasing the mouse, the view will automatically zoom to fit the contents of this rectangle.

### 3.2 Item Interaction

*   **Single Selection:** **Left-Click** on any node or item to select it.
*   **Multi-Selection (Rubber Band):** **Left-Click** on an empty area of the canvas and drag to create a selection box. All items within the box will be selected upon release.
*   **Contextual Response:** Clicking on a `ChatNode` or `PyCoderNode` sets it as the "current context." The next message sent by the user will create a new node that branches directly from this selected node.

### 3.3 Canvas Overlays & Controls

These are floating widgets that provide direct control over the canvas environment. They can be toggled via the "Controls" button in the main toolbar.

*   **Grid Control:**
    *   **Opacity:** A slider to control the visibility of the background grid.
    *   **Grid Size:** Buttons to set the grid spacing to `10px`, `20px`, `50px`, or `100px`.
    *   **Grid Style:** Toggles the grid pattern between `Dots`, `Lines`, and `Crosses`.
    *   **Grid Color:** Presets to change the grid color for better contrast or aesthetic preference.
    *   **Snap to Grid:** A checkbox that forces nodes and other items to align with the grid when moved.
    *   **Orthogonal Connections:** A checkbox that forces connection lines (without pins) to route using only right angles.
    *   **Smart Guides:** A checkbox that enables dynamic alignment lines to appear when moving an item, allowing for precise alignment with other items on the canvas.

*   **Font Control:**
    *   **Font Family:** A dropdown to change the default font for text content within `ChatNode` and `DocumentNode` items.
    *   **Font Size:** A slider to adjust the default font size.
    *   **Font Color:** A set of color presets to change the default text color.

*   **Pin Overlay:**
    *   A dedicated panel for managing `Navigation Pins`. It lists all created pins, allowing for quick navigation by clicking a pin's name. It also provides functionality to add new pins or delete existing ones.

*   **Minimap:**
    *   A vertical timeline of all `ChatNode` items in the order they were created. It provides a high-level overview of the entire conversation and allows for quick navigation by clicking on a node indicator. The minimap supports scrolling via the mouse wheel.

---

## 4. Node & Item Reference

This section details every type of object that can exist on the canvas.

### 4.1 `ChatNode`
The fundamental unit of conversation.
*   **Visuals:** Rendered as a rounded rectangle with a colored outline.
    *   **User Node:** Green outline.
    *   **AI Node:** Blue outline.
*   **Features:**
    *   **Content Rendering:** Supports full Markdown, including headers, lists, blockquotes, and tables.
    *   **Collapsing:** Nodes can be collapsed to their title to save space, and expanded to view full content.
    *   **Content Scrolling:** If a node's content exceeds its maximum height, a custom scrollbar appears.
*   **Context Menu Actions:**
    *   **Copy Text:** Copies the node's raw text content to the clipboard.
    *   **Collapse/Expand Node:** Toggles the node's collapsed state.
    *   **Export to Doc:** Opens a submenu to export the node's content to various formats (see Section 7.3).
    *   **Hide/Show Other Branches:** Dims all nodes that are not direct ancestors or descendants, focusing the view on a single conversational thread.
    *   **Generate Key Takeaway:** (AI Action) Creates a `Note` with a concise summary.
    *   **Generate Explainer Note:** (AI Action) Creates a `Note` with a simplified explanation.
    *   **Generate Chart:** (AI Action) Opens a submenu to create various chart visualizations from data in the node.
    *   **Generate Image:** (AI Action) Uses the node's text as a prompt to generate an image.
    *   **Delete Node:** Removes the node and intelligently re-parents its children.
    *   **Regenerate Response:** (AI Nodes only) Re-runs the query to generate a new response in the same node.

### 4.2 `CodeNode`
A specialized container for formatted code.
*   **Visuals:** A dark-themed block with a header indicating the language.
*   **Features:**
    *   **Syntax Highlighting:** Utilizes the Pygments library for rich, multi-language syntax highlighting. It will attempt to auto-detect the language if not specified.
    *   **Copy Button:** A one-click button in the header to copy the entire code block to the clipboard.
*   **Context Menu Actions:**
    *   **Copy Code:** Copies the code to the clipboard.
    *   **Export to Doc:** Submenu to export the code to various formats, including a `.py` script.
    *   **Regenerate Response:** Triggers regeneration on the parent `ChatNode`.
    *   **Delete Code Block:** Removes the node.

### 4.3 `DocumentNode` & `ImageNode`
These nodes are automatically created when a document or image file is attached to a message.
*   **`DocumentNode`:** Displays the title and scrollable text content of supported documents (`.txt`, `.md`, `.py`, `.pdf`, `.docx`, etc.).
*   **`ImageNode`:** Displays the attached image, scaled to fit within the node.

### 4.4 `Note`
A flexible annotation tool.
*   **Visuals:** A "sticky note" with a colored header.
*   **Features:**
    *   **Text Editing:** Supports in-place text editing with rich text features (selection, copy, paste, etc.).
    *   **Resizable:** Can be resized by dragging the handle in the bottom-right corner.
    *   **System Prompt:** A special variant created by the "System Prompt" plugin, which overrides the AI's default behavior for an entire conversation branch.

### 4.5 `Frame`
A container for grouping and organizing nodes.
*   **Features:**
    *   **Grouping:** Created by selecting nodes and pressing `Ctrl+G`.
    *   **Title Editing:** The title ("note") can be edited by double-clicking the header.
    *   **Color Customization:** The frame's body and header colors can be changed via a color picker accessible from the header.
    *   **Locking Mechanism:** Frames can be "locked," which makes all contained nodes non-movable and non-selectable. When locked, moving the frame moves all nodes within it as a single unit. When unlocked, nodes can be moved freely within the frame's boundaries.

### 4.6 `Container`
A more advanced grouping item that fully encapsulates its contents.
*   **Features:**
    *   **Encapsulation:** Unlike Frames, items placed in a Container become its children. Moving the container moves all contents.
    *   **Collapsing:** Containers can be collapsed into a single, compact node, hiding all internal items. This is useful for abstracting complex sections of the graph.
    *   **Visual State:** The expanded state shows all items, while the collapsed state shows a pulsing node with the container's title.

### 4.7 `NavigationPin`
A bookmark for a specific location on the canvas.
*   **Creation:** Can be created via the Pin Overlay or by selecting one and using its context menu.
*   **Functionality:** Allows users to name and annotate specific coordinates on the canvas. Clicking a pin in the Pin Overlay instantly centers the view on its location.

### 4.8 `ChartItem`
A node that visualizes data.
*   **Creation:** Generated via the "Generate Chart" context menu action on a `ChatNode`.
*   **Features:** Supports `Bar`, `Line`, `Pie`, `Histogram`, and `Sankey` charts, rendered using Matplotlib. The chart is resizable.

---

## 5. Core Features & Workflows

### 5.1 AI & Generation

*   **Primary Chat Interaction:** The core loop of sending a message and receiving an AI response in a new node.
*   **Regeneration:** AI nodes can be regenerated to get a different response to the same prompt.
*   **Key Takeaways & Explainers:** On-demand AI analysis to summarize or simplify the content of any `ChatNode`.
*   **Chart Generation:** An AI agent analyzes the text in a `ChatNode` to extract data and generate a chart visualization.
*   **Image Generation:** An AI agent uses the text of a `ChatNode` as a prompt to generate an image.

### 5.2 Organization & Layout

*   **Frames & Containers:** Manual grouping tools for semantic organization (see Section 4).
*   **Automatic Node Organization:** A one-click "Organize" feature that rearranges all nodes on the canvas into a hierarchical tree layout, providing instant clarity to complex graphs.

### 5.3 Session Management

*   **Database Backend:** All chat sessions are stored locally in a SQLite database (`.graphite/chats.db`).
*   **Chat Library (`Ctrl+L`):** A dialog for managing all saved chat sessions. It allows users to:
    *   Load a previous session.
    *   Rename a session.
    *   Delete a session.
    *   Start a new session.
*   **Auto-Saving:** The current session state is automatically saved after each AI response. Manual saving is available via `Ctrl+S` or the "Save" button.
*   **Automatic Title Generation:** When a new chat is saved for the first time, an AI agent generates a concise 2-3 word title based on the initial message.

### 5.4 Search & Execution

*   **Search Overlay (`Ctrl+F`):** A floating search bar to find text within any node on the canvas. Supports "Find Next" (`Enter`) and "Find Previous" (`Shift+Enter`).
*   **Command Palette (`Ctrl+K`):** A powerful, searchable command menu that provides quick access to nearly all application functions, from creating notes to generating charts and managing the view.

---

## 6. Plugins

Graphite features a plugin system for extending its core functionality.

### 6.1 System Prompt Plugin
*   **Purpose:** To override the default AI system prompt for a specific conversation branch.
*   **Workflow:**
    1.  Select a node within the desired branch.
    2.  Execute the "System Prompt" plugin from the toolbar.
    3.  A special `Note` item with a pulsing purple connection is created and linked to the root of the branch.
    4.  Any text written in this note becomes the new system prompt for all subsequent AI interactions within that branch.

### 6.2 Py-Coder Plugin
A powerful, integrated coding environment for Python execution and analysis.
*   **Modes of Operation:**
    *   **AI-Driven Mode:** The user provides a natural language prompt. The AI analyzes the prompt, generates Python code, executes it, and provides an analysis of the results. It includes a self-repair mechanism to fix code that produces errors.
    *   **Manual Mode:** The user can write and execute their own Python code directly. The AI can then be triggered to analyze the output.
*   **User Interface:**
    *   **Status Tracker:** A checklist that shows the real-time progress of the AI-Driven workflow (Analyze, Generate, Execute, Analyze Result).
    *   **Prompt/Code Input:** A text area for either the user's prompt or manual code.
    *   **Generated Code Display:** Shows the code generated by the AI.
    *   **Execution Output:** Displays the `stdout` and `stderr` from the code execution.
    *   **AI Analysis:** A final text block where the AI explains the code and its output.

---

## 7. Data & Integration

### 7.1 Model Provider Abstraction
Graphite is designed to be backend-agnostic, supporting multiple Large Language Model (LLM) providers through a unified `api_provider` module.
*   **Ollama:** For running local models.
*   **OpenAI-Compatible:** For connecting to any API endpoint that follows the OpenAI specification (e.g., OpenAI, LiteLLM, OpenRouter, Anthropic).
*   **Google Gemini:** Native support for Google's Gemini family of models.
*   **Axiom:** A pre-configured, rate-limited mode for demonstration purposes, utilizing a specific Gemini model.

### 7.2 File Handling
*   **File Attachments:** Users can attach files to their messages via a button or by **drag-and-dropping** a file onto the application window.
*   **Supported Types:**
    *   **Images:** (`.png`, `.jpg`, `.jpeg`, `.webp`) are displayed in an `ImageNode` and can be sent to multi-modal models.
    *   **Documents:** (`.txt`, `.md`, `.py`, `.pdf`, `.docx`, etc.) have their text content extracted and displayed in a `DocumentNode`. This content is also included in the context sent to the LLM.

### 7.3 Content Exporting
Individual nodes can have their content exported to a variety of standard file formats.
*   **`ChatNode` / `DocumentNode`:**
    *   Text File (`.txt`)
    *   Markdown File (`.md`)
    *   HTML Document (`.html`)
    *   Word Document (`.docx`)
    *   PDF Document (`.pdf`)
*   **`CodeNode`:** All of the above, plus **Python Script (`.py`)**.
*   **`ImageNode`:** Can be exported as a `.png` or `.jpg` file.
*   **Dependency Handling:** If an optional library for an export format (e.g., `reportlab` for PDF) is not installed, the application presents a non-crashing dialog informing the user how to install it.

---

## 8. Application & Licensing

*   **EULA:** On first launch, users must agree to an End-User License Agreement.
*   **Licensing Model:**
    *   **Trial:** A fully-featured 5-day trial period begins on first launch after EULA acceptance.
    *   **Perpetual/Yearly Key:** A permanent license key.
    *   **Subscription Key:** A time-limited license key.
*   **Axiom Mode Rate Limiting:** The built-in Axiom provider is rate-limited to 3 uses per 4-hour window to manage demo API costs. The application tracks this and provides feedback to the user.
*   **Theming:** Supports a "Dark" and "Monochromatic" theme, which is persisted across sessions.

---

## 9. Keyboard Shortcut Reference

| Shortcut | Action |
| :--- | :--- |
| `Ctrl + K` | Open Command Palette |
| `Ctrl + F` | Open Search Overlay |
| `Ctrl + T` | New Chat Session |
| `Ctrl + L` | Open Chat Library |
| `Ctrl + S` | Save Current Chat |
| `Ctrl + G` | Create Frame from Selection |
| `Ctrl + Shift + G` | Create Container from Selection |
| `Ctrl + N` | Create a new Note at cursor |
| `Delete` | Delete selected items |
| `Ctrl + Mouse Wheel` | Zoom In / Zoom Out |
| `Middle Mouse Button + Drag` | Pan the Canvas |
| `Shift + Drag` | Zoom to Selection (Box Zoom) |
| `Ctrl + Left-Click` (on connection) | Add a Pin to the connection line |
| `Ctrl + Right-Click` (on pin) | Remove a Pin from the connection line |
