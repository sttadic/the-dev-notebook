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

<br>

# Agentic AI in VSCode (Practical Setup)

## Overview

Agentic mode turns VSCode into a configurable environment where an AI teammate uses persistent instructions, tools, and role-based moods to collaborate on coding tasks. This setup prioritizes repeatable workflows, project alignment, and safe tool permissions so assistance stays consistent and reliable across sessions.

---

## Why VSCode?

VSCode is a popular environment for agentic AI because it offers rich extension APIs, workspace-scoped settings, and seamless integration with project files and tasks. Extensions can persist prompts, expose safe tool capabilities, and leverage project-wide context to deliver relevant, code-aware assistance.

---

## Core setup steps

### 1. Install an agentic AI extension

- **Examples:** Copilot Chat, Cursor, or other agentic-mode-enabled extensions.
- **Provides:**
  - **Prompt persistence:** Save instructions across sessions.
  - **Tool access:** Read files, search, and run commands.
  - **Context awareness:** Project-wide reasoning and code navigation.
- **Best practice:** Start with one extension, learn its workflow, then expand.

### 2. Create a `.prompts/` directory

- **Purpose:** Store reusable prompt files in your repo for version control and sharing.
- **Example structure:**

```
.prompts/
├── debugging.prompt.md
├── reviewer.prompt.md
└── teacher.prompt.md
```

- **Benefits:**
  - **Version-controlled prompts:** Keep changes auditable.
  - **Easy sharing:** Standardize across a team.
  - **Modular roles:** Swap in/out as tasks change.

### 3. Configure global instructions

- **Where:** Extension settings or a workspace `config.json`.
- **Example:**

```
{
  "preferences": {
    "language": "TypeScript",
    "style": "readable-first",
    "testing": "always include unit tests"
  },
  "rules": [
    "Explain rationale before refactors",
    "Prefer async/await over callbacks"
  ]
}
```

- **Goal:** Align assistant output with project conventions and standards.

### 4. Enable/disable tools (principle of least privilege)

- **In VSCode:** Tools map to extension capabilities (e.g., run terminal commands, edit files).
- **Example:**

```
{
  "tools": {
    "allow": ["readFile", "searchProject", "terminal.runCommand"],
    "deny": ["deleteFile", "installPackage"]
  }
}
```

- **Best practice:** Start minimal; expand only when needed.

### 5. Define moods as roles

- **Approach:** Treat moods like meeting roles for your AI to fit the task at hand.
- **Examples:**
  - **Teacher Mode:** Step-by-step explanations, scaffolding, and clarity.
  - **Reviewer Mode:** Critical, concise, focuses on correctness and style.
  - **Engineer Mode:** Direct, implementation-focused, prioritizes working code.
- **Tip:** Switch moods per task (debugging vs. design vs. refactor).

### 6. Connect MCP servers (optional)

- **Use when supported:** Attach local APIs, databases, or schema servers for richer context.
- **Example:** `project-schema.mcp` → autocomplete API calls with validated parameters.
- **Security tip:** Audit MCP servers like NPM packages (version, provenance, sandbox).

---

## Commands / Examples

### Reusable reviewer prompt

```
# Reviewer Mode
- Check correctness, performance, and readability.
- Enforce TypeScript strictness; prefer explicit types.
- Flag tests that lack edge cases (null, empty, large input).
- Require a short "risk assessment" before suggesting changes.
```

### Task-specific debugging prompt

```
# Debugging Mode
- Identify root cause first, then propose minimal fixes.
- Show reproduction steps and failing test before changes.
- Provide a rollback note if uncertainty is high.
```

### Apply a profile (example flow)

```
1. Open Command Palette → "Agentic: Apply Profile"
2. Select `Engineer Mode` and enable tools: readFile, searchProject, terminal.runCommand
3. Load `.prompts/reviewer.prompt.md` for pre-commit reviews
4. Run tests: `terminal.runCommand` → `npm test -- --watch=false`
```

---

## Quick tips

- **Modularize prompts:** Keep small, focused files for roles and tasks.
- **Workspace scope:** Store config in the repo to ensure team-wide consistency.
- **Log decisions:** Capture AI suggestions in PR descriptions for traceability.
- **Fail safely:** Prefer diff previews and confirm edits via PRs rather than direct writes.

---

## Common pitfalls

- **Overloaded profiles:** Too many rules cause inconsistent behavior; split by role/task.
- **Excessive permissions:** Broad tool access risks unintended changes; restrict early.
- **Unversioned prompts:** Drift across machines; always commit `.prompts/`.
- **Role confusion:** Mixing reviewer and engineer behaviors reduces clarity; switch explicitly.

---

## Further reading

- **VSCode Extension API:** Understand capabilities and limits for safe tooling.
- **Model Context Protocol (MCP):** Patterns for connecting external data and tools.
- **AI pair programming practices:** Techniques for effective human–AI collaboration.

---

## Quick example workflow

- **Open project:** Ensure `.prompts/` and `config.json` are present.
- **Activate Reviewer Mode:** Load `reviewer.prompt.md`; scan `src/` for code smells.
- **Switch to Engineer Mode:** Apply focused fixes; keep diffs minimal.
- **Run tests:** Use `terminal.runCommand`; verify green before merging.
- **Result:** A rotating AI teammate that adapts role to context (reviewer → teacher → engineer).
