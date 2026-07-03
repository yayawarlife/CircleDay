# CircleDay — Handoff Notes

Context for picking this project back up in a new chat. Pair this with `index.html` (the working prototype) and `README.md` (portfolio-facing summary) in project knowledge.

## What this is

A radial "day-as-a-dial" calendar concept. The core idea: your day mapped onto a clock circle instead of a list, so blocked time and free time are both visible at a glance without reading text. Built as a single self-contained HTML/CSS/vanilla-JS file — no build step, no backend.

Original goal: a home-screen widget that connects to Google Calendar and shows meetings/appointments in real time, so nothing gets missed. **Current direction: Android only, aiming for a real native app on Google Play** — the HTML file is the interaction/visual spec, not the final deliverable. Actual native development belongs in Claude Code (Kotlin + Jetpack Compose + Glance API for the widget), not this chat interface.

## Feature list, with the reasoning behind each

- **24-hour full-day mode.** 00 at the top, clockwise, the whole day visible at once. No ambiguity since every hour appears exactly once.
- **12-hour mode = a classic analog clock face, not a sliding window.** Earlier version tried a "sliding window centered on now" (now always at top, dial continuously drifts) — this was rejected as confusing. Current version: 00 is *fixed* at the top, numbers 00–11 all labeled around the face, and the hand moves around it just like a real watch (repeating every 12 hours). A ☀/☾ badge plus a subtle warm/cool tint on the widget resolve the AM/PM ambiguity that a real 12-hour clock always has. This was explicitly chosen for familiarity — "like a normal clock" — over anything cleverer.
- **Events as filled pie wedges (center → rim).** Not a thin ring band — the intent is that blocked time reads as "this much of my day," not just a mark on a ring.
- **Radial (spoke-aligned) labels, with a corrected flip rule.** Labels run along the spoke, center→rim on one half of the circle and rim→center on the other half, so nothing ever reads upside-down. **Important bug history:** the first implementation flipped at the wrong boundary (90°/270°, i.e. right/left) instead of the mathematically correct boundary (180°/360°, i.e. top-half vs bottom-half of rotation space, which visually corresponds to the right hemisphere reading normally and the left hemisphere flipping). If label readability ever looks wrong again after an edit near this code, check `renderDial()`'s label rotation logic — the flip condition must be `norm > 180`, not a left/right split. The correct rule, described the way the user framed it: hours 00→6 read clockwise (normal), hours 6→00 read anticlockwise (flipped).
- **Tapered "clock hand"** for the now-indicator, not a uniform-width line — reads more clearly as *the* current-time indicator.
- **Solid border rim**, replacing an earlier animated "spectrograph" bar effect that was removed for being too busy/decorative without functional value.
- **No gap/free-time labels.** These were tried, then explicitly removed — the user found them unnecessary clutter once they'd seen it live.
- **Fully custom categories.** Add/remove categories (name + color) in Settings. Default seed categories are Work/Personal/Focus/Health, purely as examples.
- **Tap-to-edit.** Tapping any wedge or agenda row opens an editable modal: title, category, **start time and end time both directly editable** (not a duration dropdown — that was the original design, changed on request so exact end times can be set), plus Delete.
- **Manual task creation** ("+ Add task") — independent of calendar import.
- **Excel/CSV import** — SheetJS (via CDN) client-side. Looks for columns matching Title/Task/Event, Start, End (or Duration), and optionally Category, case-insensitively. No AI involved.
- **Android-only preview.** The Phone/Desktop toggle and desktop frame were removed — only the phone frame remains, matching the decision to build Android first (not Apple's App Store, which is a different platform/terminology the user needs to keep in mind for later).

## Features that were built, then explicitly removed (don't re-add without being asked)

- **Month view** (mini pie-chart per day, month navigation, past-day detail modal, seeded-synthetic per-day data) — removed entirely per user request to simplify scope. All the supporting code (`synthDayEvents`, `synthDaySummary`, `buildMiniRing`, `renderMonthGrid`, `monthCursor`, the day-detail modal) was deleted, not just hidden.
- **Image import (AI vision timetable reading)** — removed. Used to call Claude's vision API directly from the browser; worked inside Claude.ai's artifact environment but would need a backend proxy + API key once hosted standalone. Removed entirely rather than kept as a broken feature.
- **Animated spectrograph rim** — removed, replaced with a plain static border circle.
- **Desktop preview frame** — removed now that Android is the explicit near-term target.
- **Free-time gap labels on the dial** — removed after being tried.
- **12h "sliding window centered on now"** — replaced by the classic fixed clock face described above. This was a full redesign of `angleForTime()`, not a tweak.

## What's real vs. placeholder

| Real | Placeholder |
|---|---|
| Dial rendering, clock hand, wedge geometry, urgency logic, both clock modes | Google Calendar OAuth ("Connect" button is a no-op with an explanatory note) |
| Category CRUD | Data persistence (everything resets on reload) |
| Manual task add/edit (incl. end time)/delete | Native Android app / home-screen widget (this is still a browser page) |
| Excel/CSV import (real parsing) | Google Play Store listing |

## Known gotchas already hit and fixed

- **`geo.R_HOLE` referenced before `geo` existed** inside `buildWidget()` (it's only assembled in the function's `return` statement) — threw `ReferenceError: geo is not defined`, which halted the whole script and cascaded into a second `Cannot access 'monthCursor' before initialization` error. Fixed by using the local `R_HOLE` constant. **Lesson: inside `buildWidget`, use the loose local radius constants, not `geo.*`, since `geo` doesn't exist until the function returns.**
- **Radial label flip boundary was off by 90°** (see above) — derived from testing the wrong variable (the raw angle instead of the computed rotation). Fixed to `norm > 180`.
- Artifacts in Claude.ai cannot use `localStorage`/`sessionStorage` — all state is in-memory only, resets on reload. This is a hard platform constraint, not an oversight, and won't apply once this becomes a real native app.

## Design tokens

- Background: `#05070C` / panel `#10141F` / glass panel `rgba(16,20,31,0.62)`
- Text: hi `#EDF1F8`, lo `#7C8699`, faint `#4A5266`
- Clock hand: `#F4FBFF` (near-white, intentionally distinct from category colors and from urgent red)
- Urgent: `#FF5C7A`
- Default categories: Work `#5B8DEF`, Personal `#E8A33D`, Focus `#B980F0`, Health `#45D6A0`
- Fonts: Space Grotesk (UI text), JetBrains Mono (numerals/time/technical labels)
- Wedge labels: 15px, weight 700. Hour numbers on the rim: 18px, weight 700.

## File manifest

- `index.html` — the working prototype (must be exactly this filename at repo root for GitHub Pages)
- `README.md` — portfolio-facing description, honest limitations list
- `circleday-handoff-notes.md` — this file

## Suggested next steps

1. **Move actual development to Claude Code**, not this chat/artifact environment. This HTML file is the finished spec — colors, geometry, interaction rules, the 12h/24h logic — to hand off, not to keep extending indefinitely as a web page.
2. Native Android project: Kotlin + Jetpack Compose for the app, Glance API for the home-screen widget, Canvas/Compose drawing for the dial (reuse the same angle math documented above).
3. Real Google Calendar OAuth (Google Sign-In SDK + Calendar API scope).
4. Local persistence (Room database or DataStore).
5. Google Play Console: developer account, signing, store listing, privacy policy (required since the app touches calendar data).

## Deployment reminder (for the web prototype specifically)

GitHub Pages: new public repo → upload `index.html` + `README.md` → Settings → Pages → Deploy from branch (`main`, root) → live at `https://username.github.io/repo-name/`. This is for showing the prototype/portfolio piece — separate from the native Android app, which won't be deployed this way.
