# Design Document

## Personal Dashboard

---

## Overview

The Personal Dashboard is a single self-contained `.html` file with a Cyberpunk 2077-inspired visual theme. It delivers four interactive widgets on a near-black background with neon cyan card borders and glow effects. All CSS and JavaScript are embedded inline — no build step, no external dependencies beyond Google Fonts, and no server required. User data (tasks and links) is persisted entirely in `localStorage` so the dashboard works fully offline after the first load.

The four widgets are:

| Widget | Layout position |
|---|---|
| Clock & Greeting | Full-width, top |
| Focus Timer | Left column, middle row |
| To-Do List | Right column, middle row |
| Quick Links | Full-width, bottom |

A Theme_Toggle button is fixed in the top-right corner and switches between the cyberpunk base theme and a softer dark mode variant, with the preference saved to `localStorage`.

The design is intentionally minimal in its technical stack: vanilla HTML5, CSS3, and ES6+ JavaScript. No frameworks, no libraries, no transpiler.

---

## Architecture

The entire application lives in one file. The logical structure inside that file is:

```
dashboard.html
├── <head>
│   ├── Google Fonts links (Inter, Orbitron, Share Tech Mono)
│   └── <style>  ← all CSS (cyberpunk base + dark mode CSS variables + scanline overlay + preloader + name modal)
└── <body>
    ├── #preloader          ← FIRST child; fullscreen overlay, removed after 2.5s + 0.6s fade
    ├── #name-modal         ← centered overlay for name entry; shown on first visit or edit click
    ├── #theme-toggle       ← fixed position button, top-right
    ├── .dashboard-grid     ← CSS Grid layout container
    │   ├── #clock-widget
    │   ├── #timer-widget
    │   ├── #todo-widget
    │   └── #links-widget
    └── <script>  ← all JavaScript
        ├── Preloader module
        ├── Theme module
        ├── Name module
        ├── Clock module
        ├── Timer module
        ├── Todo module
        └── Links module
```

Each widget is a self-contained section of the DOM. The JavaScript is organised into four logical modules (plain functions and closures — no ES modules, no classes required) that each own their widget's state and DOM manipulation. They communicate only through `localStorage`; there is no shared in-memory state between widgets.

### Data flow

```
User interaction
      │
      ▼
Widget JS function
      │
      ├──► DOM update (re-render widget list)
      │
      └──► localStorage.setItem(key, JSON.stringify(data))

Page load
      │
      └──► localStorage.getItem(key) → JSON.parse → render
```

---

## Components and Interfaces

### 1. Preloader (`#preloader`)

**DOM structure:**

```html
<div id="preloader">
  <div class="preloader-content">
    <div class="preloader-title">DASHBOARD</div>
    <div class="preloader-spinner"></div>
    <div class="preloader-bar-track">
      <div class="preloader-bar-fill"></div>
    </div>
    <div class="preloader-text">Initializing</div>
  </div>
</div>
```

**CSS:**

```css
#preloader {
  position: fixed;
  inset: 0;
  background: var(--bg);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10000;
  transition: opacity 0.6s ease;
}

.preloader-title {
  font-family: 'Orbitron', sans-serif;
  font-size: 2.5rem;
  font-weight: 700;
  color: var(--accent);
  text-shadow: var(--clock-glow);
  letter-spacing: 0.2em;
  margin-bottom: 2rem;
}

/* Spinner */
.preloader-spinner {
  width: 48px;
  height: 48px;
  border: 4px solid transparent;
  border-top-color: var(--accent);
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  margin: 0 auto 1.5rem;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* Loading bar */
.preloader-bar-track {
  width: 200px;
  height: 4px;
  background: var(--card-bg);
  border: 1px solid var(--card-border);
  border-radius: 2px;
  overflow: hidden;
  margin: 0 auto 1rem;
}

.preloader-bar-fill {
  height: 100%;
  width: 0%;
  background: var(--accent);
  animation: loadBar 2.5s linear forwards;
}

@keyframes loadBar {
  to { width: 100%; }
}

/* Cycling dots text */
.preloader-text {
  color: var(--text-muted);
  font-family: 'Share Tech Mono', monospace;
  font-size: 0.85rem;
}

.preloader-text::after {
  content: '';
  animation: dots 1.2s steps(3, end) infinite;
}

@keyframes dots {
  0%   { content: '.'; }
  33%  { content: '..'; }
  66%  { content: '...'; }
  100% { content: ''; }
}
```

