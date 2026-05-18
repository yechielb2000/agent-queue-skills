# Agent Queue Skills

A lightweight, skills-only queue protocol for agent hosts such as Cursor, Claude Code, VS Code agents, or terminal-based coding agents.

This version does not require MCP, Redis, SQLite, or a dedicated queue service.

It uses three skills:

- `queue-coordinator.md` - orchestrates the whole workflow.
- `queue-publisher.md` - breaks a broad task into small work items, preferably in batches.
- `queue-worker.md` - handles exactly one work item in isolation.

## Intended Flow

```text
User asks for a queue-style task
  -> Coordinator asks Publisher for first batch
  -> Publisher returns small atomic work items
  -> Coordinator dispatches one Worker per item
  -> Coordinator asks Publisher for next batch
  -> Repeat until Publisher says DONE
  -> Coordinator summarizes all Worker outputs
```

## Important Limitation

Skills alone do not provide true event streaming, durable queues, retries, or real concurrency across every host.

This pack uses near-streaming by batching:

```text
Publisher returns 3-10 items quickly
Coordinator dispatches workers
Publisher continues with next batch
```
