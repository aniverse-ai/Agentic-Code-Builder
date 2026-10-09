# Agentic Code Builder

A multi-agent AI system that turns a plain-English project idea into a working codebase. Specialized agents handle requirement planning, architecture design, and code generation, cutting the time it takes to bootstrap a new project.

## How It Works

### 1. Planner Agent
- Reads the user's project request and turns it into a complete, structured engineering **Plan**.
- The plan has the app's **name**, a one-line **description**, the **tech stack** to use, a list of **features**, and every **file** to create along with its purpose.
- Uses structured output so the plan always follows a fixed schema that the later agents can rely on.

### 2. Architect Agent
- Takes the Planner's plan and breaks it into an ordered list of concrete **implementation tasks**, one or more per file.
- Each task says exactly what to build: the variables, functions, classes and components to define, plus imports, function signatures and data flow between modules.
- Orders the tasks so dependencies are built first. Each task carries forward the context it needs from earlier tasks, so it can be implemented on its own.

### 3. Coder Agent
- A tool-using ReAct agent that works through the Architect's tasks one at a time, looping until every task is done.
- For each task it reads the target file's current content, then writes the full implementation and keeps it consistent with the other modules (naming, imports, interfaces).
- Has sandboxed file tools (`read_file`, `write_file`, `list_files`, `get_current_directory`) that only work inside the `generated_project/` directory.

## Setup & Usage

1. Requires Python 3.11+. Install dependencies with [uv](https://github.com/astral-sh/uv):
   ```bash
   uv sync
   ```
2. Run the generator and enter your project idea when prompted:
   ```bash
   uv run main.py
   ```
   Optional: `-r` / `--recursion-limit` sets the graph's recursion limit (default: 100).
