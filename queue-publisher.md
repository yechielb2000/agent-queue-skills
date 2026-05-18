---
name: queue-publisher
description: Split a broad task into small atomic queue work items and return them in small batches.
---

# Queue Publisher Skill

Use this skill when a broad task should be decomposed into many independent work items.

The publisher does not solve the work items. It only discovers and emits them.

## Responsibilities

- Understand the user's broad task.
- Discover independent work items.
- Emit a small batch quickly instead of waiting for all possible items.
- Keep each item self-contained.
- Avoid duplicate items.
- Stop when no more items remain.

## Batch Behavior

Return work items in batches of 3-10 by default.

Prefer fast partial output over exhaustive delayed output.

If the host supports repeated invocation, the coordinator may ask:

```text
Give me the next batch for task <task_id>.
```

When no more items remain, return:

```json
{
  "status": "done",
  "items": []
}
```

## Output Format

Return JSON only:

```json
{
  "status": "items",
  "task_id": "short-stable-id",
  "batch_id": "batch-001",
  "items": [
    {
      "item_id": "item-001",
      "title": "Short item title",
      "objective": "What the worker should accomplish",
      "context": "Specific details needed by the worker",
      "files": ["optional/path/to/file.ts"],
      "acceptance_criteria": [
        "Concrete success condition 1",
        "Concrete success condition 2"
      ],
      "priority": "medium",
      "dedupe_key": "stable-unique-key"
    }
  ]
}
```

## Item Rules

Each item must be:

- Atomic: one worker can complete it independently.
- Specific: include enough context to start immediately.
- Verifiable: include acceptance criteria.
- Bounded: avoid vague tasks like "refactor everything".
- Non-overlapping: avoid two workers editing the same thing unless explicitly needed.

## Do Not

- Do not solve the items.
- Do not return a giant list if partial batches are possible.
- Do not emit duplicate work.
- Do not create items that require hidden context from previous batches.
- Do not assume the worker has access to the publisher's full reasoning.
