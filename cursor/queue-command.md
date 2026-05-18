# Queue Command for Cursor

Use this as a Cursor command or prompt snippet.

```text
Create a queue-style workflow for this task:

$ARGUMENTS

Use the queue-coordinator skill.

Rules:
- Ask the publisher for a small first batch, not the full task list.
- For each item, dispatch a separate worker when possible.
- If separate workers are not available, process items sequentially but keep strict item boundaries.
- Continue requesting batches until the publisher returns done or the default item limit is reached.
- Summarize successes, failures, and next steps.
```
