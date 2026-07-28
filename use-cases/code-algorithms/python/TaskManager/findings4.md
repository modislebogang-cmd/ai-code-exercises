# Findings for the Abandoned-After-7-Days Rule

## Summary
A new business rule should be implemented so that tasks overdue for more than 7 days are automatically marked as abandoned unless they are high priority.

## Files likely to change
- models.py
  - Add a new task status such as ABANDONED so the system can represent this state explicitly.
- task_manager.py
  - Implement the business rule in the task management layer.
- cli.py
  - Allow the new status to be used through the command-line interface.
- tests/
  - Add tests for the new behavior before and after implementation.

## Proposed rule
A task should be marked as abandoned when:
- it is overdue for more than 7 days,
- it is not already done,
- and it is not high priority or urgent.

## Implementation plan
1. Add tests for the rule.
2. Extend the task status enum with ABANDONED.
3. Add a helper method in task_manager.py to evaluate and apply the rule.
4. Call the helper when tasks are listed, viewed, or updated.
5. Update the CLI so the new status is recognized.

## Why this matters to the business
This rule helps keep the task list focused on active work and prevents old unresolved tasks from remaining visible as if they still need attention. It improves clarity and supports better task triage.

## Questions to ask before implementation
- Should “high priority” include only HIGH or also URGENT?
- Should abandoned tasks remain visible in the main list or be hidden from active work?
- Should the rule apply immediately when a task becomes overdue, or only when it is viewed?
- Should abandoned tasks remain in history for audit purposes?
- What should happen if a task is later updated after being abandoned?
