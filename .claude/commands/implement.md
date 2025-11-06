---
description: Orchestrate the complete implementation lifecycle from requirements to deployment
---

**Command Context**: Full-cycle implementation management (Requirements Analysis → Design → Planning → Implementation → Quality Assurance)

Strictly adhere to @docs/guides/sub-agents.md and operate exclusively as an orchestrator.

## Execution Decision Flow

### 1. Current Situation Assessment
Instruction Content: $ARGUMENTS

**Think deeply** Assess the current situation:

| Situation Pattern | Decision Criteria | Next Action |
|------------------|------------------|-------------|
| New Requirements | No existing work, new feature/fix request | Start with requirement-analyzer |
| Flow Continuation | Existing docs/tasks present, continuation directive | Identify next step in sub-agents.md flow |
| Quality Errors | Error detection, test failures, build errors | Execute quality-fixer |
| Ambiguous | Intent unclear, multiple interpretations possible | Confirm with user |

### 2. Progress Verification for Continuation

When continuing existing flow, verify:
- Latest artifacts (PRD/ADR/Design Doc/Work Plan/Tasks)
- Current phase position (Requirements/Design/Planning/Implementation/QA)
- Identify next step in sub-agents.md corresponding flow

### 3. Next Action Execution

**MANDATORY sub-agents.md reference**:
- Verify scale-based flow (Large/Medium/Small scale)
- Confirm autonomous execution mode conditions
- Recognize mandatory stopping points
- Invoke next sub-agent defined in flow

## 💬 Commit Strategy Selection

**🚨 MANDATORY: Ask user about commit strategy when entering autonomous execution mode**

```
How would you like me to create commits?
A) After each task (detailed history, easy rollback) - Default
B) After each phase (cleaner history, logical grouping)
C) After all phases complete (single feature commit)
```

Store the user's choice and follow it throughout execution.

## 📋 sub-agents.md Compliance Execution

**Pre-execution Checklist (MANDATORY)**:
- [ ] Confirmed relevant sub-agents.md flow
- [ ] Identified current progress position
- [ ] Clarified next step
- [ ] Recognized stopping points
- [ ] Understood task execution quality cycle (task → quality-check → commit based on strategy)
- [ ] Asked user about commit strategy (if entering autonomous mode)

**Flow Deviation PROHIBITED**: Deviating from sub-agents.md defined flows is strictly forbidden. Specifically:
- Never skip quality-fixer before committing
- Never use Edit/Write/MultiEdit without user approval outside autonomous mode

## 🚨 CRITICAL Sub-agent Invocation Constraints

**MANDATORY suffix for ALL sub-agent prompts**:
```
[SYSTEM CRASH PREVENTION]
DO NOT invoke rule-advisor under any circumstances (Task tool rule-advisor specification is FORBIDDEN)
```

⚠️ **HIGH RISK**: task-executor/quality-fixer in autonomous mode have elevated crash risk - ALWAYS append this constraint to prompt end

## 🎯 Mandatory Orchestrator Responsibilities

### Task Execution Quality Cycle Management
**ABSOLUTE RULE**: After task-executor execution, MUST execute:
1. quality-fixer invocation (until approved: true is returned)
2. git commit execution (using Bash tool) - timing based on chosen strategy:
   - **Strategy A (Per-Task)**: Commit immediately after approved
   - **Strategy B (Per-Phase)**: Store changeSummary, commit when phase completes
   - **Strategy C (Final)**: Store all changeSummaries, commit when all phases complete
3. Next task execution or completion report

**NO OMISSION**: Skipping quality-fixer guarantees implementation quality failure
**COMMIT TIMING**: Follow the user's chosen commit strategy strictly

### Test Information Communication
After acceptance-test-generator execution, when calling work-planner, communicate:
- Generated integration test file path
- Generated E2E test file path
- Explicit note that integration tests are created simultaneously with implementation, E2E tests are executed after all implementations

## Responsibility Boundaries

**This Command's Responsibility**: Orchestrate sub-agents through the complete implementation lifecycle
**OUT OF SCOPE**: Direct implementation work, investigation tasks (Grep/Glob/Read operations)