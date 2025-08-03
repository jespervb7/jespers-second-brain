# Programmatically format all DAX expressions

[source](https://data-goblins.com/power-bi/format-all-dax/#format-all-things=)

```C# title="C# Macro - formats all DAX expressions through API calls to daxformatter.com" linenums="1"
// ----------------------------- MEASURES --------------------------------------- //

// Format all DAX measures in the model
var _measures = Model.AllMeasures;
_measures.FormatDax();

// Count & report in an info box the # measures formatted


// ----------------------------- CALCULATION GROUPS ----------------------------- //

// Loop through calculation groups & items, counting and formatting them
int _counter = 0;
foreach (  var _calcgroup  in Model.CalculationGroups )
{
 foreach (  var _item  in _calcgroup.CalculationItems )
 {
 _counter = _counter + 1;
 _item.Expression = "\n" + FormatDax( _item.Expression, shortFormat: true );
 }
}


// ----------------------------- CALCULATED TABLES ----------------------------- //

// Loop through calculated tables in the model and format their DAX expressions
foreach (  var _tables  in Model.Tables )
{
 if ( Convert.ToString( _tables.Columns[0].Type ) == "CalculatedTableColumn")
 {
 _tables.Partitions[0].Expression = "\n" + FormatDax( _tables.Partitions[0].Expression, shortFormat: true );
 }
}

// Count & report in an info box the # Calculated Tables formatted
int _nrtables = Model.Tables.Where(_tables => Convert.ToString(_tables.Columns[0].Type) == "CalculatedTableColumn").Count();


// ----------------------------- CALCULATED COLUMNS ----------------------------- //

// Loop through calculated columns in the model and format their DAX expressions
foreach (  var _columns  in Model.AllColumns )
{
 if ( Convert.ToString( _columns.Type ) == "Calculated")
 {
 (_columns as CalculatedColumn).Expression = "\n" + FormatDax( (_columns as CalculatedColumn).Expression, shortFormat: true );
 }
}

// Count the # Calculated Columns formatted
int _nrcolumns = Model.AllColumns.Where(_columns => Convert.ToString(_columns.Type) == "Calculated").Count();




// Return an info box counting what was formatted
Info(   "Formatted all the things! \n\n" +
        Convert.ToString(_measures.Count()) + " measures,\n" + 
        Convert.ToString( _counter ) + " calculation items, \n" + 
        Convert.ToString( _nrtables ) + " calculated tables &\n" + 
        Convert.ToString( _nrcolumns ) + " calculated columns." );
```