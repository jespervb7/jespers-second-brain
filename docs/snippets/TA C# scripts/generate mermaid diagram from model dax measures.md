# Output DAX measure dependancies to mermaid diagram code

Make sure to save as model macro.

```C# title="C# Macro - outputs mermaid diagram code for all DAX measures" linenums="1"
string dependancies = "flowchart LR\n%% Measure dependancy mermaid flowchart";

foreach(var _measures in Model.AllMeasures )         
    {

    var _upstream           = _measures.DependsOn;
    var _upstream_measures  = _upstream.Measures.OfType<Measure>().Select(c => c).Distinct();

    dependancies += string.Format("\r\n\n%% [{1}] Dependancies:\n\t{0}[\"{1}\"]",

        // 0 - Lineage tag of the measure in scope
        _measures.LineageTag, 

        // 1 - Name of the measure in scope
        _measures.Name);

    foreach( var measure_dependencies in _upstream_measures )
        { 
        dependancies += string.Format("\r\n\t{2}[\"{3}\"] --> {0}[\"{1}\"]",
            
            // 0 - Lineage tag of the measure in scope
            _measures.LineageTag, 
            
            // 1 - Name of the measure in scope
            _measures.Name,
            
            // 2 - Lineage tag of the dependant measure
            measure_dependencies.LineageTag,      
            
            // 3 - Name of the dependant measure
            measure_dependencies.Name);
        }
    }

dependancies.Output();
```