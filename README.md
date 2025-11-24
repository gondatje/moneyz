# Money Cockpit Reference

This repository hosts a single-page web app contained entirely in [`index.html`]. The UI follows a soft, Apple-inspired style with rounded cards and gradient background.

## Layout
- **Header**: Title with import/export/snapshot buttons.
- **Summary row**: Four summary cards (Available This Cycle, Bills Coverage, Envelopes Funded, Month Ahead) showing metrics and progress bars.
- **Main content**: Two-column layout.
  - **Left card**: Calendar month view with previous/next/today controls and a day detail list.
  - **Right stack**: Pay panel (biweekly pay form and logged income), Bills panel (name/amount/due day), and Envelopes panel (category balances/targets).
- **Snapshot modal**: Shows profile, bills coverage, envelope totals, next cycle info, and raw JSON snapshot.

## State & Data Model
- All data lives in a global `appState` initialized from `defaultState` (versioned object).
- **Profile**: currency, fixed biweekly pay frequency anchored to a constant Friday (`FIXED_ANCHOR_DATE`), typical net amount, and per-date pay overrides.
- **Accounts**: Default checking and savings entries (currently unused in UI).
- **Bills**: Array of bill objects `{id, name, amount, dueDay, notes}`.
- **Categories**: Envelope objects `{id, name, balance, target}`.
- **Pay events**: Logged income entries `{id, type, date, amount}`.
- **Debts**: Placeholder array (not surfaced elsewhere).

## Key Behaviors
- **Payday generation**: `getPaydaysAround(anchorISO, monthsAhead)` builds a biweekly sequence around the anchor date.
- **Bill allocations**: `computeBillAllocations(paydays, monthsAhead)` splits each bill in half across the two paydays preceding its due date, producing per-payday allocation totals/items.
- **Calendar rendering**: `renderCalendar()` draws a 6-week grid with pay/bill dots, selection state, and delegates day details to `renderDayDetails()`.
- **Day details**: `renderDayDetails()` lists logged pay events, bill allocations when the day is a payday, and bills due that day.
- **Panels**: `renderBills()`, `renderCategories()`, `renderPaychecks()`, and `renderPayEvents()` manage their respective card contents and interactive add/edit/delete flows.
- **Summary**: `renderSummary()` computes available funds for the current pay cycle, bill coverage percent, total envelopes funded, and ahead-of-bills metric, updating progress bars.
- **Snapshot**: Import/export handlers read/write JSON (version 1) and enforce the fixed biweekly schedule; `renderSnapshotModal()` presents a human-friendly snapshot plus raw JSON.

## Rendering Lifecycle
`renderAll()` runs on startup and after any state mutation, calling summary, calendar/day details, bills, categories, pay settings, and pay event renderers. The selected month/day drives calendar context, while snapshot import/export preserves the `appState` structure.

## Styling Notes
The `<style>` block defines CSS variables for colors/radius/shadows and component classes for cards, grids, pills, tables, and modal elements. The design relies on rounded borders, subtle borders/shadows, and small typography for a clean dashboard aesthetic.

