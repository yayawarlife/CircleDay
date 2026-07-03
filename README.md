# CircleDay

A radial ("day-as-a-dial") calendar concept — your schedule mapped onto a clock face instead of a list, so blocked time is unmistakable at a glance. Built with an Android app as the near-term target.

**[Live demo →](#)** *(replace with your GitHub Pages URL once deployed — see below)*

## What it does

- Events render as pie-style wedges from the center of the dial out to the rim, sized to the hour(s) they occupy, with the task name labeled radially on the wedge
- A tapered clock hand marks the current time in real time
- **24-hour mode**: the full day, 00 at the top, going clockwise
- **12-hour mode**: a classic analog clock face — 00 fixed at the top, numbers 00–11 all the way around, exactly like a real clock (it repeats every 12 hours, same as any watch). A ☀/☾ badge and a subtle warm/cool tint disambiguate AM from PM
- Tap any event (on the dial or in the agenda list) to edit title, category, start time, *and* end time — or delete it
- Fully custom categories — add your own (Work, School, Gym, whatever) with your own colors
- Add tasks manually, or import a schedule from an Excel/CSV file
- Analysis view: time spent per category today, as a percentage of the day
- Phone preview frame showing how it'd sit as an Android home-screen widget

## Status: prototype, not production

This is a front-end concept prototype built to validate the design — not a shippable app yet. Specifically:

- **No real Google Calendar sync.** The "Connect" button is a placeholder. Wiring up real OAuth needs a Google Cloud project + client ID and (for a real widget) a backend or native calendar API integration.
- **No persistence.** Everything lives in memory and resets on page reload. A real version needs local storage or a database.
- **Not an actual OS widget yet.** This runs in a browser tab. A true Android home-screen widget needs a native build (Kotlin + Jetpack Compose + Glance API) that reuses this same dial-rendering logic and design decisions.
- **Excel import** looks for columns like Title/Task, Start, End (or Duration), and optionally Category — very unusually formatted sheets may need cleanup first.

## Tech

Single-file HTML/CSS/vanilla JS. SVG for the dial rendering (no chart library). [SheetJS](https://sheetjs.com/) (via CDN) for Excel/CSV parsing. No build step, no dependencies to install.

## Roadmap

- [ ] Native Android app (Kotlin/Jetpack Compose) + home-screen widget (Glance API)
- [ ] Real Google Calendar OAuth + sync
- [ ] Persistent storage
- [ ] Google Play Store listing

## Running it locally

Just open `index.html` in a browser — no server or build step required.
