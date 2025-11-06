---
description: Execute frontend implementation in autonomous execution mode
---

**Command Context**: As orchestrator, autonomously completes frontend implementation in autonomous execution mode.

Work plan: $ARGUMENTS

## 📋 Pre-execution Prerequisites

### Task File Existence Check
```bash
# Check work plans
! ls -la docs/plans/*.md | grep -v template | tail -5

# Check task files
! ls docs/plans/tasks/*.md 2>/dev/null || echo "⚠️ No task files found"
```

### Task Generation Decision Flow

**THINK DEEPLY AND SYSTEMATICALLY**: Analyze task file existence state and determine the EXACT action required:

| State | Criteria | Next Action |
|-------|----------|-------------|
| Tasks exist | .md files in tasks/ directory | Proceed to autonomous execution |
| No tasks + plan exists | Plan exists but no task files | Confirm with user → run task-decomposer |
| Neither exists | No plan or task files | Error: Prerequisites not met |

## 🔄 Task Decomposition Phase (Conditional)

When task files don't exist:

### 1. User Confirmation
```
No task files found.
Work plan: docs/plans/[plan-name].md

Generate tasks from the work plan? (y/n):
```

### 2. Task Decomposition (if approved)
```
@task-decomposer Read work plan and decompose into atomic tasks:
- Input: docs/plans/[plan-name].md
- Output: Individual task files in docs/plans/tasks/
- Granularity: Each task independently executable (commit timing determined by user's chosen strategy)
```

### 3. Verify Generation
```bash
# Verify generated task files
! ls -la docs/plans/tasks/*.md | head -10
```

✅ **MANDATORY**: After task generation, AUTOMATICALLY proceed to autonomous execution
❌ **PROHIBITED**: Starting implementation without task generation

## 💬 Commit Strategy Selection

**🚨 MANDATORY: Ask user about commit strategy BEFORE starting task execution**

```
How would you like me to create commits?
A) After each task (detailed history, easy rollback) - Default
B) After each phase (cleaner history, logical grouping)
C) After all phases complete (single feature commit)
```

Store the user's choice and follow it throughout execution.

## 🧠 Metacognition for Each Task - Frontend Specialized

**MANDATORY EXECUTION CYCLE**: `task-executor-frontend → quality-fixer-frontend → commit (based on strategy)`

### Sub-agent Invocation Method
Use Task tool to invoke sub-agents:
- `subagent_type`: Agent name
- `description`: Brief task description (3-5 words)
- `prompt`: Specific instructions

### Structured Response Specification
Each sub-agent responds in JSON format:
- **task-executor-frontend**: status, filesModified, testsAdded, readyForQualityCheck
- **quality-fixer-frontend**: status, checksPerformed, fixesApplied, approved

### Execution Flow for Each Task

Execute for EACH task:

1. **USE task-executor-frontend**: Execute frontend implementation
   - Invocation example: `subagent_type: "task-executor-frontend"`, `description: "Task execution"`, `prompt: "Task file: docs/plans/tasks/[filename].md Execute implementation"`
2. **PROCESS structured responses**: When `readyForQualityCheck: true` is detected → EXECUTE quality-fixer-frontend IMMEDIATELY
3. **USE quality-fixer-frontend**: Execute all quality checks (Lighthouse, bundle size, tests, etc.)
   - Invocation example: `subagent_type: "quality-fixer-frontend"`, `description: "Quality check"`, `prompt: "Execute all frontend quality checks and fixes"`
4. **EXECUTE commit**: After `approved: true` confirmation, execute git commit based on chosen strategy

### Quality Assurance During Autonomous Execution (Details)
- task-executor-frontend execution → quality-fixer-frontend execution → **I (Main AI) execute commit** (based on chosen strategy)
- After quality-fixer-frontend's `approved: true` confirmation:
  - **Strategy A (Per-Task)**: Execute git commit immediately
  - **Strategy B (Per-Phase)**: Store changeSummary, commit when phase completes
  - **Strategy C (Final)**: Store all changeSummaries, commit when all phases complete
- Use accumulated changeSummary for commit message (combine multiple if needed)

**THINK DEEPLY**: Monitor ALL structured responses WITHOUT EXCEPTION and ENSURE every quality gate is passed.

! ls -la docs/plans/*.md | head -10

VERIFY approval status before proceeding. Once confirmed, INITIATE autonomous execution mode. STOP IMMEDIATELY upon detecting ANY requirement changes.

## Output Example
Frontend implementation phase completed.
- Task decomposition: Generated under docs/plans/tasks/
- Implemented tasks: [number] tasks
- Quality checks: All passed (Lighthouse, bundle size, tests)
- Commits: [number] commits created

**Important**: This command manages the entire autonomous execution flow for FRONTEND implementation from task decomposition to completion. Automatically uses frontend-specialized agents (task-executor-frontend, quality-fixer-frontend).
