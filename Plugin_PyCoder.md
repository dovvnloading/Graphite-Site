<img width="1919" height="1031" alt="Screenshot 2025-11-03 075119" src="https://github.com/user-attachments/assets/001eaf5a-0734-4b95-8c55-fbf6bc323c2c" />



# Graphite User Manual: The PyCoder Plugin

## 1. Introduction

The **PyCoder Plugin** is a powerful tool integrated within Graphite that enables local Python code execution and AI-driven analysis. It functions as an interactive coding environment on your canvas, allowing you to solve problems, perform calculations, and process data that would be difficult for a language model to handle on its own.

The plugin operates in two distinct modes:

*   **AI-Driven Mode (Default):** The AI acts as an intelligent agent. You provide a prompt, and the AI determines if code is needed to answer it. If so, it writes the code, executes it, and then uses the result to formulate a comprehensive final answer.
*   **Manual Mode:** This mode provides a direct, hands-on coding scratchpad. You write and execute your own Python code, and can then optionally have the AI analyze your code and its output.

## 2. Accessing the PyCoder Plugin

To add a PyCoder node to your canvas:

1.  **Select a Context Node:** Click on any existing `ChatNode` on the canvas. The PyCoder node will branch off from this selected node, using it as its conceptual starting point.
2.  **Open the Plugins Menu:** Navigate to the main toolbar at the top of the window and click on the "Plugins" dropdown menu.
3.  **Select "Py-Coder":** Choose "Py-Coder" from the list. A new PyCoder node will appear on the canvas, connected to your selected context node.

## 3. Anatomy of the PyCoder Node

The PyCoder node is a self-contained environment with a clear, structured interface.



1.  **Header:** Displays the current mode ("AI-Driven" or "Manual").
2.  **Mode Toggle Button:** An icon button (either a robot or a user) that allows you to switch between the two modes.
3.  **Prompt Input (AI-Driven Mode):** A text area where you enter your high-level request for the AI.
4.  **Manual Code Input (Manual Mode):** A text area where you write your own Python code.
5.  **Run Button:** Labeled "Generate & Execute" in AI-Driven mode or "Run Code" in Manual mode. This button initiates the workflow.
6.  **Generated Code:** A read-only display where the code (either AI-generated or manually entered) is shown post-execution.
7.  **Execution Output / Stderr:** A read-only display showing the `print()` output and any errors from the executed code.
8.  **AI Analysis:** A read-only display where the AI provides its final, synthesized answer or analysis.

## 4. Workflow: AI-Driven Mode

This is the default and most powerful mode. It allows the AI to use code as a tool to solve your problems.

**Step-by-Step Guide:**

1.  **Enter a Prompt:** In the "Prompt" text area, write a clear, descriptive request. This should be a problem to solve, not the code itself.
    *   **Good Prompt:** `What is the 15th number in the Fibonacci sequence?`
    *   **Bad Prompt:** `print(fib(15))`

2.  **Generate & Execute:** Click the **"Generate & Execute"** button. The node will enter a running state.

3.  **Review the Results:** The automated workflow will complete in three stages, populating the fields in order:
    *   **Generated Code:** The AI will generate Python code to solve your prompt and display it here.
    *   **Execution Output:** The generated code is run locally and securely. Any `print()` statements or errors are captured and displayed here.
    *   **AI Analysis:** The AI receives your original prompt, its generated code, and the execution output. It then synthesizes all this information into a final, human-readable answer.

**Example Use Case:**

*   **Prompt:** `Please fetch the title from the website at https://example.com and tell me its length.`
*   **Generated Code:** The AI might generate code using `urllib.request` and `re` to get the webpage content and extract the title tag.
*   **Execution Output:** `93`
*   **AI Analysis:** "The Python script successfully fetched the content from `https://example.com`. The title of the page is 'Example Domain', and its length is 93 characters."

## 5. Workflow: Manual Mode

This mode gives you full control to write, execute, and analyze your own code snippets.

**Step-by-Step Guide:**

1.  **Switch to Manual Mode:** Click the mode toggle button in the header. The icon will change, and the UI will reconfigure to show the "Manual Code" input area.

2.  **Write Your Code:** Enter your desired Python script into the **"Manual Code"** text area.

3.  **Run Code:** Click the **"Run Code"** button.

4.  **Review the Results:**
    *   **Execution Output:** Your code is executed locally. The output and any errors are displayed here.
    *   **AI Analysis:** After execution, the AI is automatically asked to analyze your code and its output. This is useful for debugging, getting explanations, or suggesting improvements.

**Example Use Case:**

*   **Manual Code:**
    ```python
    def my_func(items):
        return items.sort()

    my_list = [3, 1, 4]
    result = my_func(my_list)
    print(result)
    ```
*   **Execution Output:** `None`
*   **AI Analysis:** "The provided code defines a function `my_func` that attempts to sort a list. However, the `list.sort()` method sorts the list in-place and returns `None`. The `print(result)` statement therefore correctly outputs `None`. To fix this and print the sorted list, you should either print `my_list` after calling the function, or use the `sorted()` function which returns a new sorted list."

## 6. Integration and Best Practices

*   **Graph Rules:** The PyCoder node behaves like any other node on the canvas. It respects **Snap to Grid** and **Smart Guides**, and its connection respects **Orthogonal Routing**. It can be selected, moved, and grouped into Frames and Containers.
*   **Security:** All code is executed locally on your machine in a sandboxed process. Be mindful of the code you run, just as you would with any local script.
*   **Model Choice:** For best results in AI-Driven mode, use a model that has strong code generation capabilities (e.g., `deepseek-coder`, `phi-3`, or larger models like `llama3:70b`).
