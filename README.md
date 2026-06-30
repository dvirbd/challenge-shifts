# Shift Calendar Exporter

A simple Hebrew web app for quickly entering work shifts and adding them to a calendar.

The app is designed for manual shift entry: choose a month, tap the relevant days, then either create individual Google Calendar events or export all selected shifts as an ICS file.

## Features

- Hebrew RTL interface
- Fast manual shift selection
- Full-week calendar view, including days from the previous or next month when needed
- Four shift types: morning, afternoon, night, and mid
- One shift per day
- Selected-shift cards above the calendar
- Click a selected shift card to open Google Calendar event creation for that shift
- Delete a selected shift using the `×` button on the card
- Stable `×` position for repeated deletion from the same tap point
- Stable card layout: weekday/date always appears under the shift name
- Calendar scroll-lock so the calendar does not jump when the selected-shifts list grows
- Bulk ICS export for all selected shifts
- Single-file HTML app with the logo embedded in the file
- No server, database, login, or external API calls

## Shift Types

| Shift | Time |
|---|---:|
| Morning | 06:00–14:30 |
| Afternoon | 14:00–22:30 |
| Night | 22:00–06:30, next day |
| Mid | 10:00–18:30 |

## Controls

- Single click: cycles through `Morning → Afternoon → Night → Empty`
- Single click on `Mid`: clears the shift
- Long press on an empty day: selects `Mid`
- Long press on an existing shift: clears the shift
- Click a selected shift card: opens Google Calendar event creation for that specific shift
- Click the `×` inside a selected shift card: deletes that shift without opening Google Calendar

## Google Calendar Links

Each selected shift card opens Google Calendar's event creation page for that specific shift.

The generated link includes:

- Event title
- Start/end time
- Location
- Time zone

The link intentionally does **not** include a `details` parameter.

## ICS Export

The ICS export button downloads one calendar file containing all selected shifts.

The exported events include:

- Shift title
- Start/end time
- Location

Event times are exported in UTC `Z` format for better compatibility with Google Calendar and other calendar apps.

## Usage

1. Open the web app in a browser.
2. Select the relevant month.
3. Click `פתיחת לוח להזנת משמרות`.
4. Add shifts by clicking or long-pressing days on the calendar.
5. Use one of the calendar options:
   - Click a selected-shift card to create that single event in Google Calendar.
   - Click the ICS export button to export all selected shifts at once.

## GitHub Pages Deployment

To publish this as a GitHub Pages site:

1. Rename the HTML file to `index.html`.
2. Upload `index.html` and this `README.md` to a public GitHub repository.
3. Go to repository `Settings` → `Pages`.
4. Select deployment from the `main` branch and root folder.
5. The site will be available at:

```text
https://USERNAME.github.io/REPOSITORY_NAME/
```

## Privacy

All data stays local in the browser. The app does not upload shift data, images, or calendar data to any server.