**JavaScript interface:**

```
initPreloader()
  ├── reads #preloader element
  ├── setTimeout(hidePreloader, 2500)

hidePreloader()
  ├── preloader.style.opacity = '0'
  └── preloader.addEventListener('transitionend', () => preloader.style.display = 'none', { once: true })
```

`initPreloader()` is called as the very first function in the `DOMContentLoaded` handler, before `initTheme()` and all widget inits.

### 2. Name Modal (`#name-modal`)

**DOM structure:**

```html
<div id="name-modal" class="modal-overlay" style="display:none">
  <div class="modal-card">
    <h2 class="modal-title">IDENTIFY YOURSELF</h2>
    <p class="modal-subtitle">Enter your name to personalize your dashboard</p>
    <input id="name-input" type="text" maxlength="30" placeholder="Your name…" autocomplete="off">
    <div class="modal-actions">
      <button id="name-save" class="btn-primary">Save</button>
      <button id="name-skip" class="btn-secondary">Skip</button>
    </div>
  </div>
</div>
```

**CSS:**

```css
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9000;
}

.modal-card {
  background: var(--card-bg);
  border: 1px solid var(--card-border);
  box-shadow: var(--card-glow);
  border-radius: 12px;
  padding: 2rem;
  width: min(400px, 90vw);
  text-align: center;
}

.modal-title {
  font-family: 'Orbitron', sans-serif;
  color: var(--accent);
  text-shadow: var(--clock-glow);
  letter-spacing: 0.15em;
  margin-bottom: 0.5rem;
}

/* Validation error state */
#name-input.error {
  border-color: var(--danger);
  box-shadow: 0 0 6px var(--danger);
}
```

**State:**

```
userName : string  — value from localStorage.getItem('userName')
                     null  → first visit, show modal
                     ''    → user previously skipped, no modal
                     'Alex' → personalized greeting
```

**localStorage key:** `userName`

**JavaScript interface:**

```
initName()
  ├── userName = localStorage.getItem('userName')
  ├── if userName === null → openNameModal()   // first visit
  └── attaches click handler to #greeting-edit (pencil icon)

openNameModal()
  ├── #name-input.value = localStorage.getItem('userName') ?? ''
  ├── #name-input.classList.remove('error')
  └── #name-modal.style.display = 'flex'

closeNameModal()
  └── #name-modal.style.display = 'none'

saveName()
  ├── name = #name-input.value.trim()
  ├── if name === '' → #name-input.classList.add('error'); return
  ├── localStorage.setItem('userName', name)
  ├── closeNameModal()
  └── updateGreeting()   // re-derives greeting with new name

skipName()
  ├── localStorage.setItem('userName', '')   // sentinel: skip recorded
  ├── closeNameModal()
  └── updateGreeting()

updateGreeting()
  ├── name = localStorage.getItem('userName') ?? ''
  ├── base = getGreetingBase()   // 'Good Morning' etc.
  └── #clock-greeting.textContent = name ? `${base}, ${name}!` : base
```

`initName()` is called after `initTheme()` and before `initClock()` in the `DOMContentLoaded` handler.

### 3. Layout Shell

A CSS Grid container (`.dashboard-grid`) with the following template:

```
"clock  clock"
"timer  todo"
"links  links"
```

Column widths: `1fr 1fr`. Gap: `20px`. Max-width `1200px`, centred with `margin: auto`. Padding `20px` on all sides.

At `≤768px` the grid collapses to a single column:

```
"clock"
"timer"
"todo"
"links"
```

Every widget card shares the uniform card style:

```css
background: #0d0d2b;
border: 1px solid #00ffe1;
border-radius: 12px;
padding: 20px;
box-shadow: 0 0 8px #00ffe1, 0 0 20px rgba(0,255,225,0.3);
```

The page background is `#0a0a1a` with a scanline overlay pseudo-element:

