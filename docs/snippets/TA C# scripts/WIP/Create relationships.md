```C#
// ============================================================================
// AUTO-DETECT & CREATE RELATIONSHIPS
// Scans all tables for columns ending in common key suffixes (Key, ID, SK)
// and automatically creates Many-to-One relationships between fact and
// dimension tables.
//
// Compatible with: Tabular Editor 2 (default compiler) & Tabular Editor 3
// NOTE: No local functions used — TE2's compiler does not support them.
//       All logic is inlined using loops and variables.
// ============================================================================


// ============================================================================
// CONFIGURATION — edit these values
// ============================================================================

var keySuffixes       = new List<string> { "Key", "ID", "SK" };
var dimensionPrefixes = new List<string> { "Dim", "Lkp", "Ref", "Lookup" };
var excludeFromTables = new List<string> { };   // table names to never use as the many-side

var dryRun          = false;   // true = preview only, false = apply changes
var crossFilterBoth = false;  // false = SingleDirection, true = BothDirections
var skipExisting    = true;   // skip pairs that already have a relationship


// ============================================================================
// SCAN — build a dictionary: keyRoot -> list of columns that carry that key
// ============================================================================

var dimCandidates = new Dictionary<string, List<Column>>(StringComparer.OrdinalIgnoreCase);

foreach (var table in Model.Tables) {
    if (table.GetType().Name == "CalculationGroupTable") continue;

    foreach (var col in table.Columns) {
        if (col.Type == ColumnType.RowNumber) continue;

        var colMatchesSuffix = false;
        var colRoot          = col.Name;
        foreach (var sfx in keySuffixes) {
            if (col.Name.Length > sfx.Length &&
                col.Name.EndsWith(sfx, StringComparison.OrdinalIgnoreCase)) {
                colMatchesSuffix = true;
                colRoot = col.Name.Substring(0, col.Name.Length - sfx.Length);
                break;
            }
        }
        if (!colMatchesSuffix) continue;

        if (!dimCandidates.ContainsKey(colRoot))
            dimCandidates[colRoot] = new List<Column>();

        dimCandidates[colRoot].Add(col);
    }
}


// ============================================================================
// MATCH & CREATE
// ============================================================================

var created  = new List<string>();
var skipped  = new List<string>();
var warnings = new List<string>();

