# Implementation Tasks

## Personal Dashboard

- [x] 1. Create the HTML file skeleton and layout shell
  - [x] 1.1 Create `dashboard.html` with `<!DOCTYPE html>`, `<head>`, and `<body>` structure
  - [x] 1.2 Add Google Fonts `<link>` tags for Inter, Orbitron, and Share Tech Mono in `<head>`
  - [x] 1.3 Add `<style>` block with CSS custom properties for cyberpunk base theme and `body.dark` variant on `body`
  - [x] 1.4 Add scanline overlay using `body::before` with `repeating-linear-gradient` at very low opacity
  - [x] 1.5 Add CSS Grid layout container `.dashboard-grid` with `grid-template-areas` for the four widget slots
  - [x] 1.6 Add responsive breakpoint at `768px` collapsing the grid to a single column
  - [x] 1.7 Add uniform `.card` styles using CSS variables (`--card-bg`, `--card-border`, `--card-glow`)
  - [x] 1.8 Add primary button styles (cyan neon fill, dark text, yellow glow on hover via `--btn-hover-glow`)
  - [x] 1.9 Add delete button styles (red neon fill, white text)
  - [x] 1.10 Add card title styles: Orbitron font, uppercase, `letter-spacing: 0.15em`
  - [x] 1.11 Add neon border styles for all text inputs using `--card-border` color
  - [x] 1.12 Add `<script>` block placeholder at the bottom of `<body>`

- [x] 2. Implement the Preloader
  - [x] 2.1 Add `#preloader` as the very first child of `<body>` with centered content: "DASHBOARD" title, spinner, loading bar track + fill, and "Initializing" text
  - [x] 2.2 Style `#preloader` as a `position: fixed; inset: 0` overlay using `var(--bg)` background and `z-index: 10000`; add `transition: opacity 0.6s ease`
  - [x] 2.3 Style `.preloader-title` using Orbitron font, large bold text, `color: var(--accent)`, `text-shadow: var(--clock-glow)`, `letter-spacing: 0.2em`
  - [x] 2.4 Style `.preloader-spinner` as a 48px circle with `border-top-color: var(--accent)` and `animation: spin 0.8s linear infinite`; define `@keyframes spin` (rotate 0 → 360deg)
  - [x] 2.5 Style `.preloader-bar-track` as a 200px × 4px container; style `.preloader-bar-fill` with `animation: loadBar 2.5s linear forwards`; define `@keyframes loadBar` (width 0% → 100%)
  - [x] 2.6 Style `.preloader-text::after` with `animation: dots 1.2s steps(3, end) infinite`; define `@keyframes dots` cycling through `.`, `..`, `...`
  - [x] 2.7 Implement `initPreloader()` — calls `setTimeout(hidePreloader, 2500)`
  - [x] 2.8 Implement `hidePreloader()` — sets `preloader.style.opacity = '0'`; on `transitionend` (once) sets `display: none`; add 700ms fallback guard in case `transitionend` does not fire

- [x] 3. Implement the Theme Toggle (dark mode)
  - [x] 3.1 Add `#theme-toggle` button element fixed at top-right of the page
  - [x] 3.2 Style the toggle as a circular icon button using CSS variables for colors
  - [x] 3.3 Implement `initTheme()` — reads `localStorage.getItem('theme')`, applies `body.dark` class if saved value is `'dark'`, sets initial icon
  - [x] 3.4 Implement `toggleTheme()` — toggles `body.dark`, saves new value to `localStorage` key `'theme'`, updates icon
  - [x] 3.5 Implement `updateToggleIcon()` — sets button text to `☀️` in dark mode and `🌙` in cyberpunk base mode
  - [x] 3.6 Call `initTheme()` as the very first JS call on page load (before other widgets render)

