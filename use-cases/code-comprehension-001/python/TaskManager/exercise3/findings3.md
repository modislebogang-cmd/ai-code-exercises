# Task Merge Logic Findings

## What the code does
- `merge_task_lists` merges two dictionaries of tasks by task ID.
- It builds the union of all IDs and handles three cases:
  - exists only locally → add to merged and create remote
  - exists only remotely → add to merged and create local
  - exists in both → resolve conflict and possibly update local, remote, or both

## Key decision points
- `remote_task.updated_at > local_task.updated_at` decides which source provides title, description, priority, and due date.
- Status merging is handled separately and uses special rules for `TaskStatus.DONE`.
- Tags are merged as a union, and if the new tag set differs from either source, that source is marked for update.
- `merged_task.updated_at` is set to the later of the two timestamps at the end.

## Important behavior details
- The code does not sort task IDs; it iterates the union set in arbitrary order.
- A same-age timestamp comparison gives priority to local values and marks remote for update.
- If one side is `DONE` and the other is not, completion status wins over timestamp recency.
- Tag merging can cause both sources to be updated even if only one side added a tag.

## Potential edge cases / bugs
- `completed_at` may remain stale if a task goes from `DONE` back to another status.
- The union of task IDs is not deterministic unless task IDs are sorted before iteration.
- The code assumes `tags` and `completed_at` exist and are comparable on both task objects.
- The special `DONE` rule can force an update to the younger source even when that source has more recent non-status data.

## Learning scenarios
1. Remote newer, same status, different tags
   - remote wins for fields, tags are unioned, both sources may need updates
2. Local `DONE`, remote newer not done
   - merged task remains `DONE`, remote gets updated, local may still be updated for other fields
3. Remote `DONE`, local newer not done
   - `DONE` wins despite older remote timestamp, local needs update to finished status

## Summary
- The merge is a combination of "newest-value wins" for normal fields and "completed wins" for status.
- Status and tags are resolved through separate rules, so the result is not driven solely by the latest timestamp.
- If you want this code to be easier to understand, break conflict resolution into smaller helper functions for timestamp fields, status, and tags.
