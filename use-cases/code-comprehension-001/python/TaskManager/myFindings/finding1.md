# Finding 1: Task Status Update Flow

## What this component does
This part updates a task's status in the app. When the user requests a new status, the program finds the task, changes its state, and saves the update so it is not lost.

## Execution flow
1. The CLI receives a status command.
2. The command is passed to TaskManager.
3. TaskManager converts the incoming status string into a TaskStatus enum value.
4. If the new status is DONE, the task is marked as completed and saved.
5. If the new status is something else, the storage layer updates the task and saves it.

## How the files interact
- cli.py: entry point for user commands.
- task_manager.py: contains the business logic for task updates.
- storage.py: saves and loads tasks from disk.
- models.py: defines the task structure and status behavior.

## How persistence works without a database
The app uses a JSON file named tasks.json instead of a database. The storage layer loads tasks into memory when the app starts, updates them while the app runs, and writes them back to disk when save() is called.

## Mental model
Think of the app as a small filing system:
- TaskManager decides what should change.
- TaskStorage writes the changes to disk.
- Task objects hold the actual task data.
