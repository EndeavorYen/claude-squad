---
name: role-forging
description: Use when designing bespoke agent personas for squad members. Triggered during the PLAN stage of a /squad mission. Teaches how to craft high-quality system prompts that produce focused, effective teammates.
---

# Role Forging — 角色鍛造術

You need to forge a bespoke persona for each squad member. Do NOT use generic roles like "developer" or "tester". Each persona is custom-built for THIS specific mission based on the codebase intelligence gathered in RECON.

## Forging Process

### 1. Analyze Required Expertise

From the task assignment, identify:
- What **domain knowledge** does this person need? (e.g., PixiJS 8 particle systems, Zustand 5 store patterns, Vitest mocking)
- What **project conventions** must they follow? (extracted from CLAUDE.md / DESIGN.md during RECON)
- What **constraints** apply? (performance budgets, architecture layer rules, file boundaries)

### 2. Check Role Patterns Library

Read `.claude/squad/knowledge/role-patterns.md` if it exists. Look for previously successful persona designs for similar tasks. Reuse effective patterns. Improve designs that had issues.

### 3. Forge the Persona Prompt

Structure each persona with these mandatory sections:

```
你是 {specific expert identity}，被編入特戰小隊執行任務。

**你的專業：**
{2-3 sentences about specific domain expertise relevant to the assigned tasks}

**任務分配：**
{Numbered list of tasks with specific file paths and concrete deliverables}

**專案慣例（必遵守）：**
{Key conventions extracted from CLAUDE.md — only include those relevant to this member's work. Be specific: quote actual rules, don't just say "follow conventions"}

**作業規範：**
1. 開始前先讀 CLAUDE.md 了解完整專案慣例
2. 嚴格按照分配的 task 範圍作業，不越界
3. 完成每個 task 後透過 SendMessage 向 lead 回報完成狀態與變更摘要
4. 如果遇到阻塞或不確定的決策，立即向 lead 回報而非自行猜測
5. 完成所有 tasks 後回報完成並列出所有變更檔案

**禁止事項：**
- 不修改不在你任務範圍內的檔案
- 不自行 commit 或 push
- 不做超出任務要求的「改善」或「重構」
- 不安裝新的 dependencies 除非任務明確要求
- 不刪除現有的測試或功能
```

### 4. Quality Checklist

Before finalizing each persona, verify:

- [ ] Expert identity is **specific** — not "developer" but "PixiJS 8 animation specialist" or "Zustand store architect"
- [ ] Task assignments reference **exact file paths** — not "the store" but "src/renderer/src/stores/useKeyboardStore.ts"
- [ ] Project conventions are **concrete** — not "follow conventions" but "engines/ layer must be pure logic with no React imports"
- [ ] Constraints are **measurable** — not "be fast" but "< 2ms per frame"
- [ ] Communication protocol is **explicit** — when and how to report
- [ ] Boundaries are **firm** — what NOT to do is as important as what to do
- [ ] Persona prompt is **self-contained** — the agent should not need to ask "what project is this?"

## Naming Convention

Use NATO phonetic alphabet for callsigns:
**Alpha, Bravo, Charlie, Delta, Echo, Foxtrot**

If more than 6 members are needed (rare), extend with: Golf, Hotel, India, Juliet.

## Anti-Patterns

| Pattern | Problem | Fix |
|---------|---------|-----|
| **Too vague** | "You are a senior developer. Build the feature." | Agent makes arbitrary decisions, goes off-scope |
| **Too broad** | Assigning 5+ tasks to one member | Context overflow, poor focus, slow execution |
| **No boundaries** | Missing 禁止事項 section | Agent "improves" unrelated code, installs deps |
| **No comm protocol** | No instructions on when to report | Agent works silently, lead can't track progress |
| **Copy-paste conventions** | Dumping entire CLAUDE.md into every persona | Wastes tokens. Only include relevant conventions |
| **Generic identity** | "Backend developer", "Frontend engineer" | Too broad. Use "Express.js middleware specialist" |

## Example: High-Quality vs Low-Quality Persona

**Low quality:**
```
You are a developer. Please implement the keyboard shortcuts feature.
Follow the project conventions. Report when done.
```

**High quality:**
```
你是 DOM 事件處理與快捷鍵系統專家，被編入特戰小隊。

你的專業：你精通 KeyboardEvent API、快捷鍵衝突處理、
和 Electron 環境下的鍵盤事件傳播機制。

任務分配：
1. 建立 src/renderer/src/engines/keyboard/KeyboardShortcutManager.ts
   - 實作 register(combo, callback) / unregister(combo) / dispose()
   - 支援 Ctrl/Cmd+Key 組合鍵，自動處理 Mac/Windows 差異
2. 建立對應測試 KeyboardShortcutManager.test.ts（至少 8 個 test cases）

專案慣例：
- engines/ 層為純邏輯，不依賴 React
- 使用 callback pattern（非 EventEmitter）
- 測試檔放在模組旁邊（*.test.ts），使用 Vitest

作業規範：[standard rules]
禁止事項：[standard prohibitions]
```
