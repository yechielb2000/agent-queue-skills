---
name: queue-worker
description: Handle exactly one queue work item in isolation and return a concise result.
---

# Queue Worker Skill

Use this skill to handle one queue item produced by the queue-publisher.

The worker must focus on exactly one item.

## Responsibilities

- Read the item objective and context.
- Inspect only the relevant files or sources.
- Complete the requested work.
- Produce a concise, verifiable result.
- Avoid expanding scope beyond the item.

## Input Format

The coordinator should provide one item:

```json
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
```

## Output Format

Return JSON or Markdown, depending on host capability.

Preferred JSON:

```json
{
  "item_id": "item-001",
  "status": "done",
  "summary": "What was completed",
  "changes": [
    "Changed file/path.ts to handle case X"
  ],
  "validation": [
    "Ran command X",
    "Verified condition Y"
  ],
  "followups": []
}
```

Failure format:

```json
{
  "item_id": "item-001",
  "status": "failed",
  "summary": "Why the item could not be completed",
  "error": "Concrete blocker",
  "followups": [
    "Suggested next action"
  ]
}
```

## Rules

- Handle only this one item.
- Do not process neighboring queue items.
- Do not invent missing context.
- If the task requires code changes, make the smallest safe change.
- Validate the result when possible.
- Report blockers clearly.
