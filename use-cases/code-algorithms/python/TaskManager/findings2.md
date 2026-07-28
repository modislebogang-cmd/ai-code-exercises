# Findings on Task Export to CSV

## Summary
The current TaskManager codebase does not yet implement CSV export. The relevant feature would likely be added across the CLI, task manager, storage, and model layers.

### Latest finding
The first correct place to start is the CLI entrypoint in cli.py, because that is where user commands are parsed and routed. For this feature, I would add an `export` subcommand there, then connect it to task_manager.py for the export logic and storage.py for retrieving the task data.

## Files most likely involved
- cli.py
  - User-facing command parsing and CLI entrypoint.
  - This is where a new `export` command would be added.
- task_manager.py
  - Core business logic for managing tasks.
  - A method to gather tasks and prepare them for CSV export would fit here.
- storage.py
  - Loads, saves, and retrieves task data.
  - This is the likely source for the list of tasks that would be exported.
- models.py
  - Defines the task structure and fields such as title, description, status, priority, due date, tags, and timestamps.
- tests/
  - Best place to add regression tests for the new export behavior.

## Search strategy improvements
Instead of searching only for terms like `csv`, `download`, or `to csv`, the more effective approach is to trace the existing task data flow:
- `argparse`
- `subparsers`
- `list_tasks`
- `get_all_tasks`
- `TaskStorage`
- `json`
- `format`
- `print`
- `task.id`
- `task.title`

These terms help identify where commands are wired and where task data is collected.

## Likely feature flow
1. User invokes a new CLI command such as `export`.
2. The CLI parses the command and calls into the task manager.
3. The task manager retrieves tasks from storage.
4. The task data is converted into CSV rows.
5. The CSV content is written to a file or printed to stdout.

## Questions to ask while exploring
- Where do user commands enter the application?
- What existing command already returns a collection of tasks?
- Where are tasks fetched from storage?
- Which task fields should appear in the CSV?
- Should the feature create a file or print output?

## Patterns to look for
- Command parser blocks in cli.py
- Manager methods that call storage methods like `get_all_tasks`
- Model fields that map cleanly to CSV columns
- Tests describing expected output behavior

## Small challenge
Identify which file you would edit first to add an `export` command (answer = cli.py)
and name the two main functions or methods that would be involved in the full flow from command to CSV output.(answer = cli.py and task_manager.py)
