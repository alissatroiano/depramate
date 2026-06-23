# DepraMate

## System Plan
Designing an AI agent for autonomous code refactoring requires moving away from simple linear scripting (like a chatbot) and moving toward an event-driven loop. Because code updates break things, the agent must be able to write code, test it, read error logs, and fix its own mistakes.

To hit the hackathon’s Google Cloud requirement, you can build this using Python (via LangGraph or LlamaIndex Workflows) and deploy the compute blocks on Google Cloud Run, using Vertex AI (Gemini 1.5 Pro or Gemini 2.0 Flash) as the reasoning engine due to its massive context window.

### The AI Agent Lifecycle Architecture

#### 1. Ingest & Parse the Migration Guide:
***Vertex AI Document API.***
The user provides a URL or Markdown file of the migration guide (e.g., Upgrading Next.js 13 to 14). An ingestion worker uses an LLM to parse this into a structured JSON dictionary of "Old Syntax Patterns" → "New Syntax Patterns."

#### 2. Repository Mapping & AST Extraction:
***Google Cloud Run Worker.***
The agent clones the user's repository. Instead of doing a blind regex string search, it parses the target files into an Abstract Syntax Tree (AST). It identifies where the deprecated package is imported and tracks every file where those functions/classes are actively called.

#### 3. The Context Assembly & Planning:
***Gemini Long Context Window.***
The agent passes the relevant source code files, the structured migration rules, and the existing repository structure into the context window. It prompts the LLM to generate a Refactoring Plan (a checklist of file-by-file changes required).

#### 4. Execution Loop & Self-Correction:
***Sandboxed Execution Environment.***
The agent applies the changes to the code. It then immediately triggers the repository's test suite (e.g., npm test or pytest).
- If tests pass, it moves to the next step.
- If tests fail, the error logs are fed back into the agent as new context. It enters a loop to rewrite the code until the tests clear.

#### 5. PR Generation & Agent Logging
***GitHub/GitLab Integration***
Once the codebase successfully passes its local checks, the agent programmatically staging the files, commits them, pushes a branch, and opens a Pull/Merge Request containing a clean breakdown of the changes it made.

## Designing the Agentic State Machine
Since you need to supply agent logs to the XPRIZE judges, you should explicitly structure your code as a state machine. If an execution step fails, the agent transitions backward to a correcting state, rather than crashing out.
```
[Start]
          │
          ▼
   ┌──────────────┐
   │ Ingest Docs  │
   └──────┬───────┘
          │
          ▼
   ┌──────────────┐
   │ Locate Debt  │◄───────────────────┐
   └──────┬───────┘                    │
          │                            │
          ▼                            │ Refactor Failed
   ┌──────────────┐   Test Fails   ┌───┴──────────┐
   │ Apply Fixes  ├───────────────►│ Debug & Patch│
   └──────┬───────┘                └──────────────┘
          │
      Test Passes
          ▼
   ┌──────────────┐
   │  Generate PR │
   └──────┬───────┘
          │
          ▼
        [End]
```


#### GitLab Integration

- GitLab CI/CD for GitHub: Use GitLab's CI/CD features without migrating your source code from GitHub.
- Repository Mirroring: Automatically synchronize changes between a GitLab repository and a GitHub repository in either direction.
- Project Import/Migration: One-time transfer of a project, including issues and pull requests, from GitHub to GitLab.
- Authentication: Allow users to sign in to GitLab using their GitHub credentials (OAuth).