foreach (var table in Model.Tables) {
    if (table.GetType().Name == "CalculationGroupTable") continue;

    var tableExcluded = false;
    foreach (var ex in excludeFromTables) {
        if (string.Equals(ex, table.Name, StringComparison.OrdinalIgnoreCase)) {
            tableExcluded = true;
            break;
        }
    }
    if (tableExcluded) continue;

    foreach (var col in table.Columns) {
        if (col.Type == ColumnType.RowNumber) continue;

        var colMatchesSuffix = false;
        var colRoot          = col.Name;
        foreach (var sfx in keySuffixes) {
            if (col.Name.Length > sfx.Length &&
                col.Name.EndsWith(sfx, StringComparison.OrdinalIgnoreCase)) {
                colMatchesSuffix = true;
                colRoot = col.Name.Substring(0, col.Name.Length - sfx.Length);
                break;
            }
        }
        if (!colMatchesSuffix) continue;
        if (!dimCandidates.ContainsKey(colRoot)) continue;

        // Gather candidates from OTHER tables only
        var candidates = new List<Column>();
        foreach (var c in dimCandidates[colRoot]) {
            if (c.Table != table) candidates.Add(c);
        }
        if (candidates.Count == 0) continue;

        // Prefer dim-prefixed tables; within same priority sort alphabetically
        Column oneSideDim      = null;
        Column oneSideFallback = null;

        foreach (var c in candidates) {
            var isDim = false;
            foreach (var pfx in dimensionPrefixes) {
                if (c.Table.Name.StartsWith(pfx, StringComparison.OrdinalIgnoreCase)) {
                    isDim = true;
                    break;
                }
            }
            if (isDim) {
                if (oneSideDim == null ||
                    string.Compare(c.Table.Name, oneSideDim.Table.Name,
                                   StringComparison.OrdinalIgnoreCase) < 0)
                    oneSideDim = c;
            } else {
                if (oneSideFallback == null ||
                    string.Compare(c.Table.Name, oneSideFallback.Table.Name,
                                   StringComparison.OrdinalIgnoreCase) < 0)
                    oneSideFallback = c;
            }
        }

        var oneSide = (oneSideDim != null) ? oneSideDim : oneSideFallback;
        if (oneSide == null) continue;

        var manySide = col;
        var relLabel = table.Name + "[" + col.Name + "] -> " +
                       oneSide.Table.Name + "[" + oneSide.Name + "]";

        // Guard: self-join
        if (manySide.Table == oneSide.Table) {
            warnings.Add("SELF-JOIN skipped : " + relLabel);
            continue;
        }

        // Guard: dim -> dim (likely unintentional)
        var fromIsDim = false;
        var toIsDim   = false;
        foreach (var pfx in dimensionPrefixes) {
            if (table.Name.StartsWith(pfx, StringComparison.OrdinalIgnoreCase))
                fromIsDim = true;
            if (oneSide.Table.Name.StartsWith(pfx, StringComparison.OrdinalIgnoreCase))
                toIsDim = true;
        }
        if (fromIsDim && toIsDim) {
            warnings.Add("DIM->DIM skipped  : " + relLabel);
            continue;
        }

        // Guard: relationship already exists in either direction
        if (skipExisting) {
            var alreadyExists = false;
            foreach (var rel in Model.Relationships) {
                if ((rel.FromColumn == manySide && rel.ToColumn == oneSide) ||
                    (rel.FromColumn == oneSide  && rel.ToColumn == manySide)) {
                    alreadyExists = true;
                    break;
                }
            }
            if (alreadyExists) {
                skipped.Add("EXISTS   : " + relLabel);
                continue;
            }
        }

        // Create or log
        if (dryRun) {
            created.Add("DRY-RUN  : " + relLabel);
        } else {
            try {
                var newRel = Model.AddRelationship();
                newRel.FromColumn = manySide;
                newRel.ToColumn   = oneSide;
                newRel.FromCardinality = RelationshipEndCardinality.Many;
                newRel.ToCardinality   = RelationshipEndCardinality.One;
                newRel.CrossFilteringBehavior = crossFilterBoth
                    ? CrossFilteringBehavior.BothDirections
                    : CrossFilteringBehavior.OneDirection;
                newRel.IsActive = true;
                newRel.SecurityFilteringBehavior = SecurityFilteringBehavior.OneDirection;
                created.Add("CREATED  : " + relLabel);
            } catch (Exception ex) {
                warnings.Add("ERROR    : " + relLabel + " -- " + ex.Message);
            }
        }
    }
}


// ============================================================================
// SUMMARY REPORT
// ============================================================================

var sb = new System.Text.StringBuilder();
sb.AppendLine("=================================================");
sb.AppendLine(dryRun
    ? "  AUTO-RELATIONSHIP  [DRY-RUN - no changes saved]"
    : "  AUTO-RELATIONSHIP  [LIVE - relationships created]");
sb.AppendLine("=================================================");
sb.AppendLine();
sb.AppendLine("Suffixes scanned : " + string.Join(", ", keySuffixes.ToArray()));
sb.AppendLine("Cross-filter     : " + (crossFilterBoth ? "BothDirections" : "SingleDirection"));
sb.AppendLine("Tables in model  : " + Model.Tables.Count());
sb.AppendLine();

if (created.Count > 0) {
    sb.AppendLine("-- " + (dryRun ? "WOULD CREATE" : "CREATED") + " (" + created.Count + ") --");
    foreach (var r in created) sb.AppendLine("  " + r);
    sb.AppendLine();
}
if (skipped.Count > 0) {
    sb.AppendLine("-- SKIPPED / already exist (" + skipped.Count + ") --");
    foreach (var r in skipped) sb.AppendLine("  " + r);
    sb.AppendLine();
}
if (warnings.Count > 0) {
    sb.AppendLine("-- WARNINGS (" + warnings.Count + ") --");
    foreach (var r in warnings) sb.AppendLine("  " + r);
    sb.AppendLine();
}

sb.AppendLine("=================================================");
sb.AppendLine("Total: " + created.Count + " " + (dryRun ? "proposed" : "created") +
              " | " + skipped.Count + " skipped | " + warnings.Count + " warnings");
sb.AppendLine("=================================================");

if (dryRun) {
    sb.AppendLine();
    sb.AppendLine("-> Set  dryRun = false  at the top to apply changes.");
}

Info(sb.ToString());

```