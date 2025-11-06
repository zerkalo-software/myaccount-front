# Quick Start Guide for AI Development

This guide walks you through setting up the AI Coding Project Boilerplate and implementing your first feature. Let's skip the complex stuff for now and just get it running.


## Launch Claude Code and Initial Setup

Launch Claude Code in your project directory.

```bash
claude
```

Once launched, let's set up project-specific information. This is a crucial step for the AI to understand your project.

Run the following custom slash command inside Claude Code.

```bash
/project-inject
```

You'll be guided through an interactive dialog to clarify your project information. Feel free to answer casually - you can change this information later.
Once you finish answering, your project context will be saved to `docs/rules/project-context.md`. This enables the AI to understand your project's purpose and generate more appropriate code.

After reviewing the file and confirming it looks good, complete this step with the following command.

```bash
/sync-rules
```

## Let's Implement Your First Feature

Now let's create your first feature. Specify it using the following command in Claude Code.

```bash
/implement Create a simple API that returns "Hello"
```

The `/implement` command guides you through the entire workflow from design to implementation.

First, it analyzes your requirements to determine the feature scale. It might say something like "This is a medium-scale feature requiring about 3 files." Based on the scale, it creates necessary design documents.

For small features, it creates just a simple work plan. For large features, it creates a complete flow from PRD (Product Requirements Document) to Design Doc (Technical Design Document). After each design document is created, an automatic review is performed.

Once the design is complete, the AI will ask "Here's the design I've created. Could you review it?" Read through it and request changes if needed, or approve it if it looks good.
Depending on the Claude Code model you're using, it may automatically proceed to the next design document if your review is positive. At some point it will ask for your approval, so you can provide batch feedback then.

After design approval, integration test skeletons and a work plan are created. Once you review the implementation steps (requesting changes if needed) and approve, the actual implementation begins.

The AI will ask you about your preferred commit strategy:
- **A) Per-Task** (default): One commit per task - detailed history, easy rollback
- **B) Per-Phase**: One commit per phase - cleaner history, logical grouping
- **C) Final**: One commit for entire feature - single commit

After choosing, the AI breaks down the work plan into task units and autonomously implements each using a TDD approach. After each task, it performs a defined 6-step quality check, fixes any errors, and creates commits based on your chosen strategy. You can simply watch the progress.

When all tasks are complete, it will report "Implementation complete." Check `git log` to see your commits based on the chosen strategy.

## Development Philosophy of This Boilerplate

Let me explain the thinking behind this boilerplate.

To maximize throughput with AI assistance, it's crucial to minimize human intervention. However, achieving high execution accuracy with AI requires careful context management—providing the right information at the right time. Without proper systems, humans need to guide the implementation process, which prevents throughput maximization.

This boilerplate solves this through systematic approaches: selecting appropriate context (rules, requirements, specifications) for each phase and limiting unnecessary information during task execution.

The workflow is: create design documents, review them with users to align understanding, use those documents as context for planning and task creation. Tasks are executed by sub-agents with dedicated contexts, which removes unnecessary information and stabilizes implementation. This system helps maintain quality even for projects too large to fit within a single coding agent's context window (Claude Opus 4.1 supports 200K tokens, Sonnet 4 beta supports up to 1M tokens, but the basic limit is 200K tokens).

## Troubleshooting

If things aren't working, check the following.

**If implementation stops midway**
Describe the current state to Claude Code and ask it to resume, for example: "You've completed up to the Design Doc creation, so please continue from there and complete the implementation."