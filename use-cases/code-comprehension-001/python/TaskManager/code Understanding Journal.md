## Main Components Involved in Task Creation and Updates

### EXERCISE 2 PART 1 Findings
See [myFindings/finding1.md](myFindings/finding1.md) for the summary of this flow.

### Main files involved
- [cli.py](cli.py) — receives the user command from the terminal.
- [task_manager.py](task_manager.py) — contains the main logic for creating and updating tasks.
- [storage.py](storage.py) — saves and loads task data to and from the JSON file.
- [models.py](models.py) — defines the Task object and status/priority enums.

### Key code example
```python
def update_task_status(self, task_id, new_status_value):
    new_status = TaskStatus(new_status_value)
    if new_status == TaskStatus.DONE:
        task = self.storage.get_task(task_id)
        if task:
            task.mark_as_done()
            self.storage.save()
            return True
    else:
        return self.storage.update_task(task_id, status=new_status)
```

### What this shows
- Task creation and updates flow through the manager layer.
- The storage layer is responsible for persistence.
- The app uses a JSON file instead of a database.
- The task object holds the actual task data and update behavior.

### Execution flow when a task is created or updated
1. The user runs a command in the CLI.
2. The CLI passes the request to TaskManager.
3. TaskManager creates a new Task object for a create action or updates an existing task for an update action.
4. The task data is sent to the storage layer.
5. Storage saves the updated task list to the JSON file.
6. On the next startup, the app loads those saved tasks back into memory.

### How data is stored and retrieved
- When the app starts, TaskStorage loads tasks from a file called tasks.json.
- The tasks are kept in memory inside a dictionary named self.tasks.
- When a task is created or updated, the in-memory object changes.
- The save() method writes the current task list to the JSON file.
- The load() method reads the JSON file and rebuilds the Task objects when the app starts again.
- This means the app uses a simple file-based storage system rather than a database.

### Interesting design patterns
- Separation of concerns: the CLI, manager, storage, and model layers each have a different role.
- Encapsulation: TaskManager and TaskStorage hide the details of how updates and persistence work.
- Simple repository-like pattern: TaskStorage acts like a small repository for reading and writing tasks.
- Enum-based modeling: TaskStatus and TaskPriority use enums to make valid states explicit.

### EXERCISE 2 PART 2
### Initial understanding vs. AI findings
- Initial understanding correctly identified `task_priority.py`, `task_manager.py`, and `cli.py` as key files.
- The AI findings confirmed score calculation and sorting happen in `task_priority.py`, and that task creation persists through JSON storage.
- The initial belief in a `task_parser.py` validation step was not supported by this version of the codebase.
- The prompt's mention of "boost your score" is clarified as tag-based boosts and recent-update boosts inside `calculate_task_score()`.
- The AI findings also highlighted the precise flow for invalid due dates and the fact that `TaskManager.create_task()` handles date parsing and task creation directly.


### EXERCISE 2 PART 3

### Data Flow Diagram
```
CLI (cli.py)
  |-- parse args --> TaskManager.update_task_status(task_id, status)

TaskManager (task_manager.py)
  |-- convert status --> TaskStatus enum
  |-- get task --> TaskStorage.get_task(task_id)
  |-- if done --> task.mark_as_done()
  |-- save changes --> TaskStorage.save()

Task (models.py)
  |-- status = DONE
  |-- completed_at = now
  |-- updated_at = now

TaskStorage (storage.py)
  |-- save() writes tasks.json using TaskEncoder
  |-- load() reads tasks.json using TaskDecoder
```

### State changes during task completion
- `TaskStorage.tasks` holds the live task object in memory.
- `TaskManager.update_task_status()` retrieves the task and triggers state change.
- `Task.mark_as_done()` mutates the `Task` object:
  - `status` changes from `TODO`/`IN_PROGRESS`/`REVIEW` to `DONE`.
  - `completed_at` is set to the current timestamp.
  - `updated_at` is also set to the current timestamp.
- `TaskStorage.save()` then serializes the updated object and writes it to `tasks.json`, persisting the new state.

### Potential points of failure
- If `task_id` is missing or invalid, `TaskStorage.get_task()` returns `None` and the update does nothing.
- If the status value is invalid, `TaskStatus(new_status_value)` may raise a `ValueError` before the task can be updated.
- If `TaskStorage.save()` encounters an I/O error, the data may not be written to `tasks.json` even though the object changed in memory.
- If `tasks.json` is corrupted or contains malformed dates, `TaskDecoder` may fail while loading tasks.
- Concurrency issues may occur because there is no file locking, so simultaneous writes could overwrite or lose updates.

### How persistence works
- `TaskStorage.save()` serializes the in-memory task objects into JSON using `TaskEncoder`.
- `TaskEncoder` converts `Task` fields to serializable values, including enum values and datetime fields as ISO-format strings.
- The serialized list of tasks is written to `tasks.json`.
- On startup, `TaskStorage.load()` reads `tasks.json` and uses `TaskDecoder` to rebuild `Task` objects.
- `TaskDecoder` converts stored enum values back into `TaskPriority` and `TaskStatus`, and converts ISO date strings back into `datetime` objects.
- This means the updated `DONE` state, `completed_at`, and `updated_at` values are persisted across runs.

This diagram shows the main flow of task completion from CLI input through manager logic, model mutation, and JSON persistence.


