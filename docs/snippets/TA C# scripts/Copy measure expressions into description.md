# Copy measure expression (DAX) into object description

```C# title="Adding expressions of measure to object description" linenums="1"
foreach(var m in Model.AllMeasures)
{
    if(m.Description == "")
    {    
        m.Description =  "Expression:" + "\n" + m.Expression;
    }
    else if (!m.Description.Contains("Expression"))
    {
        m.Description = m.Description + "\n" + "Expression:" + "\n" + m.Expression;
    }
    else
    {
        // '2021-07-05 / B.Agullo / reset expressions already added
        int pos = m.Description.IndexOf("Expression",0); 
        bool onlyExpression = (pos == 0);
        
        if (onlyExpression) {
            m.Description = "Expression:" + "\n" + m.Expression;
        } else {
            m.Description = m.Description.Substring(0,pos-1)  + "\n" + "Expression:" + "\n" + m.Expression;
        }
    }
}

Model.AllMeasures.FormatDax();
```