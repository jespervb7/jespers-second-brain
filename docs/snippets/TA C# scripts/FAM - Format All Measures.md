# Programmatically format all DAX measures

[source](https://data-goblins.com/power-bi/format-all-dax/#format-all-things=)

```C# title="C# Macro - formats all DAX measures through API calls to daxformatter.com" linenums="1"
// Format all DAX measures in the model
var _measures = Model.AllMeasures;
foreach (  var m  in _measures )
{
 m.FormatDax();
 m.Expression =  "\n" + m.Expression;
}

// Count & report in an info box the # measures formatted
Info(  "Formatted DAX for " + Convert.ToString(_measures.Count()) +  " measures." );
```