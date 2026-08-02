# Findings: Task Completion Data Flow

## Summary
This document captures the end-to-end data flow and state behavior for the "mark task as done" functionality.

## Data flow (input → output)
- CLI: `cli.py` parses the `status` command and passes `task_id` + new status to `TaskManager.update_task_status()`.
- Manager: `TaskManager.update_task_status()` converts the status to `TaskStatus`. For `done`, it fetches the `Task` via `TaskStorage.get_task()`, calls `task.mark_as_done()`, then persists via `TaskStorage.save()`.
- Model: `Task.mark_as_done()` updates `status`, sets `completed_at`, and updates `updated_at`.
- Storage: `TaskStorage.save()` serializes tasks using `TaskEncoder` (datetimes → ISO strings) and writes `tasks.json`.

## Where state is managed
- In-memory: `TaskStorage.tasks` dictionary holds live `Task` objects.
- Object-level: `Task` instances are mutated via `update()` and `mark_as_done()`.
- Persistent: `tasks.json` is written/read by `TaskStorage.save()` and `TaskStorage.load()`.

## Transformations at each step
- CLI strings/args → Python types by `argparse`.
- Status input → `TaskStatus(...)` enum conversion.
- `Task` → dict with ISO datetimes via `TaskEncoder` for JSON.
- JSON → `Task` via `TaskDecoder` (ISO → `datetime`).

## Potential failure points
- Missing task id: `get_task()` returns `None` and update fails.
- Invalid status value: `TaskStatus()` may raise `ValueError`.
- I/O errors: `load()`/`save()` catch and print exceptions; writes may silently fail.
- Partial persistence: object mutated but `save()` fails, causing lost durability.
- Corrupted JSON or unexpected datetime format: `TaskDecoder` may raise on `fromisoformat()`.
- Concurrency: no locking; simultaneous processes may overwrite `tasks.json`.

## Debugging checklist
- Run the CLI `status` command and observe output.
- Inspect `TaskStorage.tasks[task_id]` in a REPL or with debug prints after the update.
- Open `tasks.json` to confirm `status` and `completed_at` timestamp.
- Add logging around `TaskManager.update_task_status()` and `TaskStorage.save()` to capture exceptions.
- Add a unit test verifying `mark_as_done()` sets `completed_at` and that `save()` writes it.

## Suggested improvements
- Make `TaskStorage.save()` return success/failure or raise, so callers can act on failures.
- Use atomic file writes (write temp file then rename) to avoid partial writes.
- Validate enum conversions and return friendly errors for invalid inputs.
- Add file locking or migrate to a lightweight DB (SQLite) for concurrent access.
- Add tests for persistence failure modes (mock `save()` to raise).

## Next steps
- Add an optional unit test to assert persistence of `completed_at` after `mark_as_done()`.
- Consider a small patch to surface `save()` failures to the CLI with clear error messages.
