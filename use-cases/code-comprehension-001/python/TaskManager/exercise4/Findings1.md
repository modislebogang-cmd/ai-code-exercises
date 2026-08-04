# Findings for `get_next_weekday`

## Summary
The function `get_next_weekday(current_date, weekday)` returns the next occurrence of a specified weekday after a given date. If the current date is already the requested weekday, it returns the same weekday in the following week.

## Parameters
- `current_date` (`datetime`): The starting date from which the next weekday is calculated.
- `weekday` (`int`): The target weekday, where Monday is `0` and Sunday is `6`.

## Return Value
- Returns a `datetime` object representing the next occurrence of the requested weekday.

## Exceptions / Errors
- `AttributeError`: If `current_date` does not have a `weekday()` method.
- `TypeError`: If `weekday` is not an integer.
- `ValueError`: If `weekday` is outside the valid range `0` to `6`.

## Example Usage
```python
from datetime import datetime

next_monday = get_next_weekday(datetime(2025, 5, 14), 0)
# If 2025-05-14 is a Wednesday, this returns 2025-05-19
```

## Notes and Edge Cases
- `current_date.weekday()` returns `0` for Monday through `6` for Sunday.
- If the target weekday has already occurred in the current week, the function adds 7 days to return the next week's occurrence.
- If `current_date` already falls on the target weekday, the function also returns the same weekday one week later.
