[source](https://pawarbi.github.io/blog/powerbi/tabulareditor/2022/08/30/adding-prefix-to-selected-columns-powerbi.html)

//1. Change the prefix below, notice the space at the end.
//2. Select all the columns to rename

var prefix = "Effective ";

foreach (var col in Selected.Columns)
    {

        col.Name = prefix + col.Name;
    }