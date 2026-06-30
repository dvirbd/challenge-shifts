# Challenge Airlines IL Shift Calendar Exporter

A simple Hebrew web app for manually selecting Challenge Airlines IL shifts and exporting them to an ICS calendar file that can be imported into Google Calendar, Apple Calendar, Outlook, and other calendar apps.

## Features

- Manual monthly shift selection
- Hebrew RTL interface
- Full-week calendar display, including days from the previous or next month when they are part of the first or last displayed week
- Single-file HTML app with the company logo embedded as a Data URL
- No server, no database, and no external API calls
- Local ICS generation directly in the browser

## Shift Types

| Shift | Time |
|---|---:|
| Morning | 06:00–14:30 |
| Afternoon | 14:00–22:30 |
| Night | 22:00–06:30, next day |
| Mid | 10:00–18:30 |

## Controls

- Single click: cycles through `Morning → Afternoon → Night → Empty`
- Long press: selects `Mid`
- Double click: clears the selected shift

## ICS Export

The exported calendar events use the following summary format:

```text
✈️🗺️ FOO - Morning
```

In Hebrew inside the actual app/export:

```text
✈️🗺️ FOO - בוקר
✈️🗺️ FOO - צהריים
✈️🗺️ FOO - לילה
✈️🗺️ FOO - אמצע
```

Event times are exported in UTC `Z` format for better compatibility with Google Calendar.

The event location is exported as:

```text
צ'אלנג' איירלינס (ישראל) בע"מ, אורן 4, שוהם, Israel
```

## Usage

Open the HTML file in a browser, select the relevant month, enter the shifts manually, and click the ICS export button.

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
