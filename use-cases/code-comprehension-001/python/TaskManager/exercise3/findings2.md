# Task Parser Findings

## Summary
Parsed the free-form task parser in `task_parser.py` and confirmed behavior from the unit tests.

## Intent
The parser converts a single text string into a structured `Task` object by extracting:
- task title
- priority
- due date
- tags

Supported inline markers:
- `!1`..`!4` or `!low`/`!medium`/`!high`/`!urgent` for priority
- `@tag` for tags
- `#today`, `#tomorrow`, `#next_week`, weekday names, or `YYYY-MM-DD` for due date

## Behavior
- Uses default priority `TaskPriority.MEDIUM` when no priority marker is present
- Uses first valid priority marker found
- Uses first valid date marker found
- Removes markers from the title and normalizes whitespace
- Ignores unrecognized markers instead of raising errors

## Better names
- `parse_task_from_text` → `parse_task_line` or `parse_task_entry`
- `get_next_weekday` → `get_next_occurrence_of_weekday`
- `text` → `raw_text`
- `title` → `task_title`
- `priority` → `parsed_priority`
- `due_date` → `parsed_due_date`
- `tags` → `parsed_tags`

## Patterns used
- regex-based token extraction
- default initialization
- keyword-to-enum mapping
- whitespace cleanup
- helper function for date calculation

## Validation questions
1. Where is this parser called in the app, and what input source supplies the text?
2. Does the app expect markers to be stripped from the final `Task.title`?
3. Should multiple date markers be supported or should only the first valid one be used?
4. How should invalid markers like `!5` or `#holiday` be handled in the larger app?

## Safe experiments
- Run `parse_task_from_text("Buy milk @shopping !high #tomorrow")`
- Run `parse_task_from_text("Buy milk !5")`
- Run `parse_task_from_text("Buy milk #holiday")`
- Run the existing parser tests with `python -m unittest use-cases/code-algorithms/python/TaskManager/tests/test_task_parser.py`
