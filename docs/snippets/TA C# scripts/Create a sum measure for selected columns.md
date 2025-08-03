# Creating SUM measures for selected columns

This script will create a SUM measure for each selected column in the Tabular Editor window. Change the formatting string and then put it in a display folder in the current table. This is extremely useful when you have many columns to create this measures for.

## Script

```C# title="Create a SUM measure for each selected column" linenums="1"
// Loop through the list of selected columns
foreach(var c in Selected.Columns)
{
    var newMeasure = c.Table.AddMeasure(
        "Sum of " + c.Name,                    // Name
        "SUM(" + c.DaxObjectFullName + ")",    // DAX expression
        c.DisplayFolder                        // Display Folder
    );
    
    // Set the format string on the new measure:
    newMeasure.FormatString = "0";

    // Provide some documentation:
    newMeasure.Description = "This measure is the sum of column " + c.DaxObjectFullName;

    // Create all measures within a Named Folder
    newMeasure.DisplayFolder = "_Model";

    // Hide the base column:
    c.IsHidden = true;
}
```

## Credits

Author: [Mike Carlo](https://powerbi.tips)