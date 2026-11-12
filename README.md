# Handoff: Kitchenline — restaurant management app

## Overview
A responsive web app for a restaurant owner/manager running several locations. One page, four screens switched by a bottom tab bar: **Pass** (kitchen ticket rail), **Floor** (top-down table plan), **Menu** (portions left / 86'd dishes), **Sales** (service figures and close-out receipt). The interface is available in English and Spanish.

## About the design files
The files in this bundle are **design references created in HTML**. They are a prototype showing the intended look and behavior, not production code to copy directly. The task is to **recreate this design in the target codebase's environment** (React, Vue, SwiftUI, etc.) using its established patterns and libraries. If no codebase exists yet, pick the most suitable framework (a React + TypeScript SPA is a reasonable default) and implement it there.

`Restaurant Manager v2.dc.html` is the design. Open it in a browser (it needs `support.js` and the `_ds/` folder beside it). Its markup is inside `<x-dc>` with inline styles; its logic is the `class Component` in the script at the bottom. `{{ name }}` in the markup refers to a value returned by `renderVals()`.

## Fidelity
**High-fidelity.** Colors, type, spacing and interactions are final. All data is sample data hard-coded in the file; replace it with real data sources.

## Design tokens

### Color (60 / 30 / 10)
| Role | Value | Use |
|---|---|---|
| Oat milk | `#EEE5D4` | ~60%. Page ground; ticket paper, occupied tables, receipt paper; text on green and on tomato |
| Forest green | `#1D4A35` | ~30%. All text on oat; chart and progress bars; solid fields behind the ticket rail, the floor plan and the bottom tab bar; active filter button |
| Tomato red | `#f2462e` | ~10%. Primary buttons, active tab, active segmented option, late flags, selection outline, tables waiting on a bill, ticket clip |

Derived values:
- Divider on oat: forest green at 28% alpha. Divider on green: oat at 30% alpha.
- Muted text: current text color at 55% alpha.
- Tomato hover: `color-mix(in srgb, #f2462e 86%, #1D4A35)`; pressed: 72%.
- Tinted tag background: `color-mix(in srgb, #f2462e 14%, #EEE5D4)` with forest green text.
- Known issue: oat text on tomato is about 3:1 contrast. Acceptable for the 18px timer; consider green or heavier weight for small labels.

### Type
- Headings and numerals: **Barlow Condensed**, weight 600.
- Body: **Barlow**, 400/500.
- Ticket lines and receipt: system monospace (`ui-monospace, Menlo, monospace`), 11–13px.
- Sizes used: stat numerals 42px (34px on Floor), ticket table number 26px, card titles ~18–20px, tab labels 20px, body 13–14px, kickers 10px uppercase with 0.1em tracking, meta 11–12px.

### Spacing, radius, shadow
- Spacing scale: 3.4, 6.8, 10.2, 13.6, 20.4, 27.2px (steps 1, 2, 3, 4, 6, 8).
- Radius: 0 on cards, buttons, tables and panels (square corners). Only seat pips and the one round table are circular.
- Shadows: sm `0 1px 2px`, md `0 3px 10px`, lg `0 12px 32px`, all ink-tinted at 14–22% alpha. Tickets use md, the receipt uses lg.

### Blueprint frame
Cards, panels and primary buttons are hairline-bordered rectangles with a small "+" registration mark at each corner (see `.blueprint` and `.corner` in `styles.css`). Cards are transparent line drawings; only the green fields and the primary button are filled.

## Global layout
- Full-height column: header, main (padding 13.6px top, 20.4px sides, 27.2px bottom), tab bar.
- **Header** (wraps on narrow screens): brand "Kitchenline"; location select (Downtown / Harbor / Uptown); service segmented control ("Lunch 12:00–15:00" / "Dinner 19:00 · 62 booked"); language segmented control (EN / ES); right-aligned "Covers so far" count and a live HH:MM clock.
- **Tab bar**: sticky to the bottom of the viewport, forest green field, four equal columns, min-height 60px. Each tab shows a two-digit index (10px) and a label (20px condensed). Active tab has a tomato background. The Pass tab shows a "N late" tag when any ticket is late.
- Everything reflows: grids use `auto-fit` with `minmax()`, and rows wrap. No fixed page width.

## Screens

### 01 Pass
Purpose: watch tickets in progress, fire the next course, clear finished tickets.

- **Ticket rail** (forest green panel with blueprint frame): one zone per station (Grill, Sauté, Cold, Pastry). Zones sit in a wrapping flex row; each zone grows in proportion to its ticket count and is at least wide enough for its tickets.
  - Zone header: station name (16px condensed uppercase), "N on · avg M:SS", and an "N late" tag when applicable.
  - Rail: a 2px oat line; tickets hang below it in one row, sorted oldest first, scrolling horizontally if they overflow.
  - **Ticket**: 112px wide oat paper, a 16×7px tomato clip at the top, slight rotation (−1° to +1°, stable per ticket). Contents: table number (26px) and covers; course in 10px uppercase above a dashed rule; item lines in 11px monospace; a timer strip (18px condensed, M:SS). When the ticket is older than the late threshold, the timer strip turns tomato with oat text and shows "LATE". The selected ticket has a 2px tomato outline offset 3px.
  - New tickets animate in over 0.5s ease-out: from `translateY(-130%) rotate(-9deg)`, opacity 0.
- Below, a wrapping grid (min column 260px) of four blocks:
  1. Stats: "On the pass" count with late count and oldest age; "Average ticket time" with done count and target.
  2. **Fire next** card: queued courses, each with table, "course · covers · station · note", item list, and a primary **Fire** button. Empty state: "Nothing waiting to fire."
  3. **Stations** card: per station, a bar showing the oldest ticket's age against the threshold (tomato when that station has a late ticket, green otherwise), the oldest age and the ticket count.
  4. **Selected ticket** card: station · course · covers, table, timer, items, and **Bump** (primary) and **Refire** (secondary) buttons. Empty state: "The rail is clear."

### 02 Floor
Purpose: see table status at a glance; seat, bill and clear tables.

- Left (about 3/4): filter buttons All / Eating / Bill / Free / Late with counts (active is solid green); then the **floor plan**, a forest green panel at 16:10 with tables absolutely positioned by percentage. Labels mark KITCHEN, BAR and ENTRANCE.
  - Table states: **Eating** oat fill with green text; **Late** oat fill plus a tomato ring; **Bill** tomato fill with oat text; **Free** transparent with a dashed oat border.
  - Each table shows its number; four- and six-seat tables also show a short status/course label. Seat pips are 8px circles around the table, filled when occupied.
  - Tables not matching the filter fade to 18% opacity. The selected table has a 2px tomato outline offset 7px.
- Right (about 1/4): three stats (covers seated, tables in use, average turn); a **selected table** card (status tag, seats, covers, course, check, one primary action); and a **Still to come** list of bookings with a Seat button each.

### 03 Menu
Purpose: track portions left and take dishes off the menu.

- Three stats: dishes on the menu, 86'd (with names), running low (3 or fewer left, with names).
- One card per group (Mains, Starters, Dessert), min column 300px. Each row: dish name with station and price; portions left (22px) or an "86" tag; a sold/made progress bar; a "−1" button and an "86"/"On" toggle. 86'd dishes are dimmed to 50% with strikethrough.

### 04 Sales
Purpose: review the service and close it out.

- Four stats: covers, gross sales, per cover, tips.
- Left: "Covers by quarter hour" bar chart (green bars, 180px tall) and "Best sellers" horizontal bars.
- Right: a **close-out receipt**, oat paper in 12px monospace inside a 13.6px forest green border, max-width 360px. Sections separated by dashed rules: counts, sales breakdown, payment types, comps and voids.

## Interactions and behavior
- **Tabs**: switch the visible screen; state on other screens is kept.
- **Timers**: tick every second. A ticket is late when its age exceeds the threshold (default 12 minutes, configurable 5–20).
- **Fire**: removes the entry from the queue, adds a ticket to its station's rail with a fresh timer, selects it, and plays the drop animation.
- **Bump**: removes the selected ticket and increments the done count (and "Covers so far").
- **Refire**: resets the selected ticket's timer.
- **Table action** cycles Free → Seat party → Eating → Print bill → Bill → Clear table → Free.
- **Seat (booking)**: seats the party at its assigned table, or the first free table for walk-ins, removes the booking and selects that table.
- **−1** reduces portions left; reaching 0 marks the dish 86'd. **86 / On** toggles availability.
- **EN / ES** switches all interface text, statuses, station, course and dish names, and the receipt. Guest names, location names and the brand stay unchanged. Spanish floor labels use short forms ("Princ.", "Entr.", "Sent.") to fit inside tables.
- Hover and pressed states on buttons step along the tomato ramp; keyboard focus is a 2px tomato outline offset 2px.
- Not implemented in the prototype: the location select and lunch/dinner switch only change labels; nothing is persisted; there are no loading or error states.

## State
- `tab`: pass | floor | menu | sales
- `lang`: en | es
- `service`: Lunch | Dinner; `location`
- `tickets[]`: id, table, covers, course, station, items[], start timestamp
- `queue[]`: table, covers, course, station, items[], optional note
- `done`: count of bumped tickets
- `selectedTicketId`, `selectedTableId`, `floorFilter`
- `tables[]`: id, seats, position and size (percent), round flag, status (Free | Eating | Late | Bill), covers, course, check total
- `bookings[]`: time, name, party, table
- `menu[]`: group, name, station, price, left, made, off

Options exposed in the prototype: late threshold (minutes), paper vs outline tickets, show seat pips.

A real build needs live data for tickets, tables, bookings, menu counts and sales, scoped by location and service. The Spanish strings were written by the designer and should be reviewed by a native speaker; move them into the app's i18n system.

## Assets
- Fonts: Barlow and Barlow Condensed (Google Fonts).
- No images or icons. If icons are added, use Lucide at stroke-width 1.5.

## Files
- `Restaurant Manager v2.dc.html`: the design (markup, logic, sample data, EN/ES dictionaries).
- `_ds/.../styles.css`: base tokens and component classes (`.btn`, `.tag`, `.card`, `.seg`, `.input`, `.nav`, `.blueprint`). The page overrides its color tokens in a `<style>` block at the top to apply the oat / green / tomato palette.
- `support.js`, `_ds/.../_ds_bundle.js`: runtime needed only to open the prototype in a browser.
