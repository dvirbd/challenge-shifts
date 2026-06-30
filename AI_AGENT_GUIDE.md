# AI Agent Guide — OCC Shift Calendar

This file explains the project for future AI agents or developers editing it.

## Project Purpose

This is a single-file Hebrew RTL web app for manually entering OCC shifts, opening individual shifts in Google Calendar event creation, and exporting all selected shifts as an `.ics` calendar file.

The app is for OCC shift entry only. Keep the guide and public/user-facing documentation generic unless a specific operational detail is technically required by the app itself.

The app is designed for a user who receives monthly shift schedules and wants to quickly mark shifts on a calendar grid, then import them into Google Calendar or another calendar app.

## Main Files

- `challenge-shifts.html` — the complete app. It contains HTML, CSS, JavaScript, and an embedded logo as a Data URL.
- `README.md` — public-facing GitHub README.
- `AI_AGENT_GUIDE.md` — this file, for future AI/code agents.

There is no build system, package manager, backend, database, framework, or external dependency.

## Deployment

For GitHub Pages, copy or rename:

```text
challenge-shifts.html → index.html
```

Then publish from the repository root via GitHub Pages.

The logo is already embedded in the HTML as a `data:image/png;base64,...` URL, so no separate image file is required for deployment.

## Shift Types

The app currently supports exactly one shift per date.

| Internal Type | Hebrew Label | Time |
|---|---|---|
| `morning` | `בוקר` | 06:00–14:30 |
| `afternoon` | `צהריים` | 14:00–22:30 |
| `night` | `לילה` | 22:00–06:30 next day |
| `mid` | `אמצע` | 10:00–18:30 |

Important code locations:

- `CYCLE_SINGLE` controls the normal click cycle.
- `SHIFT_LABELS` controls Hebrew labels used in UI and ICS summaries.
- `INFO` controls icons and CSS classes for each shift type.
- `SHIFT_TIMES` controls actual event times for both Google Calendar links and ICS export.

When adding or renaming a shift type, update all four places.

## Click / Touch Behavior

Current behavior:

- Single click on an empty/regular day cycles:

```text
Empty → Morning → Afternoon → Night → Empty
```

- Single click on a `mid` shift clears it.
- Long press on an empty day creates a `mid` shift.
- Long press on any existing shift clears it.

Important code locations:

- `bindDayCell(cell, year, month, day)` wires mouse/touch events.
- `cycleSingle(cell, year, month, day)` handles normal clicks.
- `cycleLong(cell, year, month, day)` handles long press behavior.
- `setShift(cell, y, m, d, type)` writes the shift state and updates the UI.

In the selected-shift chips list, each chip is clickable and opens Google Calendar event creation for that specific shift. The inner `×` button deletes the shift and must call `stopPropagation()` so it does not open the link.

Important UX rule: the `×` delete button is absolutely positioned in the top-right corner of every chip. When deleting from the chips list, call `setShift(..., false)` so the grid scroll-lock does not move the chips list. The chips grid uses `auto-fill` rather than `auto-fit`, and `chipsMinHeight` keeps the list from collapsing while shifts remain selected. This lets the user repeatedly tap the same top-right `×` position to delete many shifts without the button sliding away under their finger, and prevents a disappearing lower row from exposing a Google Calendar link under the tap point.

Avoid reintroducing double-click unless explicitly requested. Double-click previously made normal shift entry feel slow because click handling had to wait to distinguish single vs. double click.

## State Model

The global array is:

```js
let shifts = [];
```

Each shift object has this shape:

```js
{
  year: 2026,
  month: 7,
  day: 21,
  dateKey: "2026-07-21",
  type: "night"
}
```

`dateKey` is used to avoid ambiguity when the displayed grid includes days from the previous or next month.

## Calendar Grid

The grid shows full weeks, not only the selected month.

This means:

- At the beginning of the selected month, days from the previous month may appear.
- At the end of the selected month, days from the next month may appear.
- Adjacent-month cells receive the `.out` CSS class and show a small month/year hint.

Important code locations:

