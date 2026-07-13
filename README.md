# AI IDE Project Memory System (v2)

A lightweight, standardized, and token-efficient workspace state and behavior persistence system designed for developer-AI agent collaboration. 

This repository templates the `.ai` memory architecture, explaining what it does, how it improves productivity, the trade-offs involved, and how to integrate it seamlessly into modern IDEs (e.g., Cursor, Windsurf, Antigravity, VS Code, and others).

---

## 📖 Table of Contents
1. [What is the AI Project Memory System?](#-what-is-the-ai-project-memory-system)
2. [What It Does (The Core Loop)](#-what-it-does-the-core-loop)
3. [How It Improves Workflows](#-how-it-improves-workflows)
4. [Pros and Cons (Trade-Offs)](#-pros-and-cons-trade-offs)
5. [Directory Layout](#-directory-layout)
6. [How to Import and Use in Your IDE](#-how-to-import-and-use-in-your-ide)
    - [Cursor / Windsurf / Antigravity](#cursor--windsurf--antigravity)
    - [VS Code / Generic Copilot Agents](#vs-code--generic-copilot-agents)
7. [Getting Started (Quick Setup)](#-getting-started-quick-setup)

---

## 🧠 What is the AI Project Memory System?

The AI Project Memory System is a **persistent memory folder (`.ai/`)** situated in the root of your project workspace. Unlike large, auto-generated indices or brute-force code embeddings (which cost massive context window overhead), this system acts as a curated **"handover log"** and **"structural blueprint"** for AI assistants.

It is built on a simple premise: **AI models do not need to read your entire source code to understand what they are supposed to do, what constraints they must follow, or what task is active.** 

By reading a tiny sequence of metadata markdown files at the start of a session, any AI model can immediately align itself to the project context within **under 1000 tokens**—saving cost, reducing hallucinations, and ensuring continuity across multiple chat sessions.

---

## 🔄 What It Does (The Core Loop)

When a developer starts a new chat, the AI agent follows a strict **Startup Workflow**:
1. **Reads `.ai/README.md`**: Understands how the memory files are configured.
2. **Reads `.ai/CONSTRAINTS.md`**: Instantly ingests strict behavioral constraints (e.g., *No silent scope changes*, *No fake command execution outputs*, *Git history rules*).
3. **Reads `.ai/manifest.json`**: Grabs current branch names, modified files, and tasks.
4. **Reads `.ai/session.md`**: Comprehends the immediate objective, blockers, and next steps.
5. **Reads `.ai/summary.md`**: Gets a high-level picture of the project's purpose, architecture layout, and milestone decisions.

### The Write Loop
Before ending a session, the AI agent is instructed to update the memory files:
- `session.md` and `manifest.json` are updated **every session** to log changes.
- `summary.md` and module docs under `docs/` are updated **only** when high-level architecture changes or features are completed.

---

## 📈 How It Improves Workflows

- **Infinite Session Continuity**: Standard LLM chats lose context after a few messages or when you open a new chat tab. Because the agent reads and writes to `.ai/` on your disk, **every new chat session knows exactly where the last one left off.**
- **Cost Reduction**: Avoids sending massive chunks of codebase context repeatedly to the LLM. 
- **Zero Hallucination Blueprint**: If the AI is uncertain of an API or layout, the constraints force it to label the detail as `"unconfirmed — verify against source"` instead of fabricating logic.
- **Unified Rules**: Keeps developer guidelines checked into the codebase where agents are forced to read them, replacing fragile chat instructions.

---

## ⚖️ Pros and Cons (Trade-Offs)

### Pros
- **Ultra-Low Token Overhead**: Standard initialization uses **~500 - 1500 tokens** total, compared to tens of thousands of tokens when using direct codebase context ingestion.
- **Engine-Agnostic**: Works across any LLM (Anthropic Claude, OpenAI GPT, Google Gemini, Meta Llama) and any editor interface.
- **Git Tracked**: Memory changes are version-controlled, meaning you can review exactly how the AI's understanding of the project evolved over time via `git diff`.
- **Prevent Missteps**: The constraints file acts as an active guardrail preventing common agent failures (e.g., rewriting history, deleting files).

### Cons
- **Self-Discipline Required**: If you write code manually without updating the memory files, the AI's cached state in `.ai/session.md` can drift from the actual codebase.
- **Requires Initial Setup**: You must customize the files for your specific project's modules and folder maps.
- **Storage**: Introduces a `.ai/` directory in the repository (though it should be kept extremely lightweight, under 50KB total).

> [!CAUTION]
> **Security & Credentials Warning**: Never write API keys, passwords, database credentials, or sensitive configuration values into any file under the `.ai/` directory. Because `.ai/` is checked into Git by default to sync memory across sessions, any credentials placed here risk being pushed to your public repository. Keep all secrets in `.env` files (which must remain in `.gitignore`).

---

## 📂 Directory Layout

```text
.ai/
├── README.md         # Instructions for AI sessions reading this folder
├── CONSTRAINTS.md    # Rigid, non-negotiable coding rules/behaviors
├── manifest.json     # Extremely tiny tracking JSON (~100 tokens max)
├── session.md        # Session checklist, blockers, and active tasks
├── summary.md        # High-level architecture, tech stack, and roadmap
└── docs/             # (Optional) Deep dives into modules (Read only when needed)
    ├── backend.md
    ├── frontend.md
    └── database.md
```

---

## 🛠️ How to Import and Use in Your IDE

Because this system is file-based, it works out of the box in all environments. Here is how to configure it:

### Cursor / Windsurf / Antigravity
IDE agents in editors like **Cursor** or **Windsurf** automatically read instructions or open files if requested in your initial prompt.

1. **System Prompt Integration**: Add the startup workflow as a rule inside your custom system prompt settings or `.cursorrules` / `.windsurfrules`:
   ```text
   At the start of every chat session, you MUST locate and read the following files in order:
   1. `.ai/README.md`
   2. `.ai/CONSTRAINTS.md`
   3. `.ai/manifest.json`
   4. `.ai/session.md`
   5. `.ai/summary.md`
   Follow the rules in CONSTRAINTS.md without exception.
   ```
2. **Slash Command Reference**: You can type `/` or `@` to mention the `.ai` folder or files explicitly to boot the agent's memory.

### VS Code / Generic Copilot Agents
For standard GitHub Copilot or similar extensions:
1. Open the Chat Panel.
2. Ask the assistant:
   > *"Read .ai/README.md and follow the startup workflow to understand our project status and rules."*

---

## 🚀 Getting Started (Installation Options)

Choose one of the methods below to add the `.ai` memory folder to your project root:

### Option 1: Automate via AI IDE Import (Recommended)
Because modern agentic IDEs support repository cloning and file integration, you can instruct your active AI assistant to fetch and seed this system automatically. 

Simply copy-paste this prompt into your AI assistant chat window:
> **"Please import the AI IDE Project Memory System template from `https://github.com/vishalvermauts/AI-IDE-Project-Memory-System.git` directly into my current project root directory, then update `.ai/summary.md` with my project's current details and tech stack."**

---

### Option 2: Manual Setup
1. Download or clone this repository manually.
2. Copy the `.ai` directory template from this project into your own project root.
3. Update `.ai/summary.md` with your project's technology stack, modules, and target milestones.
4. Add the startup instruction workflow to your IDE's system prompt or configuration settings.

---

## 💾 How Memory Writing is Triggered & Automated

A common problem with agentic IDEs is that **when they finish their token/message budget or hit a crash, they exit immediately without writing to the `.ai/` directory.** 

To prevent this and guarantee continuity, use these strategies:

### 1. Manual Handover Command (Best Practice)
Before you expect the agent to stop, when you are wrapping up a milestone, or if you notice you are running low on message budget, explicitly issue this single command in the chat:
> **"Please run the completion checklist: verify all changes against the codebase and update `.ai/session.md`, `.ai/manifest.json`, and if necessary, `.ai/summary.md` and module docs under `.ai/docs/` with our recent work."**

This forces the AI to output drop-in replacements for all outdated memory state files in a single turn *before* you close the session.

### 2. Custom Scripts & Hooks (Git Integration)
You can automate the generation of `manifest.json` or parts of `session.md` using simple pre-commit hooks or scripts. For example, a script to automatically parse modified files and write them to `manifest.json`:

```bash
# Example script to update manifest modified files list automatically before git commits:
git status --porcelain | awk '{print $2}'
```

### 3. Agent Rules
By placing the following instruction in your IDE's system instructions (e.g. `.cursorrules`), the agent will proactively write updates as it works:
```text
State Save Rule: Immediately after completing any file edit, command execution, or task step, you must update .ai/session.md and .ai/manifest.json to reflect the current workspace state. Do not wait for the end of the session.
```

