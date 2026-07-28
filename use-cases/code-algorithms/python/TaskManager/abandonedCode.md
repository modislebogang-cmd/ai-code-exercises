# Abandoned Rule Implementation Notes

## Summary
A new business rule was implemented so that tasks overdue for more than 7 days are automatically marked as abandoned unless they are high priority or urgent.

## Files changed

### 1. models.py
Added a new task status:
- ABANDONED

Updated the overdue logic so abandoned tasks are not treated as active overdue work.

### 2. task_manager.py
Added:
- ABANDONMENT_THRESHOLD_DAYS = 7
- _should_mark_abandoned(task)
- _apply_abandonment_rule(task)

The rule now checks whether a task:
- is overdue for more than 7 days,
- is not already done or abandoned,
- and is not high priority or urgent.

The rule is applied when tasks are:
- listed,
- retrieved by details,
- and included in statistics.

### 3. cli.py
Updated the CLI so the new abandoned status can be used when filtering or updating task status.

### 4. tests/test_task_manager.py
Added regression tests to verify:
- an old overdue task becomes abandoned,
- a high-priority overdue task remains active,
- a recent task remains active.

## Verification
The change was verified by running:
```bash
C:/Users/LebZana/AppData/Local/Programs/Python/Python314/python.exe -m unittest discover -v tests
```
Result:
- 57 tests ran
- all passed
