# Requirements Document

## Introduction

A Personal Dashboard delivered as a single self-contained HTML file (HTML + CSS + JS). It has a Cyberpunk 2077-inspired visual theme with neon glow effects, and provides four widgets: a live Clock & Greeting, a Focus Timer, a To-Do List, and a Quick Links panel. All user data is persisted in `localStorage` so the dashboard works fully offline after the first load.

## Glossary

- **Dashboard**: The single HTML page that hosts all four widgets.
- **Clock_Widget**: The top full-width card displaying the live time, date, and greeting.
- **Focus_Timer**: The countdown timer widget that counts down from 25 minutes.
- **Todo_List**: The task management widget that stores tasks in localStorage.
- **Quick_Links**: The link launcher widget that stores user-defined URLs in localStorage.
- **Task**: A single to-do item consisting of a label, a completion state, and a unique identifier.
- **Link**: A user-defined entry consisting of a display name and a URL.
- **localStorage**: The browser's Web Storage API used for client-side persistence.
- **Theme**: The active color scheme of the dashboard — either "light" or "dark".
- **Theme_Toggle**: The button in the top-right corner that switches between light and dark mode.
- **Preloader**: The fullscreen overlay shown on page load before the dashboard is revealed.
- **Name_Modal**: The centered overlay dialog that prompts the user to enter their name.
- **userName**: The localStorage key storing the user's display name.
- **TaskSort**: The active sort order applied to the rendered To-Do List. One of: `default`, `az`, `za`, `done-last`, `done-first`.

---

## Requirements

### Requirement 1: Layout and Visual Design

**User Story:** As a user, I want a visually polished dashboard with a consistent design system, so that the page is pleasant to use and easy to read.

#### Acceptance Criteria

1. THE Dashboard SHALL use `#0a0a1a` as the page background color.
2. THE Dashboard SHALL load the Inter, Orbitron, and Share Tech Mono typefaces from Google Fonts.
3. THE Dashboard SHALL display the Clock_Widget as a full-width card at the top of the page.
4. THE Dashboard SHALL display the Focus_Timer card and the Todo_List card side by side in a two-column row below the Clock_Widget.
5. THE Dashboard SHALL display the Quick_Links card as a full-width card at the bottom of the page.
6. THE Dashboard SHALL apply the following styles to every card: `background: #0d0d2b`, `border: 1px solid #00ffe1`, `border-radius: 12px`, `padding: 20px`, `box-shadow: 0 0 8px #00ffe1, 0 0 20px rgba(0,255,225,0.3)`.
7. WHEN the viewport width is less than 768px, THE Dashboard SHALL stack the Focus_Timer card and the Todo_List card vertically into a single column.
8. THE Dashboard SHALL style primary action buttons with `#00ffe1` (cyan neon) fill and dark text.
9. THE Dashboard SHALL style delete/remove buttons with `#ff003c` (red neon) fill and white text.

---

### Requirement 2: Clock and Greeting

**User Story:** As a user, I want to see the current time, date, and a personalized contextual greeting, so that I always have an at-a-glance sense of the time of day.

#### Acceptance Criteria

1. THE Clock_Widget SHALL display the current local time in `HH:MM:SS` format, updating once per second.
2. THE Clock_Widget SHALL display the current local date in the format "Weekday, Month DD, YYYY" (e.g. "Tuesday, January 27, 2026") below the time.
3. THE Clock_Widget SHALL render the time using the Orbitron font with `text-shadow: 0 0 10px #00ffe1, 0 0 20px #00ffe1`.
4. WHEN the current hour is between 05 and 11 (inclusive) and no name is saved, THE Clock_Widget SHALL display the greeting "Good Morning".
5. WHEN the current hour is between 12 and 17 (inclusive) and no name is saved, THE Clock_Widget SHALL display the greeting "Good Afternoon".
6. WHEN the current hour is between 18 and 21 (inclusive) and no name is saved, THE Clock_Widget SHALL display the greeting "Good Evening".
7. WHEN the current hour is between 22 and 23 or between 00 and 04 (inclusive) and no name is saved, THE Clock_Widget SHALL display the greeting "Good Night".
8. WHEN a name is saved, THE Clock_Widget SHALL append the name to the greeting (e.g. "Good Morning, Alex!").
9. THE Clock_Widget SHALL render the greeting in `#e0f7fa` text below the date.
10. THE Clock_Widget SHALL display a small pencil edit icon (✏️) next to the greeting text that opens the Name_Modal when clicked.

