---
name: mission-planning
description: Use when decomposing a mission objective into tasks and planning squad composition. Triggered during the PLAN stage of a /squad mission. Teaches the 參謀總長 how to analyze objectives, break them into parallelizable tasks, and design execution strategy.
---

# Mission Planning — 作戰計畫方法論

You are planning a squad mission. Follow this methodology to decompose the objective into executable tasks.

## Step 1: Identify Deliverables

From the objective, list concrete deliverables — files to create, files to modify, tests to write, docs to update. Each deliverable must be **verifiable** (you can run a command or check a file to confirm it's done).

## Step 2: Map Dependencies

For each deliverable, determine:
- **Blocking dependencies**: What must exist before this can start?
- **Parallel streams**: What can be done simultaneously with no overlap?
- **Critical path**: The longest sequential chain — this determines minimum wall-clock time.

Draw the dependency graph mentally. If A depends on B, they must be sequential. If A and C are independent, they can be parallel.

## Step 3: Size Tasks

Each task must be:
- **Completable by one agent** in a single focused session
- **Independently verifiable** — can run tests or check output without other tasks
- **Bounded** — clear start and end, no open-ended exploration

Bad task: "Improve the UI"
Good task: "Create KeyboardShortcutManager class in engines/keyboard/KeyboardShortcutManager.ts with methods: register(), unregister(), handleKeyDown(). Must pass 5 unit tests."

Bad task: "Set up the backend"
Good task: "Add POST /api/songs endpoint in src/main/ipc/songHandlers.ts that accepts { title, midiData } and writes to SQLite. Return 201 on success."

## Step 4: Assign Priority and Parallelism

Group tasks into waves:

- **Wave 1**: Independent foundation tasks (can all run in parallel)
- **Wave 2**: Tasks depending on Wave 1 results (run after Wave 1 completes)
- **Wave 3**: Integration and verification tasks (run last)

Maximize parallelism in Wave 1 — this is where squad members earn their keep.

## Step 5: Estimate Squad Size

Rules of thumb:
- **1-2 tasks** → No squad needed, 參謀總長 handles directly
- **3-5 independent tasks** → 2-3 squad members
- **6+ tasks with parallelism** → 3-5 squad members (respect max_members from config)
- **Never** spawn more members than there are parallel work streams
- **Never** spawn a member for just one small task — combine small tasks or do them yourself

Consider model selection: use `sonnet` for straightforward implementation, `opus` for tasks requiring complex architectural reasoning, `haiku` for simple mechanical tasks.

## Output Format

Present the plan in this structure:

```
── 作戰計畫 ──────────────────────────
目標：{objective}

任務分解：
#1 {task description} [dependencies: none]
#2 {task description} [dependencies: none]
#3 {task description} [dependencies: #1]
#4 {task description} [dependencies: #1, #2]

執行策略：
Wave 1 (並行): #1, #2
Wave 2 (等待 Wave 1): #3, #4

編組：
• Alpha — {Forged role}: 負責 #1, #3
• Bravo — {Forged role}: 負責 #2, #4

驗證標準：
- {specific command}: {expected result}
- {specific command}: {expected result}
─────────────────────────────────────
```

## Common Mistakes

- **Over-decomposition**: 20 micro-tasks creates coordination overhead worse than the parallelism gains. Aim for 3-8 tasks.
- **Hidden dependencies**: "Modify store X" and "Add UI component using store X" have a dependency — don't put them in the same wave.
- **Unclear deliverables**: "Research options" is not a task. "Write a comparison document at docs/comparison.md covering libraries A, B, C with pros/cons" is.
- **No verification criteria**: If you can't verify it, you can't know it's done.
