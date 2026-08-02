# Findings

- `sort_tasks_by_importance` sorts tasks by score from highest to lowest.
- If two tasks have the same score, Python compares the task objects themselves as a tie-breaker.
- That tie-breaker behavior can be unreliable if the task objects are not directly comparable.
- The code does not just sort by `priority` alone; it sorts by a combined computed score that includes due date, status, tags, and recency.