---

### Requirement 3: Focus Timer

**User Story:** As a user, I want a 25-minute countdown timer with start, stop, and reset controls, so that I can time focused work sessions.

#### Acceptance Criteria

1. THE Focus_Timer SHALL initialise with a countdown value of 25 minutes and 00 seconds (25:00).
2. THE Focus_Timer SHALL display the remaining time in `MM:SS` format using the Share Tech Mono font with `color: #ff003c` and `text-shadow: 0 0 10px #ff003c`.
3. THE Focus_Timer SHALL provide a "Start" button that begins the countdown.
4. WHEN the "Start" button is activated and the timer is already running, THE Focus_Timer SHALL ignore the activation (no duplicate intervals).
5. THE Focus_Timer SHALL provide a "Stop" button that pauses the countdown without resetting the remaining time.
6. THE Focus_Timer SHALL provide a "Reset" button that stops the countdown and restores the display to 25:00.
7. WHEN the countdown reaches 00:00, THE Focus_Timer SHALL stop automatically and display a browser `alert` notifying the user that the session is complete.

---

### Requirement 4: To-Do List

**User Story:** As a user, I want to manage a list of tasks with add, complete, edit, and delete operations, so that I can track what I need to do.

#### Acceptance Criteria

1. THE Todo_List SHALL provide a text input field and an "Add" button for creating new tasks.
2. WHEN the "Add" button is activated and the text input is non-empty, THE Todo_List SHALL append a new Task with the entered text and an unchecked completion state.
3. WHEN the "Add" button is activated and the text input is empty, THE Todo_List SHALL not create a task.
4. THE Todo_List SHALL render each Task as a row containing: a checkbox, a text label, an "Edit" control, and a red "Delete" button.
5. WHEN a Task's checkbox is checked, THE Todo_List SHALL apply a strikethrough style to that Task's text label.
6. WHEN a Task's checkbox is unchecked, THE Todo_List SHALL remove the strikethrough style from that Task's text label.
7. WHEN the "Edit" control for a Task is activated, THE Todo_List SHALL replace the Task's text label with an editable input field pre-filled with the current task text.
8. WHEN the user confirms an edit (by pressing Enter or activating a save control), THE Todo_List SHALL update the Task's stored text and restore the label display.
9. WHEN the red "Delete" button for a Task is activated, THE Todo_List SHALL remove that Task from the list.
10. WHEN any Task is added, edited, deleted, or its completion state changes, THE Todo_List SHALL persist the full task list (text and checked state) to localStorage.
11. WHEN the Dashboard loads, THE Todo_List SHALL restore all Tasks from localStorage, including their text and checked state.

---

### Requirement 5: Quick Links

**User Story:** As a user, I want to save and launch frequently visited URLs from the dashboard, so that I can navigate to important sites with a single click.

#### Acceptance Criteria