```css
body::before {
  content: '';
  position: fixed;
  inset: 0;
  pointer-events: none;
  background: repeating-linear-gradient(
    0deg,
    rgba(0,0,0,0.03) 0px,
    rgba(0,0,0,0.03) 1px,
    transparent 1px,
    transparent 4px
  );
  z-index: 9999;
}
```

Dark mode overrides card styles via the `body.dark` selector (see Theme module below).

### 4. Theme Toggle (`#theme-toggle`)

**DOM structure:**

```html
<button id="theme-toggle" title="Toggle dark mode">🌙</button>
```

Fixed position: `top: 20px; right: 20px`. Styled as a circular icon button (no background fill in light mode; subtle dark fill in dark mode).

**CSS approach — CSS custom properties on `body`:**

```css
/* Cyberpunk base (default / "light" mode) */
body {
  --bg: #0a0a1a;
  --card-bg: #0d0d2b;
  --card-border: #00ffe1;
  --card-glow: 0 0 8px #00ffe1, 0 0 20px rgba(0,255,225,0.3);
  --text-primary: #e0f7fa;
  --text-muted: #607d8b;
  --accent: #00ffe1;
  --accent-text: #0a0a1a;
  --danger: #ff003c;
  --clock-glow: 0 0 10px #00ffe1, 0 0 20px #00ffe1;
  --timer-color: #ff003c;
  --timer-glow: 0 0 10px #ff003c;
  --btn-hover-glow: 0 0 8px #ffe600;
}

/* Dark mode variant */
body.dark {
  --bg: #1a1a2e;
  --card-bg: #16213e;
  --card-border: #0f3460;
  --card-glow: none;
  --text-primary: #e0e0e0;
  --text-muted: #a0a0b0;
  --accent: #7c73e6;
  --accent-text: #ffffff;
  --danger: #e53935;
  --clock-glow: none;
  --timer-color: #7c73e6;
  --timer-glow: none;
  --btn-hover-glow: 0 0 8px #7c73e6;
}
```

All color-sensitive rules reference these variables. Switching theme is a single `body.classList.toggle('dark')` call.

**JavaScript interface:**

```
initTheme()
  ├── theme = localStorage.getItem('theme') ?? 'light'
  ├── if theme === 'dark' → body.classList.add('dark')
  ├── updateToggleIcon()
  └── attaches click handler to #theme-toggle

toggleTheme()
  ├── body.classList.toggle('dark')
  ├── theme = body.classList.contains('dark') ? 'dark' : 'light'
  ├── localStorage.setItem('theme', theme)
  └── updateToggleIcon()

updateToggleIcon()
  └── #theme-toggle.textContent = body.classList.contains('dark') ? '☀️' : '🌙'
```

**localStorage key:** `theme`

### 5. Clock Widget (`#clock-widget`)

**DOM structure:**

```html
<div id="clock-widget" class="card">
  <h2 class="card-title">SYSTEM CLOCK</h2>
  <div id="clock-time">HH:MM:SS</div>
  <div id="clock-date">Weekday, Month DD, YYYY</div>
  <div class="greeting-row">
    <span id="clock-greeting">Good Morning</span>
    <button id="greeting-edit" title="Edit name">✏️</button>
  </div>
</div>
```

**JavaScript interface:**

```
initClock()
  └── setInterval(updateClock, 1000)

updateClock()
  ├── reads new Date()
  ├── formats time as HH:MM:SS (padStart 2, '0')
  ├── formats date as toLocaleDateString('en-US', { weekday:'long', year:'numeric', month:'long', day:'numeric' })
  ├── calls updateGreeting()   // name-aware greeting
  └── writes to #clock-time, #clock-date

getGreetingBase()
  └── returns 'Good Morning' | 'Good Afternoon' | 'Good Evening' | 'Good Night' based on current hour

updateGreeting()
  ├── name = localStorage.getItem('userName') ?? ''
  ├── base = getGreetingBase()
  └── #clock-greeting.textContent = name ? `${base}, ${name}!` : base
```

### 6. Focus Timer Widget (`#timer-widget`)

**DOM structure:**

```html
<div id="timer-widget" class="card">
  <div id="timer-display">25:00</div>
  <div class="timer-controls">
    <button id="timer-start">Start</button>
    <button id="timer-stop">Stop</button>
    <button id="timer-reset">Reset</button>
  </div>
</div>
```

**State (module-scoped variables):**

