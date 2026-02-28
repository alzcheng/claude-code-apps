# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

A collection of single-file, self-contained web apps — no build tools, no package manager, no bundler. Each app is one `.html` file that runs directly in the browser.

## Architecture Pattern

Every app follows the same pattern:
- **Single `.html` file** — HTML + `<style>` + `<script>` all inline
- **No frameworks** — vanilla JS only
- **CDN libraries only** — loaded via `<script src="...">` in `<head>` (e.g. SheetJS)
- **No server required** — open the file directly with `open filename.html`

## Running Apps

```bash
open project-manager.html   # macOS: opens in default browser
```

No install, no build step needed.

## Design System

All apps share the same dark color palette:

| Token | Value | Usage |
|---|---|---|
| Background | `#1a1a2e` | `body` background |
| Surface | `#16213e` | cards, panels, modals |
| Deep | `#0f3460` | buttons, table headers, inputs |
| Red accent | `#e94560` | primary actions, alerts, titles |
| Cyan accent | `#a8dadc` | active states, labels, links |
| Text | `#eee` | body text |

Interactive button pattern (view toggles):
```css
.view-btn { border: 2px solid #0f3460; background: #16213e; color: #aaa; }
.view-btn.active { background: #0f3460; border-color: #a8dadc; color: #a8dadc; }
```

## project-manager.html

**Data model:** Flat `tasks[]` array in `localStorage` (`pm_tasks` key). Each task:
```js
{ id, parentId, name, assignee, startDate, endDate, status, dependsOn[], collapsed, order }
```
Status values: `"todo"` | `"in-progress"` | `"done"` | `"blocked"`

**Key globals:** `tasks[]`, `currentView` (`'list'`|`'gantt'`), `pxPerDay` (Gantt zoom)

**Render flow:** user action → mutate `tasks[]` → `saveState()` → `render()` → `renderListView()` or `renderGanttView()`

**Gantt** is pure SVG built with `document.createElementNS`. Constants: `ROW_HEIGHT=36`, `LABEL_WIDTH=200`, `BAR_HEIGHT=20`, `HEADER_HEIGHT=48`. X-position of any date: `LABEL_WIDTH + daysBetween(minDate, dateISO) * pxPerDay`.

**Excel export** uses SheetJS (`XLSX`) from CDN — two sheets: flat "Tasks" and indented "Gantt Summary".