- `renderGrid()` calculates the first visible Sunday and last visible Saturday.
- `shiftForDate(y, m, d)` finds the shift for an exact date.
- `dateKey(y, m, d)` creates stable keys such as `2026-07-21`.

## Preventing Layout Jumps

The list of selected shift chips appears above the calendar grid. When the list grows to multiple rows, it can push the calendar down and cause accidental clicks.

Current solution:

- The chip list is allowed to grow normally.
- `withGridScrollLock(fn)` remembers the calendar grid's screen position before updating chips.
- After updating chips, it adjusts `window.scrollBy()` so the grid stays visually in the same place.
- `withGridScrollLock(fn)` dynamically adds only the small amount of temporary body padding needed if the user is near the bottom of the page and the browser has no room to scroll-compensate.
- `withGridScrollLock(fn)` corrects scroll immediately and again after layout/paint via `requestAnimationFrame`/short timeout because chip wrapping may settle one frame later on mobile browsers.

Important code location:

- `withGridScrollLock(fn)`
- `setShift(...)` calls `withGridScrollLock(renderChips)`

Do not replace this with fixed-height chip scrolling unless requested. The current solution keeps all selected shifts visible while avoiding accidental grid jumps.

## ICS Export

`doExport()` creates the calendar file locally in the browser.

Current ICS details:

- Event summary format:

```text
✈️🗺️ FOO - בוקר
✈️🗺️ FOO - צהריים
✈️🗺️ FOO - לילה
✈️🗺️ FOO - אמצע
```

- Times are exported in UTC `Z` format via `Date(...).toISOString()`.
- Location is exported as:

```text
[configured event location]
```

This intentionally matches the user's preferred Google Calendar export style. Do not add `GEO`, `URL`, or `DESCRIPTION` map fields unless explicitly requested; this was tried and reverted.

Important code locations:

- `doExport()`
- `SHIFT_TIMES`
- `eventDataForShift(...)`
- `fmtZ(...)`
- `esc(...)`

## Google Calendar Links

Each selected shift card in the chips list opens a Google Calendar event creation URL:

```text
https://calendar.google.com/calendar/render?action=TEMPLATE&text=<TITLE>&dates=<START>/<END>&location=<LOCATION>&ctz=Asia/Jerusalem
```

Important code locations:

- `eventDataForShift(...)` builds shared event metadata.
- `googleCalendarUrlForShift(...)` builds the URL.
- `renderChips()` attaches the click/keyboard handlers to each chip.
- `.chip-del` is the inner delete button; deleting must not open Google Calendar.

## UI / Styling Notes

The app is Hebrew RTL:

```html
<html lang="he" dir="rtl">
```

Main styling uses CSS custom properties for dark/light mode.

Relevant CSS classes:

- `.chips` — selected shifts list.
- `.chip.*` — selected shift chips.
- `.chip-del` — delete button inside a selected shift chip.
- `.day-grid` — calendar grid.
- `.dc` — day cell.
- `.dc.out` — day from adjacent month.
- `.dc.sm`, `.dc.se`, `.dc.sn`, `.dc.sx` — morning/afternoon/night/mid colors.

## Safe Editing Checklist

After editing:

1. Open `challenge-shifts.html` in a browser.
2. Click `פתיחת לוח להזנת משמרות`.
3. Test normal click cycle:

```text
Empty → Morning → Afternoon → Night → Empty
```

4. Test long press:

```text
Empty → Mid
Existing shift → Empty
```

5. Add many shifts and verify the grid does not jump unexpectedly.
6. Click a selected-shift chip and verify the Google Calendar URL includes `text`, `dates`, `location`, and `ctz` — no `details` parameter.
7. Click the `×` inside a selected-shift chip and verify it deletes the shift without opening Google Calendar.
8. Export ICS and check:
   - Summary format is correct.
   - Night shift ends the next day.
   - Times have `Z` suffix.
   - Location is correct.

## Known Design Decisions

- Single-file app for easy sharing and GitHub Pages hosting.
- No automatic OCR/AI recognition.
- No external API calls.
- No localStorage persistence currently; refreshing clears selected shifts.
- The app is optimized for fast manual entry rather than complex scheduling logic.