```
totalSeconds  : number  — remaining seconds, initialised to 1500 (25 × 60)
intervalId    : number | null  — setInterval handle; null when stopped
```

**JavaScript interface:**

```
initTimer()
  └── attaches click handlers to Start / Stop / Reset buttons

startTimer()
  ├── guard: if intervalId !== null, return (no duplicate intervals)
  └── intervalId = setInterval(tick, 1000)

tick()
  ├── totalSeconds -= 1
  ├── updateTimerDisplay()
  └── if totalSeconds === 0 → stopTimer(), alert('Focus session complete!')

stopTimer()
  ├── clearInterval(intervalId)
  └── intervalId = null

resetTimer()
  ├── stopTimer()
  ├── totalSeconds = 1500
  └── updateTimerDisplay()

updateTimerDisplay()
  └── writes MM:SS (padStart) to #timer-display
```

### 7. To-Do List Widget (`#todo-widget`)

**DOM structure:**

```html
<div id="todo-widget" class="card">
  <h2 class="card-title">TASKS</h2>
  <div class="todo-input-row">
    <input id="todo-input" type="text" placeholder="New task…">
    <button id="todo-add">Add</button>
  </div>
  <div class="todo-sort-row">
    <label for="todo-sort" class="sort-label">Sort by:</label>
    <select id="todo-sort">
      <option value="default">Default</option>
      <option value="az">A–Z</option>
      <option value="za">Z–A</option>
      <option value="done-last">Done Last</option>
      <option value="done-first">Done First</option>
    </select>
  </div>
  <ul id="todo-list"></ul>
</div>
```

**CSS:**

```css
.todo-sort-row {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 0.75rem;
}

.sort-label {
  font-family: 'Orbitron', sans-serif;
  font-size: 0.7rem;
  color: var(--text-muted);
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

#todo-sort {
  background: var(--card-bg);
  color: var(--text-primary);
  border: 1px solid var(--card-border);
  border-radius: 4px;
  padding: 2px 6px;
  font-size: 0.8rem;
  cursor: pointer;
}

#todo-sort:focus {
  outline: none;
  box-shadow: 0 0 6px var(--accent);
}
```

Each rendered task item:

```html
<li data-id="{id}">
  <input type="checkbox" [checked]>
  <span class="todo-label [completed]">{text}</span>
  <button class="todo-edit">Edit</button>
  <button class="todo-delete">Delete</button>
</li>
```

When a task is in edit mode the `<span>` is replaced with:

```html
<input type="text" class="todo-edit-input" value="{text}">
<button class="todo-save">Save</button>
```

**State:**

```
tasks       : Array<{ id: string, text: string, checked: boolean }>
              — always stored in insertion order; never mutated by sort
currentSort : string  — one of 'default' | 'az' | 'za' | 'done-last' | 'done-first'
```

`id` is generated with `Date.now().toString()` (sufficient for single-user local use).

**localStorage keys:** `dashboard_todos`, `taskSort`

**Sort logic — `getSortedTasks()`:**

```
getSortedTasks()
  ├── copy = [...tasks]   // shallow copy; never mutate original
  ├── switch currentSort:
  │   case 'az':        copy.sort((a,b) => a.text.localeCompare(b.text, undefined, {sensitivity:'base'}))
  │   case 'za':        copy.sort((a,b) => b.text.localeCompare(a.text, undefined, {sensitivity:'base'}))
  │   case 'done-last': copy.sort((a,b) => {
  │                       if (a.checked !== b.checked) return a.checked ? 1 : -1
  │                       return a.text.localeCompare(b.text, undefined, {sensitivity:'base'})
  │                     })
  │   case 'done-first':copy.sort((a,b) => {
  │                       if (a.checked !== b.checked) return a.checked ? -1 : 1
  │                       return a.text.localeCompare(b.text, undefined, {sensitivity:'base'})
  │                     })
  │   default:          (no sort — insertion order preserved)
  └── return copy
```

**JavaScript interface:**