- [x] 4. Implement the Name Modal and personalized greeting
  - [x] 4.1 Add `#name-modal` overlay HTML (hidden by default) with `.modal-card` containing: "IDENTIFY YOURSELF" title, subtitle, `#name-input` (maxlength 30), `#name-save` button, and `#name-skip` button
  - [x] 4.2 Style `.modal-overlay` as `position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 9000` centered with flexbox
  - [x] 4.3 Style `.modal-card` using CSS variables (`--card-bg`, `--card-border`, `--card-glow`), Orbitron title, neon border on input
  - [x] 4.4 Style `#name-input.error` with `border-color: var(--danger)` and red glow for validation feedback
  - [x] 4.5 Implement `getGreetingBase()` — returns time-of-day greeting string based on current hour
  - [x] 4.6 Implement `updateGreeting()` — reads `localStorage.getItem('userName')`, builds personalized or generic greeting, writes to `#clock-greeting`
  - [x] 4.7 Implement `openNameModal()` — pre-fills `#name-input` with current saved name, clears error state, sets `display: flex`
  - [x] 4.8 Implement `closeNameModal()` — sets `#name-modal` to `display: none`
  - [x] 4.9 Implement `saveName()` — trims input; if empty applies `.error` class and returns; otherwise saves to `localStorage` key `'userName'`, closes modal, calls `updateGreeting()`
  - [x] 4.10 Implement `skipName()` — saves `''` to `localStorage` key `'userName'` (sentinel), closes modal, calls `updateGreeting()`
  - [x] 4.11 Implement `initName()` — reads `localStorage.getItem('userName')`; if `null` calls `openNameModal()`; attaches click handler to `#greeting-edit` pencil icon; attaches Save/Skip button handlers

- [x] 5. Implement the Clock & Greeting widget
  - [x] 5.1 Add `#clock-widget` card HTML with `#clock-time`, `#clock-date`, and a `.greeting-row` containing `#clock-greeting` and `#greeting-edit` (✏️) button; add card title "SYSTEM CLOCK" in Orbitron uppercase
  - [x] 5.2 Style `#clock-time` using Orbitron font, large size, `color: var(--accent)`, `text-shadow: var(--clock-glow)`
  - [x] 5.3 Style `#clock-greeting` using `color: var(--text-primary)`; style `#greeting-edit` as a small inline icon button with no background
  - [x] 5.4 Implement `updateClock()` — formats `HH:MM:SS`, full date string, calls `updateGreeting()`
  - [x] 5.5 Implement `initClock()` — calls `updateClock()` immediately then sets a 1-second interval

- [x] 6. Implement the Focus Timer widget
  - [x] 6.1 Add `#timer-widget` card HTML with `#timer-display` and Start / Stop / Reset buttons; add card title "FOCUS TIMER" in Orbitron uppercase
  - [x] 6.2 Style `#timer-display` using Share Tech Mono font, large size, `color: var(--timer-color)`, `text-shadow: var(--timer-glow)`, centered in the card
  - [x] 6.3 Implement module-scoped `totalSeconds` (1500) and `intervalId` (null) variables
  - [x] 6.4 Implement `updateTimerDisplay()` — formats `MM:SS` with zero-padding and writes to `#timer-display`
  - [x] 6.5 Implement `startTimer()` with guard against duplicate intervals
  - [x] 6.6 Implement `tick()` — decrements `totalSeconds`, calls `updateTimerDisplay()`, triggers alert and stop at 0
  - [x] 6.7 Implement `stopTimer()` — clears interval and sets `intervalId` to null
  - [x] 6.8 Implement `resetTimer()` — stops timer, resets `totalSeconds` to 1500, updates display
  - [x] 6.9 Implement `initTimer()` — attaches click handlers and calls `updateTimerDisplay()`

