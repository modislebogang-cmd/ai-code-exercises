# Code Understanding Journal

## Quick Markdown Cheat Sheet

### Headings

```md
# Heading 1
## Heading 2
### Heading 3
```

### Bold and Italic

```md
**bold**
*italic*
***bold italic***
```

### Lists

```md
- Bullet item
- Another bullet item

1. Numbered item
2. Another numbered item
```

### Links and Images

```md
[Link text](https://example.com)
![Alt text](image.png)
```

### Code Blocks

```md
```python
print("Hello, world!")
```
```

### Blockquotes

```md
> This is a quote.
```

### Horizontal Rule

```md
---
```

### Tables

```md
| Column 1 | Column 2 |
|----------|----------|
| Value 1  | Value 2  |
```

## Main Components Involved in Task Creation and Updates

### Findings note
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
