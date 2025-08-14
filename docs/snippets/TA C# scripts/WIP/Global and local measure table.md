// ************ CREATE MEASURE TABLE **************

// Create a calculated table with a single column that is hidden:
var measureTable = Model.AddCalculatedTable("Global Measures", "{0}");
measureTable.Columns[0].IsHidden = true;

// Add a dummy measure to the "Measures" folder:
var newMeasure = measureTable.AddMeasure(
    "Dummy Measure",   // Measure Name
    " ",               // DAX expression
    "Measures"         // Display Folder
);

// Add a local measure to the "Local Measures" folder:
var newMeasure2 = measureTable.AddMeasure(
    "Local Measure",   // Measure Name
    " ",               // DAX expression
    "Local Measures"   // Display Folder
);