```
initTodo()
  ├── tasks = loadTasks()
  ├── currentSort = localStorage.getItem('taskSort') ?? 'default'
  ├── #todo-sort.value = currentSort
  ├── renderTasks()
  ├── attaches click handler to #todo-add, keydown Enter on #todo-input
  └── attaches change handler to #todo-sort → setSort(value)

setSort(value)
  ├── currentSort = value
  ├── localStorage.setItem('taskSort', value)
  └── renderTasks()

addTask(text)
  ├── guard: if text.trim() === '' return
  ├── tasks.push({ id, text: text.trim(), checked: false })
  ├── saveTasks()
  └── renderTasks()

toggleTask(id)
  ├── flips checked on matching task
  ├── saveTasks()
  └── renderTasks()

editTask(id)
  └── swaps label span → edit input + save button in DOM (no full re-render)

saveTask(id, newText)
  ├── guard: if newText.trim() === '' return (keep original)
  ├── updates tasks[i].text
  ├── saveTasks()
  └── renderTasks()

deleteTask(id)
  ├── tasks = tasks.filter(t => t.id !== id)
  ├── saveTasks()
  └── renderTasks()

saveTasks()
  └── localStorage.setItem('dashboard_todos', JSON.stringify(tasks))

loadTasks()
  └── JSON.parse(localStorage.getItem('dashboard_todos')) ?? []

renderTasks()
  ├── sorted = getSortedTasks()
  └── clears #todo-list, rebuilds <li> elements from sorted array
```

Event delegation is used on `#todo-list` for checkbox, edit, save, and delete actions to avoid re-attaching listeners on every render.

### 8. Quick Links Widget (`#links-widget`)

**DOM structure:**

```html
<div id="links-widget" class="card">
  <div class="links-input-row">
    <input id="link-name-input" type="text" placeholder="Link name">
    <input id="link-url-input" type="url" placeholder="https://…">
    <button id="link-add">Add Link</button>
  </div>
  <div id="links-list"></div>
</div>
```

Each rendered link:

```html
<span class="link-item">
  <button class="link-btn" data-url="{url}">{name}</button>
  <button class="link-remove" data-id="{id}">×</button>
</span>
```

**State:**

```
links : Array<{ id: string, name: string, url: string }>
```

**Default links (first-load only):**

```
{ name: 'Google',   url: 'https://www.google.com' }
{ name: 'Gmail',    url: 'https://mail.google.com' }
{ name: 'Calendar', url: 'https://calendar.google.com' }
```

**localStorage key:** `dashboard_links`

**JavaScript interface:**

```
initLinks()
  ├── raw = localStorage.getItem('dashboard_links')
  ├── if raw === null → links = defaultLinks, saveLinks()
  ├── else links = JSON.parse(raw)
  ├── renderLinks()
  └── attaches click handler to #link-add

addLink(name, url)
  ├── guard: if name.trim() === '' || url.trim() === '' return
  ├── links.push({ id: Date.now().toString(), name: name.trim(), url: url.trim() })
  ├── saveLinks()
  └── renderLinks()

removeLink(id)
  ├── links = links.filter(l => l.id !== id)
  ├── saveLinks()
  └── renderLinks()

openLink(url)
  └── window.open(url, '_blank')

saveLinks()
  └── localStorage.setItem('dashboard_links', JSON.stringify(links))

renderLinks()
  └── clears #links-list, rebuilds link buttons from links array
```

Event delegation on `#links-list` handles open and remove actions.

---

## Data Models

### Task

```js
{
  id:      string,   // Date.now().toString() — unique per session
  text:    string,   // task description, non-empty
  checked: boolean   // completion state
}
```

Persisted as a JSON array under `localStorage` key `dashboard_todos`.

### Link

```js
{
  id:   string,  // Date.now().toString()
  name: string,  // display label, non-empty
  url:  string   // destination URL, non-empty
}
```

Persisted as a JSON array under `localStorage` key `dashboard_links`.

### Timer state

The timer holds no persistent state — it resets to 25:00 on every page load. Only in-memory variables (`totalSeconds`, `intervalId`) are needed.

### Clock state

Stateless — reads `new Date()` on every tick.

### Theme state

```js
theme: 'light' | 'dark'   // string stored under localStorage key 'theme'
```

Applied by toggling the `dark` class on `<body>`. Defaults to `'light'` if no value is saved.

### User name state

```js
userName: string | null
// null  → key not present → first visit, show modal
// ''    → user skipped → no modal on reload, generic greeting
// 'Alex' → personalized greeting shown
```

