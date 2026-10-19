# Theme file format

The plug-ins in this family take their colours from a *theme*: a small JSON file. This page describes the file
and how a plug-in uses it. Solomon, the first plug-in to support themes, ships three of them (**Midnight**,
**Paper** and **Amber**); the same files are in [examples](../examples).

## Using a theme

1. Design one in the **Theme Studio** (open `index.html`, see the [README](../README.md)) and download the file.
2. In the plug-in, open the theme menu (under the title) and choose **Open themes folder...**, or copy the file
   to:

   | System  | Folder                                         |
   |---------|------------------------------------------------|
   | Windows | `%APPDATA%\Solomon\Themes`                     |
   | macOS   | `~/Library/Application Support/Solomon/Themes` |
   | Linux   | `~/.config/Solomon/Themes`                     |

3. Choose **Reload themes** in the same menu. Your theme now appears under *Your themes*.

The choice is a preference of the plug-in, shared by every instance and remembered between sessions. If a file
is ignored, *Reload themes* shows the reason for the first one.

## File format (version 1)

A [JSON Schema](../theme.schema.json) describes the structure.

```json
{
  "format": "plugin-theme",
  "version": 1,
  "name": "My theme",
  "colors": {
    "background": "#161a20",
    "panel": "#1d232b",
    "well": "#12161b",
    "accent": "#3ddc97",
    "warm": "#ffa94d",
    "text": "#e6ebf0",
    "dim": "#8b97a5",
    "edge": "#2c343e",
    "control": "#232a33",
    "controlEdge": "#343d49",
    "danger": "#ff5d5d"
  },
  "metrics": { "panelRadius": 8 }
}
```

| Colour        | Used for                                         |
|---------------|--------------------------------------------------|
| `background`  | the window                                       |
| `panel`       | the sections                                     |
| `well`        | graphs, meters and slider tracks                 |
| `accent`      | headers, knob arcs, live curves                  |
| `warm`        | negative modulation, high output level           |
| `text`        | values, menu text                                |
| `dim`         | labels and scales                                |
| `edge`        | grid lines, knob tracks                          |
| `control`     | menus, buttons, tooltips                         |
| `controlEdge` | outline of menus                                 |
| `danger`      | clipping                                         |

Rules, all enforced when the file is loaded:

- at most 8 KiB, valid JSON, `"format": "plugin-theme"`, integer `"version": 1`, a `name` (up to 48 characters);
- colours are `"#rrggbb"` strings; a colour that is missing keeps the Midnight value; unknown keys are ignored;
- `panelRadius` is a number, limited to 0-24 pixels;
- **readability:** text must reach a contrast of 3:1 on the window, the panels and the controls, the accent 2:1 and
  the dim text 1.8:1 on the panels. A theme that fails is refused, so a theme can never make the theme menu
  unreadable. The Theme Studio shows the same checks live.
- a theme with the name of a built-in or of an earlier file is ignored, and at most 100 files are read.
