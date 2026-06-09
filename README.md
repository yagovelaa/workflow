# AI-Driven Development Workflow

A structured workflow for building software features end to end using AI agents (Claude Code). Each step is a **slash command** that produces a versioned artifact, ensuring idea → spec → implementation → validation follow a predictable, traceable process with controlled quality.

## Philosophy

The flow separates **WHAT** (PRD), **HOW** (Tech Spec), and **EXECUTION** (Tasks), with quality gates at every transition:

- Nothing is generated without clarifying questions.
- No task is completed without tests passing 100%.
- Every implementation goes through review and QA before closing.
- All artifacts follow strict templates and the project rules.

## Repository Structure

```
.claude/
  commands/        # The slash commands that drive each step of the flow
    criar-prd.md
    criar-techspec.md
    criar-tasks.md
    executar-task.md
    executar-review.md
    executar-qa.md
    executar-bugfix.md
  rules/           # Coding standards enforced across every step
    code-standards.md
    node.md
    react.md
    http.md
    logging.md
    tests.md
templates/         # Required structure for each generated artifact
  prd-template.md
  techspec-template.md
  tasks-template.md
  task-template.md
prompt-exemple.md  # Example of an initial feature prompt
```

## The Flow

```
  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
  │  criar-prd   │ ──> │ criar-techspec│ ──> │  criar-tasks │
  │   (WHAT)     │     │   (HOW)       │     │  (EXECUTION) │
  └──────────────┘     └──────────────┘     └──────────────┘
                                                    │
                                                    v
  ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
  │ executar-    │ <── │ executar-    │ <── │ executar-    │
  │ bugfix       │     │ qa           │     │ task         │
  └──────────────┘     └──────────────┘     └──────────────┘
         ^                                          │
         │            ┌──────────────┐              │
         └─────────── │ executar-    │ <────────────┘
                      │ review       │
                      └──────────────┘
```

Each feature lives in its own folder: `./tasks/prd-[feature-name]/`.

### 1. `criar-prd` — Product Requirements

Defines **what** will be built and **why**, focused on user and business outcome.

- Asks clarifying questions (mandatory) before drafting.
- Uses Web Search for business rules.
- Follows `templates/prd-template.md`.
- **Output:** `tasks/prd-[name]/prd.md`

### 2. `criar-techspec` — Technical Specification

Translates the PRD into **how** to implement: architecture, interfaces, models, endpoints, tests.

- Explores the project before asking.
- Uses Context7 MCP (technical) + Web Search for decisions.
- Prefers existing libraries over custom development.
- Maps decisions against `.claude/rules`.
- **Input:** `prd.md` · **Output:** `tasks/prd-[name]/techspec.md`

### 3. `criar-tasks` — Task List

Breaks the Tech Spec into incremental, deliverable tasks.

- Shows a high-level list for approval before generating files.
- Each task = a functional deliverable with its own test set.
- Max 10 tasks, ordered by dependency.
- **Input:** `prd.md` + `techspec.md` · **Output:** `tasks.md` + `[num]_task.md`

### 4. `executar-task` — Implementation

Implements the next available task following PRD, Tech Spec, and rules.

- Uses Context7 MCP for library/framework documentation.
- A task only closes with **all tests passing (100%)**.
- Triggers the review agent before marking complete.
- **Updates** `tasks.md` on completion.

### 5. `executar-review` — Code Review

Analyzes the code via `git diff` against rules, Tech Spec, and Tasks.

- Checks naming, structure, error handling, logging, security.
- Runs the test suite (does not approve with a failing test).
- **Output:** `APPROVED / APPROVED WITH RESERVATIONS / REJECTED` report.

### 6. `executar-qa` — Quality Assurance

Validates the implementation against requirements via E2E tests.

- Uses Playwright MCP to navigate and test each flow.
- Checks accessibility (WCAG 2.2) and visual states.
- Documents bugs with evidence in `bugs.md`.
- **Output:** QA report + `bugs.md`.

### 7. `executar-bugfix` — Bug Fixing

Fixes every bug documented in `bugs.md` at the root cause.

- Creates regression tests for each bug.
- Validates typing (`tsc --noEmit`) and runs the whole suite.
- Updates `bugs.md` with status and applied fix.

## Project Rules (`.claude/rules`)

Standards enforced across every generation and validation step:

| File | Covers |
|------|--------|
| `code-standards.md` | Naming, functions, conditionals, size, comments |
| `node.md` | TypeScript, async/await, strong typing, imports/exports |
| `react.md` | Functional components, hooks, Tailwind, Context API, tests |
| `http.md` | REST, Express, Axios, status codes, middlewares |
| `logging.md` | Log levels, sensitive data, structured logs |
| `tests.md` | Jest, AAA/GWT, independence, coverage |

## How to Use

1. Write the initial feature prompt (see `prompt-exemple.md`).
2. Run the commands in sequence: `criar-prd` → `criar-techspec` → `criar-tasks`.
3. Implement with `executar-task` (repeat per task).
4. Validate with `executar-review` and `executar-qa`.
5. Fix pending issues with `executar-bugfix`.

Each artifact is versioned in `./tasks/prd-[feature-name]/`, forming the complete history of the feature.