- [x] 7. Implement the To-Do List widget
  - [x] 7.1 Add `#todo-widget` card HTML with text input, Add button, sort row (`#todo-sort` select with 5 options), and `<ul id="todo-list">`; add card title "TASKS" in Orbitron uppercase
  - [x] 7.2 Style `.todo-sort-row` as a compact flex row; style `#todo-sort` using CSS variables (`--card-bg`, `--card-border`, `--text-primary`) with focus glow
  - [x] 7.3 Implement `loadTasks()` — reads and JSON-parses `dashboard_todos` from localStorage with try/catch fallback to `[]`
  - [x] 7.4 Implement `saveTasks()` — JSON-stringifies and writes `tasks` array to `dashboard_todos` (always in insertion order)
  - [x] 7.5 Implement `getSortedTasks()` — returns a shallow copy of `tasks` sorted according to `currentSort`; uses `localeCompare` with `{sensitivity:'base'}` for case-insensitive comparison; Done Last/First use completion state as primary key and alphabetical as tiebreaker
  - [x] 7.6 Implement `renderTasks()` — calls `getSortedTasks()`, clears `#todo-list`, rebuilds all `<li>` rows from the sorted copy
  - [x] 7.7 Apply strikethrough style and muted color (`--text-muted`) to task label when checkbox is checked; remove when unchecked
  - [x] 7.8 Implement `setSort(value)` — updates `currentSort`, saves to `localStorage` key `'taskSort'`, calls `renderTasks()`
  - [x] 7.9 Implement `addTask(text)` with empty-string guard; calls `saveTasks()` then `renderTasks()`
  - [x] 7.10 Implement `toggleTask(id)` — flips `checked`, saves, re-renders (re-sort applied automatically)
  - [x] 7.11 Implement `editTask(id)` — swaps label span for an inline text input pre-filled with current text
  - [x] 7.12 Implement `saveTask(id, newText)` — updates task text (keeps original if empty), saves, re-renders
  - [x] 7.13 Implement `deleteTask(id)` — filters task out, saves, re-renders
  - [x] 7.14 Use event delegation on `#todo-list` for all task interactions
  - [x] 7.15 Implement `initTodo()` — loads tasks, reads `localStorage.getItem('taskSort') ?? 'default'` into `currentSort`, sets `#todo-sort.value`, renders, attaches Add button handler, Enter keydown on input, and change handler on `#todo-sort`

- [x] 8. Implement the Quick Links widget
  - [x] 8.1 Add `#links-widget` card HTML with Link name input, URL input, Add Link button, and `#links-list` container; add card title "QUICK LINKS" in Orbitron uppercase
  - [x] 8.2 Implement `loadLinks()` — reads `dashboard_links` from localStorage; if null, seeds with three default links (Google, Gmail, Calendar) and saves
  - [x] 8.3 Implement `saveLinks()` — JSON-stringifies and writes `links` array to `dashboard_links`
  - [x] 8.4 Implement `renderLinks()` — clears `#links-list` and rebuilds link buttons with `×` remove buttons; style link buttons with cyan neon border
  - [x] 8.5 Implement `addLink(name, url)` with guard requiring both fields non-empty
  - [x] 8.6 Implement `removeLink(id)` — filters link out, saves, re-renders
  - [x] 8.7 Implement `openLink(url)` — calls `window.open(url, '_blank')`
  - [x] 8.8 Use event delegation on `#links-list` for open and remove actions
  - [x] 8.9 Implement `initLinks()` — loads links, renders, attaches Add Link button handler

- [x] 9. Wire up all modules and verify the complete dashboard
  - [x] 9.1 Call `initPreloader()`, `initTheme()`, `initName()`, `initClock()`, `initTimer()`, `initTodo()`, `initLinks()` in order inside a `DOMContentLoaded` listener
  - [x] 9.2 Verify preloader appears fullscreen on load, loading bar fills over 2.5s, dots cycle, then fades out and becomes non-interactive
  - [x] 9.3 Verify name modal appears on first visit (no `userName` in localStorage); saving a name personalizes the greeting; reloading skips the modal
  - [x] 9.4 Verify skipping the modal saves the sentinel and does not show the modal on reload
  - [x] 9.5 Verify the ✏️ edit icon opens the modal pre-filled and updating the name refreshes the greeting instantly
  - [x] 9.6 Verify empty name input shows red border and does not close the modal
  - [x] 9.7 Verify cyberpunk neon glow effects render correctly on cards, clock, and timer
  - [x] 9.8 Verify scanline overlay is visible but subtle across the full page
  - [x] 9.9 Verify button hover states show yellow neon glow (`#ffe600`)
  - [x] 9.10 Verify dark mode toggle switches all colors correctly and persists across reloads
  - [x] 9.11 Verify the layout is responsive and collapses to single column at ≤768px
  - [x] 9.12 Verify all localStorage data (tasks, links, theme, userName) survives a page reload
  - [x] 9.13 Verify each sort option (Default, A–Z, Z–A, Done Last, Done First) renders the list in the correct order
  - [x] 9.14 Verify checking/unchecking a task immediately re-sorts when Done Last or Done First is active
  - [x] 9.15 Verify the selected sort option persists across page reloads via `taskSort` in localStorage
