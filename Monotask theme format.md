# Monotask theme format

*A guide for writing your own themes, or for handing to another LLM to write them.*

Themes are plain data: colors, fonts, corner radius and icons. Write one as JSON or YAML, then install it in Monotask via **Themes → Install a theme** (paste it, or upload a .json / .yaml / .yml file). You can install several at once as a list.

## 1. Sample theme (JSON)

```json
{
  "id": "sunset",
  "name": "Sunset",
  "description": "Warm orange on cream",
  "fonts": {
    "heading": "'Syne', sans-serif",
    "body": "'DM Sans', sans-serif",
    "mono": "'DM Mono', monospace",
    "googleFonts": "https://fonts.googleapis.com/css2?family=DM+Mono&family=DM+Sans&family=Syne:wght@700&display=swap"
  },
  "radius": "1.25rem",
  "iconStroke": 2,
  "icons": {
    "box": "<circle cx='12' cy='12' r='8'/>",
    "boxChecked": "<circle cx='12' cy='12' r='8'/><path d='M8 12.5l3 3 5-6'/>"
  },
  "modes": {
    "light": { "surface": "#FFF7F0", "surface2": "#FFE9D6", "ink": "#2A1205", "muted": "#7A4A2E", "accent": "#C2410C", "accentHover": "#9A3412", "accentInk": "#FFFFFF", "line": "rgba(42,18,5,0.2)" },
    "dark": { "surface": "#1C0B04", "surface2": "#2A1308", "ink": "#FFE8D6", "muted": "#D9A27F", "accent": "#FB923C", "accentHover": "#FDBA74", "accentInk": "#1C0B04", "line": "rgba(255,232,214,0.2)" }
  }
}
```

## 2. The same theme in YAML

```yaml
id: sunset
name: Sunset
description: Warm orange on cream
fonts:
  heading: Syne, sans-serif
  body: DM Sans, sans-serif
  mono: DM Mono, monospace
  googleFonts: https://fonts.googleapis.com/css2?family=DM+Mono&family=DM+Sans&family=Syne:wght@700&display=swap
radius: 1.25rem
iconStroke: 2
icons:
  box: <circle cx='12' cy='12' r='8'/>
  boxChecked: <circle cx='12' cy='12' r='8'/><path d='M8 12.5l3 3 5-6'/>
modes:
  light:
    surface: '#FFF7F0'
    surface2: '#FFE9D6'
    ink: '#2A1205'
    muted: '#7A4A2E'
    accent: '#C2410C'
    accentHover: '#9A3412'
    accentInk: '#FFFFFF'
    line: rgba(42,18,5,0.2)
  dark:
    surface: '#1C0B04'
    surface2: '#2A1308'
    ink: '#FFE8D6'
    muted: '#D9A27F'
    accent: '#FB923C'
    accentHover: '#FDBA74'
    accentInk: '#1C0B04'
    line: rgba(255,232,214,0.2)
```

## 3. Field reference

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| id | string | no | Lowercase slug. Defaults to a slug of the name. Must not be mono, paper or terminal. Installing the same id again replaces the old theme. |
| name | string | yes | Shown in the theme list (max 40 characters). |
| description | string | no | One-line subtitle (max 80 characters). |
| fonts.heading, fonts.body, fonts.mono | CSS font stack | no | For example 'Syne', sans-serif. Always end with a generic fallback. |
| fonts.googleFonts | URL | no | Must start with https://fonts.googleapis.com/ |
| radius | CSS length | no | Corner radius for buttons, inputs and the task list, e.g. 0, 0.5rem, 1.5rem. |
| iconStroke | number | no | Icon line thickness from 0.5 to 4. Default 1.75. |
| icons | object | no | Override any icon by name. See section 5. |
| modes.light, modes.dark | object | no, but recommended | The 8 color tokens. See section 4. |

## 4. Color tokens

| Token | Used for |
| --- | --- |
| surface | Page background |
| surface2 | Task list and input background, hover fills |
| ink | Main text |
| muted | Secondary text, completed tasks, hints |
| accent | Primary buttons, active filter, checkboxes, focus ring |
| accentHover | Primary button hover |
| accentInk | Text drawn on top of accent (must contrast with accent) |
| line | Borders and dividers (usually semi-transparent) |

Values can be any CSS color: hex, rgb(), rgba() or hsl(). Keep ink on surface, and accentInk on accent, at WCAG AA contrast (4.5:1 or better). Define both light and dark. For a strict black and white theme, use only #000000, #FFFFFF and greys. Any token you leave out falls back to the built-in Mono value.

## 5. Icons

Each icon is the **inside** of a 24×24 SVG, with no outer `<svg>` tag. Shapes are drawn as outlines in the current text color, with line width taken from iconStroke.

Icon names: **add**, **box** (empty checkbox), **boxChecked**, **trash**, **edit**, **sun**, **moon**, **palette**, **close**, **trashSmall**. Icons you do not override keep the Mono version. Unknown names are ignored.

Allowed tags: path, circle, rect, line, polyline, polygon, ellipse, g.

Allowed attributes: d, cx, cy, r, rx, ry, x, y, x1, y1, x2, y2, width, height, points, transform, fill, stroke-width, opacity. Everything else (scripts, styles, stroke, event handlers, images) is removed.

Use single quotes around attribute values so the JSON stays easy to write. Design on a 24×24 grid with roughly 4px of padding.

## 6. Fonts

Pick two or three families on Google Fonts and combine them into one URL (family=...&family=...&display=swap). Heading and body can differ; mono is used for small labels. If you leave out googleFonts, the theme uses fonts already on the device, so give a good fallback stack. The characters ; { } < > are removed from font and radius values.

## 7. Rules and limits

- name is the only required field.
- Missing values fall back to Mono, so partial themes are fine.
- Install one theme (an object) or several (a list, or an object with a themes list).
- An invalid theme is rejected with a message under the Install button, and nothing changes.
- Themes are stored in your browser only. Remove a custom theme with its trash button in the list.
- The ids mono, paper and terminal are reserved for built-in themes.

## 8. Prompt for another LLM

Copy the block below, replace the last line with your theme idea, and send it to any LLM.

```text
You are writing a theme file for the Monotask todo app. Output ONLY one valid JSON object, with no commentary, following exactly this schema:
- name (required string), id (optional lowercase slug; never 'mono', 'paper' or 'terminal'), description (short string)
- fonts: heading, body, mono (CSS font stacks using single quotes, ending in a generic family) and googleFonts (one https://fonts.googleapis.com/css2?... URL covering every font used)
- radius: a CSS length such as 0.5rem; iconStroke: a number between 0.5 and 4
- icons (optional): any of add, box, boxChecked, trash, edit, sun, moon, palette, close, trashSmall. Each value is the inside of a 24x24 SVG as a string, using only the tags path, circle, rect, line, polyline, polygon, ellipse, g and the attributes d, cx, cy, r, rx, ry, x, y, x1, y1, x2, y2, width, height, points, transform, fill, stroke-width, opacity. Use single quotes for attribute values. Do not set stroke or colors; icons are outlines that inherit the text color.
- modes.light and modes.dark, each with exactly these 8 CSS color strings: surface (page background), surface2 (cards and inputs), ink (text), muted (secondary text), accent (buttons), accentHover (button hover), accentInk (text on accent), line (borders).
Requirements: ink on surface and accentInk on accent must meet WCAG AA contrast (4.5:1) in both modes. Light and dark must feel like the same theme.
Theme idea: (write your idea here)
```
