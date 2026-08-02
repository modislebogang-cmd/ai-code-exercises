# Understanding the Task Scoring Algorithm

## 1. What the code does

The code converts each task into a numeric score, then sorts tasks by that score to decide which tasks are most important.

### Main functions
- `calculate_task_score(task)`
  - Computes a single importance score for one task.
- `sort_tasks_by_importance(tasks)`
  - Sorts all tasks from highest score to lowest.
- `get_top_priority_tasks(tasks, limit=5)`
  - Returns the top `limit` tasks after sorting.

---

## 2. Key sections of `calculate_task_score`

1. **Priority weight**
   - Maps task priority to a number:
     - `LOW` → `1`
     - `MEDIUM` → `2`
     - `HIGH` → `4`
     - `URGENT` → `6`
   - Multiplies that weight by `10` to create the base score.

2. **Due date factor**
   - If the task has a due date, it adds more score for tasks that are due sooner:
     - overdue: `+35`
     - due today: `+20`
     - due in 1–2 days: `+15`
     - due in 3–7 days: `+10`

3. **Status penalty**
   - If the task is already done, subtract `50`.
   - If the task is in review, subtract `15`.

4. **Tag boost**
   - If the task has any of these tags: `blocker`, `critical`, or `urgent`, add `8`.

5. **Recent update boost**
   - If the task was updated within the last day, add `5`.


## 3. Visual diagram of the score flow

```
Task object
    ├─ priority -> map to base weight -> *10 -> base score
    ├─ due_date -> compute days until due -> add due-date bonus
    ├─ status -> subtract penalty if DONE or REVIEW
    ├─ tags -> add +8 if special tag exists
    └─ updated_at -> add +5 if updated in the last day

Combined score -> return
```

Then:

```
tasks list
    ├─ calculate score for each task
    ├─ create list of (score, task)
    ├─ sort this list descending by score
    └─ return sorted tasks or top N tasks
```

---

## 4. Example execution with real values

Assume today is `2026-08-02`.

Task A:
- priority: `HIGH`
- due_date: `2026-08-03` (1 day away)
- status: `IN_PROGRESS`
- tags: `["feature"]`
- updated_at: `2026-08-02`

Score A:
- base priority: `4 * 10 = 40`
- due date bonus: `+15`
- status: `+0`
- tag boost: `+0`
- recent update: `+5`
- total = `60`

Task B:
- priority: `MEDIUM`
- due_date: `2026-07-30` (overdue)
- status: `REVIEW`
- tags: `["critical"]`
- updated_at: `2026-07-31`

Score B:
- base priority: `2 * 10 = 20`
- overdue bonus: `+35`
- review penalty: `-15`
- tag boost: `+8`
- recent update: `+0`
- total = `48`

Sorted order: Task A first, then Task B.

---

## 5. Core pattern being used

This is a **score-and-rank** pattern:
- first compute a score from several weighted criteria,
- then sort items by that score,
- then select the top items.

This is useful when many factors influence importance and you want a single ranking.

---

## 6. Important details to notice

- The input `task` is expected to have these attributes:
  - `priority`, `due_date`, `status`, `tags`, `updated_at`
- The code uses `sorted(task_scores, reverse=True)`.
  - If two tasks have equal score, Python may compare the task objects themselves as a secondary tie-breaker.
  - That tie-breaker is not intentional and can be unreliable if tasks are not comparable.
- The algorithm is not only sorting by priority; it uses a combined score with due date, status, tags, and recency.
- Because `datetime.now()` is called separately in due date and update calculations, those values are very close but technically two different timestamps.

---

## 7. What to remember

- `get_top_priority_tasks` returns the highest-scoring tasks, not necessarily the highest `priority` enum alone.
- `calculate_task_score` is the key helper: it translates task properties into a numeric value.
- The sort is descending, so bigger scores come first.
- Equal scores may need an explicit tie-breaker if you want stable ordering.

---

## 8. Insights and learning points

- The algorithm is a good example of combining multiple heuristics into one score. Each factor is weighted and then summed, which makes the final decision easy to compare.
- Small changes in weights can change the ranking significantly. For example, increasing the overdue bonus or reducing the done penalty will make certain tasks jump in importance.
- The code currently depends on how `task` is structured. If a task object is missing an expected field, the function may raise an error or silently give a low score.
- This kind of ranking function is easiest to understand when you think in terms of "why does this task deserve more points?"
- A real improvement would be to make tie-breaking explicit, for example by sorting on `(score, due_date, priority)` instead of relying on tuple comparison of task objects.
- The function is more stable if it uses a single reference time for all date calculations, rather than calling `datetime.now()` twice.