1. THE Quick_Links SHALL provide a "Link name" text input, a "URL" text input, and an "Add Link" button.
2. WHEN the "Add Link" button is activated and both inputs are non-empty, THE Quick_Links SHALL save a new Link with the provided name and URL.
3. WHEN the "Add Link" button is activated and either input is empty, THE Quick_Links SHALL not create a link.
4. THE Quick_Links SHALL render each saved Link as a clickable button displaying the link name.
5. WHEN a Link button is activated, THE Quick_Links SHALL open the associated URL in a new browser tab.
6. THE Quick_Links SHALL render a small "×" remove button alongside each Link button.
7. WHEN the "×" remove button for a Link is activated, THE Quick_Links SHALL delete that Link from the list.
8. WHEN any Link is added or removed, THE Quick_Links SHALL persist the full link list to localStorage.
9. WHEN the Dashboard loads, THE Quick_Links SHALL restore all Links from localStorage.
10. WHEN the Dashboard loads for the first time and localStorage contains no links, THE Quick_Links SHALL pre-populate the list with three default links: "Google" (https://www.google.com), "Gmail" (https://mail.google.com), and "Calendar" (https://calendar.google.com).

---

### Requirement 6: Offline Capability and Self-Containment

**User Story:** As a user, I want the dashboard to work fully offline after the first load, so that I can use it without an internet connection.

#### Acceptance Criteria

1. THE Dashboard SHALL be delivered as a single `.html` file with all CSS and JavaScript embedded inline.
2. THE Dashboard SHALL not depend on any external JavaScript or CSS files beyond the Google Fonts stylesheet.
3. THE Dashboard SHALL use only vanilla HTML, CSS, and JavaScript with no third-party frameworks or libraries.
4. THE Dashboard SHALL use localStorage exclusively for all data persistence, requiring no server-side storage.
5. THE Dashboard SHALL NOT include any automated test files, test frameworks, or test scripts — no unit tests, integration tests, or property-based tests for HTML/CSS/JS are required.

---

### Requirement 7: Dark Mode

**User Story:** As a user, I want to switch between light and dark mode, so that I can use the dashboard comfortably in different lighting conditions.

#### Acceptance Criteria

1. THE Dashboard SHALL display a Theme_Toggle button fixed in the top-right corner of the page at all times.
2. THE Theme_Toggle SHALL display a moon icon (🌙) when the dashboard is in light mode.
3. THE Theme_Toggle SHALL display a sun icon (☀️) when the dashboard is in dark mode.
4. WHEN the Theme_Toggle is activated, THE Dashboard SHALL switch from light mode to dark mode, or from dark mode to light mode.
5. WHEN dark mode is active, THE Dashboard SHALL apply the following color scheme:
   - Page background: `#1a1a2e`
   - Card background: `#16213e`
   - Card border: `1px solid #0f3460`
   - Primary text: `#e0e0e0`
   - Secondary text: `#a0a0b0`
   - Accent/button color: `#7c73e6`
6. WHEN light mode is active, THE Dashboard SHALL restore the Cyberpunk base theme (dark background, cyan neon cards).
7. WHEN the theme is changed, THE Dashboard SHALL persist the selected theme to localStorage under the key `"theme"`.
8. WHEN the Dashboard loads, THE Dashboard SHALL read the `"theme"` key from localStorage and apply the saved theme before rendering any content.

---

### Requirement 9: Preloader

**User Story:** As a user, I want to see a branded loading screen when the dashboard first opens, so that the experience feels polished and intentional rather than showing a blank page.

#### Acceptance Criteria

1. THE Dashboard SHALL render a `#preloader` element as the very first child of `<body>` so it appears immediately before any other content.
2. THE Preloader SHALL cover the full viewport (`position: fixed`, `inset: 0`) with a background color matching the active page theme.
3. THE Preloader SHALL display the text "DASHBOARD" in large bold Orbitron font, centered on screen.
4. THE Preloader SHALL display a pure CSS animated spinner below the title — a circle with a transparent border and one side colored with the accent color (`#00ffe1`), rotating via `@keyframes spin` at `0.8s linear infinite`.
5. THE Preloader SHALL display a loading bar below the spinner — a thin bar (`4px` height) whose inner fill animates from `0%` to `100%` width over `2.5 seconds` using `@keyframes` or a CSS transition, colored with the accent color.
6. THE Preloader SHALL display the text "Initializing..." below the loading bar, with dots cycling (e.g. "Initializing.", "Initializing..", "Initializing...") using a CSS animation.
7. AFTER `2.5 seconds`, THE Dashboard SHALL begin fading out the Preloader using `opacity` transition of `0.6s ease`.
8. AFTER the fade-out transition completes, THE Dashboard SHALL set `display: none` on the Preloader so it no longer blocks interaction with the dashboard.
9. THE Preloader SHALL be hidden and non-interactive once dismissed — it SHALL NOT reappear on subsequent interactions.

---

### Requirement 10: Cyberpunk Visual Theme

**User Story:** As a user, I want the dashboard to have a Cyberpunk 2077-inspired visual style, so that the interface feels immersive and distinctive.

#### Acceptance Criteria

1. THE Dashboard SHALL render a CSS scanline overlay on the page background using a `repeating-linear-gradient` at very low opacity, creating a subtle CRT screen effect.
2. THE Dashboard SHALL apply Orbitron font to all card section titles, rendered in uppercase with `letter-spacing: 0.15em`.
3. THE Dashboard SHALL apply Share Tech Mono font to the timer countdown display.
4. THE Dashboard SHALL apply Orbitron font to the clock time display.
5. WHEN a button is hovered, THE Dashboard SHALL apply `box-shadow: 0 0 8px #ffe600` to that button.
6. THE Dashboard SHALL use `1px solid` neon-colored borders on all interactive inputs — no soft gray borders.
7. THE Dashboard SHALL use `#607d8b` as the muted/secondary text color for placeholder text and labels.

---

### Requirement 12: To-Do List Task Sorting

**User Story:** As a user, I want to sort my task list in different ways, so that I can focus on what matters most.

#### Acceptance Criteria

1. THE Todo_List SHALL display a compact "Sort by:" control above the task list with five options: Default, A–Z, Z–A, Done Last, Done First.
2. THE sort control SHALL be implemented as a `<select>` element styled to match the card theme.
3. WHEN "Default" is selected, THE Todo_List SHALL render tasks in their original insertion order.
4. WHEN "A–Z" is selected, THE Todo_List SHALL render tasks sorted alphabetically ascending by task text (case-insensitive).
5. WHEN "Z–A" is selected, THE Todo_List SHALL render tasks sorted alphabetically descending by task text (case-insensitive).
6. WHEN "Done Last" is selected, THE Todo_List SHALL render unchecked tasks first, then checked tasks; within each group tasks SHALL be sorted alphabetically ascending as a tiebreaker.
7. WHEN "Done First" is selected, THE Todo_List SHALL render checked tasks first, then unchecked tasks; within each group tasks SHALL be sorted alphabetically ascending as a tiebreaker.
8. THE sort order SHALL be applied to the rendered list only — the underlying `tasks` array in localStorage SHALL always retain the original insertion order.
9. WHEN a task is added, edited, deleted, or its completion state changes, THE Todo_List SHALL re-apply the current sort automatically before re-rendering.
10. WHEN the sort control is changed, THE Dashboard SHALL save the selected sort value to localStorage under the key `"taskSort"`.
11. WHEN the Dashboard loads, THE Todo_List SHALL read `"taskSort"` from localStorage and restore the saved sort option, applying it to the initial render.
12. THE active sort option SHALL be visually highlighted in the sort control.

---

### Requirement 11: Custom Name and Personalized Greeting

**User Story:** As a user, I want to enter my name so the dashboard greets me personally, so that the experience feels tailored to me.

#### Acceptance Criteria

1. WHEN the Dashboard loads for the first time and `localStorage` contains no `"userName"` key, THE Dashboard SHALL display the Name_Modal prompting the user to enter their name.
2. THE Name_Modal SHALL be a centered overlay with a semi-transparent dark background (`rgba(0,0,0,0.5)`), containing: a prompt heading, a text input field, a "Save" primary button, and a "Skip" button.
3. WHEN the "Save" button is activated and the trimmed input is non-empty, THE Dashboard SHALL save the trimmed name to `localStorage` under key `"userName"`, close the modal, and update the greeting immediately.
4. WHEN the "Save" button is activated and the trimmed input is empty, THE Dashboard SHALL apply a red border to the input field and not close the modal.
5. THE name input SHALL enforce a maximum length of 30 characters.
6. WHEN the "Skip" button is activated, THE Dashboard SHALL close the modal and display the generic (non-personalized) greeting; the modal SHALL NOT reappear automatically on subsequent page loads after a skip.
7. WHEN a skip has been recorded, THE Dashboard SHALL save a sentinel value (`"userName"` key set to `""`) to `localStorage` so the modal is not shown again on reload.
8. WHEN the Dashboard loads and `localStorage` contains a non-empty `"userName"` value, THE Dashboard SHALL display the personalized greeting immediately without showing the modal.
9. THE Clock_Widget SHALL display a pencil edit icon (✏️) next to the greeting text at all times.
10. WHEN the pencil edit icon is clicked, THE Dashboard SHALL open the Name_Modal pre-filled with the current saved name (if any).
11. WHEN a new name is saved via the edit flow, THE greeting SHALL update instantly without a page reload.
8. WHEN the Dashboard loads and `localStorage` contains a non-empty `"userName"` value, THE Dashboard SHALL display the personalized greeting immediately without showing the modal.
9. THE Clock_Widget SHALL display a pencil edit icon (✏️) next to the greeting text at all times.
10. WHEN the pencil edit icon is clicked, THE Dashboard SHALL open the Name_Modal pre-filled with the current saved name (if any).
11. WHEN a new name is saved via the edit flow, THE greeting SHALL update instantly without a page reload.