Stored as a plain string under `localStorage` key `userName`.

### Task sort state

```js
currentSort: 'default' | 'az' | 'za' | 'done-last' | 'done-first'
```

Stored as a plain string under `localStorage` key `taskSort`. Defaults to `'default'` if not set. The `tasks` array in `dashboard_todos` is **never reordered** — sort is applied only at render time via `getSortedTasks()`.

---

## Error Handling

| Scenario | Handling |
|---|---|
| `localStorage.getItem` returns `null` (first load) | `?? []` / `?? defaultLinks` fallback |
| `JSON.parse` throws (corrupted storage) | Wrap in `try/catch`; fall back to empty array / default links and overwrite the corrupted key |
| Empty task input on Add | Guard clause: `if (text.trim() === '') return` — no task created, no error shown |
| Empty link inputs on Add Link | Guard clause: both fields must be non-empty — no link created |
| Edit confirmed with empty text | Keep original text; discard the edit silently |
| Timer Start called while already running | Guard: `if (intervalId !== null) return` — no duplicate `setInterval` |
| Timer reaches 00:00 | Auto-stop + `alert()` — browser native, no custom UI needed |
| `window.open` blocked by popup blocker | Browser handles natively; no additional handling required |
| `localStorage.getItem('theme')` returns `null` | Default to `'light'` mode via `?? 'light'` fallback |
| `transitionend` event never fires (e.g. transitions disabled) | Fallback: also set `display: none` inside the `setTimeout` after an additional 700ms guard |
| Name input empty on Save | Apply `.error` class (red border) to `#name-input`; do not close modal |
| Name input exceeds 30 chars | Prevented by `maxlength="30"` attribute on the input element |
| `localStorage.getItem('userName')` returns `null` | First-visit: open Name_Modal |
| `localStorage.getItem('userName')` returns `''` | Skip was recorded: show generic greeting, no modal |
| `localStorage.getItem('taskSort')` returns `null` | Default to `'default'` sort via `?? 'default'` fallback |

---

## Testing Strategy

Per Requirement 6.5 and the project's tech constraints, **no automated tests of any kind are included** — no unit tests, integration tests, or property-based tests. The deliverable is a single HTML file with inline CSS and JS; there is no test runner, no test framework, and no test scripts.

Verification is done manually:

| Area | Manual check |
|---|---|
| Preloader | On page load, confirm fullscreen preloader appears with "DASHBOARD" title, spinning circle, filling bar, and cycling dots text; confirm it fades out after ~2.5s and dashboard becomes interactive |
| Name modal (first visit) | Clear `userName` from localStorage, reload → confirm modal appears; enter a name → confirm personalized greeting; reload → confirm no modal, greeting persists |
| Name modal (skip) | Clear `userName`, reload → skip → confirm generic greeting; reload → confirm modal does not reappear |
| Name edit | Click ✏️ icon → confirm modal opens pre-filled; change name → confirm greeting updates instantly |
| Name validation | Open modal, click Save with empty input → confirm red border appears and modal stays open |
| Layout | Open in browser at full width and at <768px; confirm grid collapses correctly |
| Cyberpunk theme | Confirm `#0a0a1a` background, cyan neon card borders, glow effects, and scanline overlay are visible |
| Clock | Observe Orbitron font, cyan neon glow on time digits, updating every second; check greeting at different hours |
| Timer | Confirm Share Tech Mono font and red neon glow on countdown; Start → observe countdown; Stop → confirm pause; Reset → confirm 25:00; let run to 00:00 → confirm alert |
| Buttons | Hover over buttons → confirm yellow neon glow (`#ffe600`) appears |
| To-Do | Add task, check/uncheck, edit, delete; reload page → confirm persistence |
| Task sorting | Select each sort option → confirm list reorders correctly; check/uncheck a task with Done Last/First active → confirm immediate re-sort; reload → confirm saved sort is restored |
| Quick Links | Add link, click to open in new tab, remove; reload → confirm persistence; first-load defaults |
| Dark Mode | Click toggle → confirm dark variant colors applied; reload → confirm theme persists; toggle back → confirm cyberpunk base restored |
| Offline | Load page, disconnect network, reload → confirm full functionality |
