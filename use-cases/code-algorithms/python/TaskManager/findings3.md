# Findings on the Task Domain Model

## Summary
The task management application models work as a set of tasks that can be ranked by importance. The core business idea is to help a user decide which task deserves attention first.

## Core domain concepts
- Task: a unit of work that can be created, updated, prioritized, and completed.
- Priority: a label indicating how important a task is, with values such as low, medium, high, and urgent.
- Priority score: a calculated number that reflects the task's overall importance or urgency.
- Due date: the deadline for completing the task.
- Overdue task: a task whose due date has passed and which is still not complete.
- Status: the current state of a task, such as todo, in progress, review, or done.
- Tag: a label used to add meaning to a task, such as blocker, critical, or urgent.
- Importance: the business value of paying attention to the task first.
- Urgency: the pressure created by time constraints or deadlines.
- Triage: the process of sorting tasks by urgency and importance.
- Ranking: the ordering of tasks from most important to least important.

## What the priority logic is modeling
The code in task_priority.py implements a lightweight prioritization engine. It combines several signals to decide how important a task appears:
- base priority level
- closeness to due date
- whether the task is completed or under review
- special tags such as blocker or critical
- whether the task was recently updated

## Business interpretation
This logic supports a practical business process: deciding which work should be handled first. In business terms, it is a task triage or work-queue ranking model.

## Why the functions are separated
- calculate_task_score(task): evaluates one task and gives it a score.
- sort_tasks_by_importance(tasks): ranks all tasks using those scores.
- get_top_priority_tasks(tasks, limit=5): returns the most important subset of the ranked list.

This separation keeps the logic clear and reusable. It is not only about reducing code lines; it is about giving each function a clear responsibility.

## Glossary
- Task
- Priority
- Priority score
- Due date
- Overdue task
- Status
- Tag
- Importance
- Urgency
- Triage
- Ranking
