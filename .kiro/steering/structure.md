# Structure

```
/
├── README.md
├── <dashboard>.html        # Single deliverable — all HTML/CSS/JS inline
└── .kiro/
    ├── specs/
    │   └── personal-dashboard/
    │       └── requirements.md
    └── steering/
        ├── product.md
        ├── tech.md
        └── structure.md
```

## Conventions

- One file output. Everything lives in the `.html` file.
- `<style>` block for all CSS, `<script>` block for all JS — both inline in `<head>`/`<body>`.
- Widget layout: Clock (full-width) → Focus Timer + Todo (2-col) → Quick Links (full-width).
- Responsive breakpoint at `768px`: 2-col row collapses to single column.
- Card style uniform: `background: white`, `border-radius: 12px`, `padding: 20px`, `box-shadow: 0 4px 15px rgba(0,0,0,0.1)`.
- Primary buttons: blue/purple fill, white text. Delete buttons: red fill, white text.
- `localStorage` keys should be namespaced to avoid collisions (e.g. `dashboard_todos`, `dashboard_links`).
