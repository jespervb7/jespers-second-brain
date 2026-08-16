# Developer Setup Guide — Zilveren Kruis Power BI Theme

This guide covers two things every contributor needs before editing the theme JSON file: getting IntelliSense working in VS Code, and knowing where the authoritative Power BI report theme documentation lives.

## 1. Set up VS Code IntelliSense for the theme file

VS Code's built-in JSON language service can validate the theme file and offer autocomplete, hover documentation, and inline error squiggles — but only once it knows which JSON Schema to check the file against. No extension is required; this works with a stock VS Code install. There are two ways to wire it up, and you only need one.

### Option A: Reference the schema inside the file (recommended)

Add a `$schema` key as the first property of the theme JSON file, pointing at the schema's raw GitHub URL:

```json
{
  "$schema": "https://raw.githubusercontent.com/microsoft/powerbi-desktop-samples/main/Report%20Theme%20JSON%20Schema/reportThemeSchema-2.151.json",
  "name": "Zilveren Kruis",
  "dataColors": ["#002857", "#5B9BD5", "..."]
}
```

This is the simplest option because it travels with the file — anyone who opens it in VS Code gets IntelliSense immediately, with no workspace configuration needed. `2.151` is the current schema version as of this writing; see the note on versioning below.

### Using it

Once either option is in place:

- Reopen the theme file (or run **Developer: Reload Window** from the Command Palette) so VS Code picks up the schema.
- Press `Ctrl+Space` (or `Cmd+Space` on macOS) inside an object to see valid property names and enum values at that point in the schema.
- Hover over a property name to see its description straight from the schema.
- Invalid properties, wrong types, or out-of-range values (for example, a `fontSize` outside the schema's 6–45 range) are underlined in red with a description on hover.

### Keeping the schema version current

Microsoft ships a new `reportThemeSchema-<version>.json` file in the samples repo periodically as Power BI Desktop adds theme features. Check the [schema folder](https://github.com/microsoft/powerbi-desktop-samples/tree/main/Report%20Theme%20JSON%20Schema) occasionally for a newer version number and bump the URL in your `$schema` reference or `settings.json` accordingly. Using an older schema won't break anything, it just won't validate newer properties.

## 2. Where to find more information on Power BI report themes

**Official Microsoft documentation:**

- [Use report themes in Power BI](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-report-themes) - what themes are, how to apply/export/import them in Power BI Desktop.
- [Create custom report themes in Power BI Desktop](https://learn.microsoft.com/en-us/power-bi/create-reports/report-themes-create-custom) - the practical guide to authoring a custom theme JSON file, including structure and examples.
- [Visual defaults in Power BI reports](https://learn.microsoft.com/en-us/power-bi/create-reports/power-bi-reports-visual-defaults) - how theme settings cascade into per-visual formatting defaults.

**Schema (source of truth for this project):**

- [Report Theme JSON Schema folder](https://github.com/microsoft/powerbi-desktop-samples/tree/main/Report%20Theme%20JSON%20Schema) on the official `microsoft/powerbi-desktop-samples` GitHub repo. This project's instructions require validating every theme change against the most recent schema file here — it's the definitive reference for valid property names, types, and value ranges (more authoritative than the Learn docs, which lag behind and don't cover every property).
- The folder's `README.md` explains how the schema versions map to Power BI Desktop releases.

---

Sources:
- [Use report themes in Power BI](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-report-themes)
- [Create custom report themes in Power BI Desktop](https://learn.microsoft.com/en-us/power-bi/create-reports/report-themes-create-custom)
- [Visual defaults in Power BI reports](https://learn.microsoft.com/en-us/power-bi/create-reports/power-bi-reports-visual-defaults)
- [Report Theme JSON Schema folder (GitHub)](https://github.com/microsoft/powerbi-desktop-samples/tree/main/Report%20Theme%20JSON%20Schema)
- [reportThemeSchema-2.151.json (raw)](https://raw.githubusercontent.com/microsoft/powerbi-desktop-samples/main/Report%20Theme%20JSON%20Schema/reportThemeSchema-2.151.json)