---
name: queue-coordinator
description: Coordinate a queue-style workflow using publisher batches and isolated workers.
---

# Queue Coordinator Skill

Use this skill when the user asks to create a queue, fan out work, process many results, or dispatch subagents/workers per result.

This is a skills-only queue protocol. It does not require a physical queue service.

## Core Workflow

1. Restate the user's task as a queue task.
2. Invoke or follow the queue-publisher skill to get the first batch of items.
3. For each item, invoke or follow the queue-worker skill in a separate worker context when supported.
4. Collect worker outputs.
5. Ask the publisher for the next batch.
6. Repeat until the publisher returns `status: done`.
7. Summarize all worker results.

## Near-Streaming Behavior

Do not wait for the publisher to discover every possible item.

Use this loop:

```text
publisher -> small batch
coordinator -> workers for that batch
publisher -> next batch
coordinator -> workers for that batch
repeat
```

## Default Limits

Unless the user specifies otherwise:

- Batch size: 5 items.
- Max total items: 25.
- Max worker retries: 1.
- Stop on destructive or ambiguous changes.
- Summarize after each batch if the host cannot run workers concurrently.

## Coordinator State

Track state visibly in the conversation or in a short local scratch file if the host supports files.

Use this structure:

```json
{
  "task_id": "short-stable-id",
  "user_task": "Original user task",
  "batches_completed": 0,
  "items": {
    "pending": [],
    "running": [],
    "done": [],
    "failed": []
  }
}
```

## Dispatch Instructions

For each item, tell the worker:

```text
Use the queue-worker skill.
Handle exactly this queue item and nothing else:
<item json>
```

If the host supports subagents, dispatch each worker as a separate subagent.

If the host does not support subagents, process items sequentially and preserve item boundaries.

## Final Summary Format

At the end, report:

```text
Queue task complete.

Processed: N
Succeeded: N
Failed: N
Skipped: N

Key results:
- ...

Failures/blockers:
- ...

Recommended next steps:
- ...
```

## Do Not

- Do not let one worker handle multiple unrelated items.
- Do not allow workers to depend on hidden state from the publisher.
- Do not continue indefinitely.
- Do not make risky code changes without clear acceptance criteria.
- Do not claim true streaming or durable queue semantics unless an actual runtime provides them.
