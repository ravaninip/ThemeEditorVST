# Layout files and element lists

Two small JSON formats connect the Theme Studio and a plug-in.

- The **element list** (`plugin-layout-descriptor`) is written by the plug-in. It says which elements the
  interface has, where each one sits by default and which parameter it controls. The Studio reads it, so it can
  draw any plug-in without being changed.
- The **layout** (`plugin-layout`) is written by the Studio. It lists only the elements the user moved, resized
  or hid. The plug-in applies it on top of its built-in design.

Colours are a separate file, see [THEMES.md](THEMES.md).

## Element list

```json
{
  "format": "plugin-layout-descriptor",
  "version": 1,
  "plugin": "Solomon",
  "baseWidth": 980,
  "baseHeight": 720,
  "elements": [
    { "id": "knob.cutoff", "type": "knob", "label": "Cutoff", "param": "cutoff", "parent": "panel.filter",
      "x": 440, "y": 206, "w": 160, "h": 118 }
  ]
}
```

| Field | Meaning |
|-------|---------|
| `id` | stable identifier, `A-Z a-z 0-9 . _ -`, at most 64 characters; the plug-in and the layouts rely on it |
| `type` | `panel`, `label`, `knob`, `slider`, `menu`, `graph`, `meter` or `group` (decides how the Studio draws it) |
| `label` | human name shown in the Studio |
| `param` | optional: the parameter the element controls |
| `parent` | optional: the panel it belongs to; moving a panel can move its children with it |
| `locked` | optional: the element can be moved but never hidden (for example the menus that switch theme and layout) |
| `x y w h` | default place in base-window pixels |

[examples/solomon.descriptor.json](../examples/solomon.descriptor.json) is the list for Solomon, produced by
`solomon_plugin_smoke --descriptor file.json`. The Studio has it built in; use *Open element list…* for another
plug-in. At most 512 elements and 128 KiB.

## Layout

```json
{
  "format": "plugin-layout",
  "version": 1,
  "plugin": "Solomon",
  "name": "Wide filter",
  "baseWidth": 980,
  "baseHeight": 720,
  "elements": {
    "knob.cutoff":     { "x": 20, "y": 30, "w": 100, "h": 110 },
    "matrix.cell.0.0": { "x": 400, "y": 600, "w": 100, "h": 20, "visible": false }
  }
}
```

The plug-in enforces these rules when it loads the file; the Studio applies the same ones when it opens a file.

- at most 32 KiB, valid JSON, `"format": "plugin-layout"`, integer `"version": 1`, a `name` (up to 48 characters);
- `plugin` and `baseWidth` / `baseHeight` must match the plug-in, so a layout made for another plug-in or window
  size is refused;
- at most 256 elements; ids use the safe alphabet above; unknown ids are ignored;
- every rectangle is forced inside the window and to at least 12 × 12 pixels;
- `visible` is optional (default `true`); locked elements are never hidden.

An element that is not listed stays where the plug-in's built-in design puts it, so a layout keeps working when a
new version of the plug-in adds controls. See `layout.schema.json` for the structure.

## Adding layout support to another plug-in

1. Give every user-placeable element a stable id and register how to read its default place, place it and show or
   hide it (Solomon: `registerElements()` in `Source/PluginEditor.cpp`).
2. Reuse `Source/Layout.h/.cpp` from Solomon: format parsing, validation and the layout manager have nothing
   specific to Solomon.
3. Apply the layout at the end of `resized()`, keep the menu that switches layouts *locked*, and add a way to write
   the element list.
