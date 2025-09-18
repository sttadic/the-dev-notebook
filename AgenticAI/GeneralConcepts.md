# Agentic AI Basics (General Concepts)

## What is Agentic Mode?

Agentic AI = configurable AI assistant with memory, tools, and personality.
Instead of ad-hoc prompting, you set up an **environment** (instructions + tools + moods) so the AI acts like a teammate.

---

## Core Components

### 1. Prompt Files

- Saved reusable prompts (`.prompt` or `.md`).
- Example: `debugging.prompt` → "Always give root cause analysis before suggesting a fix."
- **Best practice:** Keep modular, short, and task-specific.

### 2. Instructions

- Persistent rules/goals.
- Example: "Always prefer TypeScript over JavaScript. Optimize for readability."
- **Best practice:** Separate global vs task-specific instructions.

### 3. Tool Sets

- Actions AI can perform beyond text (e.g., read file, search project, run tests).
- **Best practice:** Enable only trusted/relevant tools.

### 4. Moods

- Defines AI's tone/personality.
- Examples: Teacher, Engineer, Skeptical Reviewer.
- **Best practice:** Use moods like "roles in a meeting" → don’t overload one agent.

### 5. MCP Servers (Model Context Protocol)

- Protocol to connect AI with external data/tools.
- Example: local server that provides project API schemas.
- **Best practice:** Audit like you would NPM packages; trust carefully.

### 6. Configure Tools

- Control which tools AI can access (Principle of Least Privilege).
- Example: Enable `Terminal.runCommand`, disable `deleteFile`.
- **Best practice:** Start minimal, expand only as needed.
