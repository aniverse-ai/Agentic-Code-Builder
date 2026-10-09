# AgenticCodeBuilder

A multi-agent AI system that turns a plain-English project idea into a working codebase. Built with [LangGraph](https://github.com/langchain-ai/langgraph) and LLMs served through [Groq](https://groq.com/).

## How It Works

The system is a LangGraph pipeline of three agents. Each one hands its output to the next:

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

## Project Structure

```
├── main.py            # CLI entry point
└── agent/
    ├── graph.py       # LangGraph workflow and agent definitions
    ├── prompts.py     # Prompts for the Planner, Architect and Coder
    ├── states.py      # Pydantic schemas (Plan, TaskPlan, CoderState)
    └── tools.py       # Sandboxed file-system tools for the Coder
```

## Setup & Usage

1. Requires Python 3.11+. Install dependencies with [uv](https://github.com/astral-sh/uv):
   ```bash
   uv sync
   ```
2. Create a `.env` file with your Groq API key:
   ```
   GROQ_API_KEY=your_api_key_here
   ```
3. Run the generator and enter your project idea when prompted:
   ```bash
   uv run main.py
   ```
   Optional: `-r` / `--recursion-limit` sets the graph's recursion limit (default: 100).

The generated code is written to the `generated_project/` directory.
