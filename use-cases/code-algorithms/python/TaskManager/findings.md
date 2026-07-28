# Exercise Part 1: Understanding Project Structure

## Summary

This is a small Python CLI Task Manager project requiring Python 3.11 or higher.

## Validation of understanding

- The project is a CLI task manager that manages tasks by status: `todo`, `in_progress`, `review`, `done`.
- It is written in Python and uses the standard library only.
- The relevant project folder is `ai-code-exercises/use-cases/code-algorithms/python/TaskManager`.

## Key files and responsibilities

- `cli.py`: Primary application entry point. Implements commands such as `create`, `list`, `status`, `priority`, `due`, `tag`, `untag`, `show`, `delete`, and `stats`.
- `task_manager.py`: Core business logic. The `TaskManager` class handles task creation, updates, deletion, retrieval, sorting, and statistics.
- `models.py`: Defines task data structures: `Task`, `TaskPriority`, and `TaskStatus`. Includes task methods like `update()`, `mark_as_done()`, and `is_overdue()`.
- `storage.py`: JSON persistence layer using `tasks.json`. Handles loading, saving, and querying tasks.
- `task_parser.py`: Parses free-form task text with markers like `@tag`, `!priority`, and `#date`.
- `task_priority.py`: Computes an importance score for tasks and sorts them by priority.
- `task_list_merge.py`: Merges and resolves conflicts between two task lists, such as local and remote versions.
- `tests/`: Unit tests for task manager behavior, parsing, merging, and priority logic.
- `tasks.json`: Data store for saved tasks, not a configuration file.

## Technologies / libraries used

- Python standard library: `argparse`, `json`, `datetime`, `enum`, `uuid`, `re`, `unittest`.
- No external dependencies are required.

## Entry points

- Runtime entry point: `cli.py` (run with `python cli.py ...`).
- Programmatic entry point: `TaskManager` class in `task_manager.py`.

## Clarifications: product structure vs config files

- `tasks.json` is application data storage, not a configuration file.
- There is no `requirements.txt`, `pyproject.toml`, or other package metadata in the repository.
- The main repo support files are `README.md` and `.gitignore`.

## Suggested questions for the team

1. Is `tasks.json` intended as long-term storage, or will it be replaced by a database or sync backend?
2. What is the intended workflow for `task_list_merge.py`? Is a sync feature planned?
3. Should `task_parser.py` be exposed through the CLI or used by another interface?
4. Are there naming conventions or restrictions for tags, priorities, and statuses?
5. Is there an expected strategy for handling concurrent updates or multi-user conflict resolution?

## Exploration exercise

1. From the `TaskManager` folder, run:

```bash
python cli.py list
```

2. Create a new task:

```bash
python cli.py create "Write summary" -d "Review project design" -p 2 -u 2026-08-01 -t "review,team"
```

3. Verify the task appears in:

```bash
python cli.py list --status todo
```

4. Open `tasks.json` and confirm the new task entry matches the created task fields.

## Example `tasks.json` excerpt

```json
{
  "id": "a08e2e8d-59a4-4bf4-8bbd-66b9a8b0193f",
  "title": "Write summary",
  "description": "Review project design",
  "priority": 2,
  "status": "todo",
  "created_at": "2026-07-28T22:41:31.137150",
  "updated_at": "2026-07-28T22:41:31.137150",
  "due_date": "2026-08-01T00:00:00",
  "completed_at": null,
  "tags": ["review", "team"]
}
```

## Notes

- The file is saved as markdown in your current directory: `findings.md`.
