# TaskManager Findings

## What is accurate
- `task_priority.py` calculates a task score and sorts tasks by that score.
- `task_manager.py` and `cli.py` are the main files for command handling and task operations.
- The CLI accepts arguments, passes them to `TaskManager`, and persistence is handled through task storage (JSON).

## What is incorrect or missing
- This codebase does not appear to use a `task_parser.py` in the main flow.
- The prompt's description of a parser validating conditions before calculation does not match the files observed.

## Important behavior in `task_priority.py`
- Score is based on priority weight and due date.
- Completed and review status reduce the score.
- Certain tags add a score boost.
- Recent updates also add a small boost.

## Key flow points
- `cli.py` uses `argparse` to collect command-line input.
- `TaskManager.create_task()` converts input into a `Task` and stores it.
- Invalid due dates are handled by a `try/except` block in `create_task()`.

## Practical modification question
- Consider whether you would change `calculate_task_score()` directly to adjust boosting logic, or keep score logic separate and add a dedicated filter for overdue/high-priority tasks.