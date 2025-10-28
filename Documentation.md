
# Graphite - User Documentation

**Version 1.0**

Welcome to Graphite, the visual AI conversation tool designed for structured thinking. This guide will walk you through the core concepts and features that make Graphite a powerful environment for brainstorming, research, and creative exploration.

## 1. The Graphite Philosophy: Beyond the Chat Log

Traditional AI chats are linear and forgettable. You have a conversation, you close the window, and the context is lost. Graphite is different.

It transforms your conversations into a **visual graph**, where every prompt and response becomes a distinct **node**. This allows you to:

-   **Visualize Connections:** See exactly how ideas relate to each other.
-   **Explore Tangents:** Branch off from any point in the conversation to explore a new idea without losing your original train of thought.
-   **Build a Knowledge Base:** Create a persistent, organized canvas of your AI-driven work that you can save, revisit, and expand upon over time.

---

## 2. The Main Interface: Your Workspace

Your Graphite window is an infinite canvas with four key areas:

1.  **The Canvas:** The main, dark-themed area where your conversation nodes appear. You can pan around by holding the **Middle Mouse Button** and dragging.
2.  **The Toolbar:** Located at the top, this bar provides quick access to navigation, organization tools, and settings.
3.  **The Input Area:** At the bottom of the window, this is where you type your messages and attach images to send to the AI.
4.  **The Pin Overlay:** A collapsible panel on the left that holds your "Navigation Pins"—bookmarks for quickly jumping to important areas of a large graph.

---

## 3. Getting Started: Your First Conversation

Let's begin. The core workflow is simple and intuitive.

1.  **Send Your First Message:** Type a question or prompt into the input bar at the bottom and press **`Enter`**. Two nodes will appear: a green one for your prompt and a blue one for the AI's response, connected by a line.
2.  **Continue the Conversation:** With the blue AI response node selected, type another message. A new pair of nodes will branch off from it.
3.  **Create a Branch:** This is where Graphite's power becomes clear. Instead of replying to the latest message, **click on any previous node in the graph**. It will become highlighted. Now, send a new message. You will see a new branch grow from that selected point, allowing you to explore a different idea in parallel.

> **Core Concept:** The currently selected node is the **context** for your next message.

---

## 4. Mastering the Features

Graphite is packed with tools to help you manage and enrich your conversations.

### 4.1. Navigation and View Control

-   **Pan:** Hold the **Middle Mouse Button** and drag to move across the canvas.
-   **Zoom:** Hold **`Ctrl`** and scroll your **Mouse Wheel** to zoom in and out.
-   **Reset Zoom:** Click the **Reset** button in the toolbar to return to the default zoom level.
-   **Fit to View:** Click the **Fit All** button to automatically zoom and pan the canvas to show all existing nodes.

### 4.2. Multi-modal Chat: Using Images

You can send images to the AI for analysis, questions, or creative prompts.

-   **Attach an Image:** Click the **Paperclip Icon** (`📎`) in the input area. A file dialog will open. Select a `.jpg`, `.png`, or other supported image file.
-   **Confirm Attachment:** The paperclip icon will turn into a green checkmark (`✔`), and hovering over it will show the filename.
-   **Send the Message:** Type an optional text prompt to go with the image (e.g., "What is this object?") and press **`Enter`**. The image will appear inside your user node, and the AI will respond to both the image and your text.

### 4.3. Organization Tools

As your graph grows, use these tools to maintain clarity.

-   **Frames (Grouping Nodes):**
    -   **To Create:** Select one or more nodes by clicking and dragging a selection box around them. Press **`Ctrl+F`**. A colored frame will appear, grouping them.
    -   **To Edit:** Double-click the frame's title bar to rename it. Click the color palette icon to change its color.

-   **Notes (Annotations):**
    -   **To Create:** Press **`Ctrl+N`**. A "sticky note" will appear at your cursor's location. Double-click it to add your own text. Notes are useful for adding summaries, to-do lists, or external information to your canvas.

-   **Navigation Pins (Bookmarks):**
    -   **To Create:** Click the **"Drop New Pin"** button in the left-side Pin Overlay. A red pin will appear at the center of your current view.
    -   **To Use:** Double-click a pin to edit its title. Click a pin's name in the overlay to instantly navigate to that location on the canvas.

-   **Connection Pins (Reshaping Lines):**
    -   **To Create:** Hold **`Ctrl`** and **Left-Click** on any connection line. A small white dot will appear.
    -   **To Use:** Drag this pin to bend and reshape the line, allowing you to create clean, flowing layouts and avoid overlapping lines.

### 4.4. AI-Powered Tools (Node Context Menu)

**Right-click on any node** to open the context menu and access powerful generative actions:

-   **`Generate Key Takeaway`:** Creates a new Note that concisely summarizes the main points of the selected node's text.
-   **`Generate Explainer`:** Creates a new Note that breaks down the node's content into simple terms, analogies, and key parts, as if explaining it to a beginner.
-   **`Generate Chart`:** If the node's text contains data (e.g., sales figures, survey results), this menu allows you to automatically create a new Chart item (bar, line, pie, etc.) to visualize that data.
-   **`Regenerate Response`:** (Available on AI nodes only) Asks the AI to provide a new, different answer to the same prompt that generated this response.

---

## 5. Session Management: The Chat Library

Your work in Graphite is automatically saved. Use the Chat Library to manage all your sessions.

-   **Open Library:** Click the **Library** button in the toolbar or press **`Ctrl+L`**.
-   **Load a Chat:** Double-click any chat in the list to close your current session and load the selected one.
-   **Create a New Chat:** Click the **"New Chat"** button. This will clear the canvas for a fresh start. Your previous session is safely stored in the library.
-   **Rename/Delete:** Select a chat and use the **Rename** or **Delete** buttons to manage your library.

---

## 6. Configuration: Local vs. Cloud AI

Graphite supports two modes of operation, selectable from the top-right of the toolbar.

#### Ollama (Local Mode)

-   **What it is:** Uses your computer's own processing power (CPU/GPU) to run AI models locally. This is excellent for privacy, offline use, and experimenting with open-source models.
-   **Setup:**
    1.  Ensure you have [Ollama](https://ollama.com/) installed and running.
    2.  In Graphite, select **Ollama** mode and click **Settings**.
    3.  Enter the name of a model you have installed (e.g., `llama3:8b`) and save.

#### API Endpoint (Cloud Mode)

-   **What it is:** Connects to a powerful, cloud-based AI service like Google Gemini or any OpenAI-compatible provider. This gives you access to state-of-the-art models.
-   **Setup:**
    1.  Select **API Endpoint** mode and click **Settings**.
    2.  Choose your provider (e.g., "Google Gemini").
    3.  Enter your **API Key**.
    4.  Click **Load Models**, select the desired models for different tasks, and save.

---

## 7. Keyboard Shortcuts

| Action                  | Shortcut        |
| ----------------------- | --------------- |
| **Session**             |                 |
| Open Chat Library       | `Ctrl + L`      |
| Save Current Chat       | `Ctrl + S`      |
| **Organization**        |                 |
| Create Frame            | `Ctrl + F`      |
| Create Note             | `Ctrl + N`      |
| **Selection**           |                 |
| Select All Nodes        | `Ctrl + A`      |
| Delete Selected Item    | `Delete`        |
| **Connections**         |                 |
| Add Pin to Connection   | `Ctrl + Left-Click` on line |
| Remove Pin              | `Ctrl + Right-Click` on pin |

---

## 8. Support

For questions, bug reports, or feature requests, please contact our support team at **dev.graphite@gmail.com**.
