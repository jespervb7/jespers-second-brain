# Date dimension

## Snippet

```tmdl title="Date dimension TMDL Script" linenums="1"
createOrReplace
 
	/// This table contains date related fields that connect to the business activity you are trying to analyse.
	table 'Dim Date'
 
		dataCategory: Time
 
		/// This column contains the date related to a business activity in the DD/MM/YYYY format.
		column Date
			dataType: dateTime
			formatString: Short Date
			displayFolder: 01. Columns
 
			isDefaultLabel
			summarizeBy: none
			sourceColumn: Date
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["DATECOLUMN_FORMATSTRING"]}
 
		/// This columns tells you what year the business acitivity occured in. For example: 2024, 2025, etc.
		column Year
			dataType: int64
			formatString: 0
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Year
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the start date of the year related to the business acitivity.
		column 'Start of year'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 04. Start off
 
			summarizeBy: none
			sourceColumn: Start of year
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
		/// This column contains the end date of the year related to the business acitivity.
		column 'End of year'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 05. End off
 
			summarizeBy: none
			sourceColumn: End of year
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
		/// This column says what month of the year the business activity occured in.
		column 'Month of year'
			dataType: int64
			isHidden
			formatString: 0
			displayFolder: 07. Other
 
			summarizeBy: none
			sourceColumn: Month of year
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the start date of the month related to the business acitivity.
		column 'Start of month'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 04. Start off
 
			summarizeBy: none
			sourceColumn: Start of month
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["MONTHCOLUMN_FORMATSTRING"]}
 
		/// This column contains the end date of the month related to the business acitivity.
		column 'End of month'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 05. End off
 
			summarizeBy: none
			sourceColumn: End of month
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["MONTHCOLUMN_FORMATSTRING"]}
 
		/// This column gives you the amount of days the month has that the business acitivity occured in. For example if this activity happend in May then the number you get is 31.
		column 'Days in month'
			dataType: int64
			formatString: 0
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Days in month
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the name of the month that the business activity happend in. Example data: January, February, March, etc.
		column 'Month name'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Month name
			sortByColumn: 'Month of year'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column says what quarter of the year the business activity occured in.
		column 'Quarter of year'
			dataType: int64
			isHidden
			formatString: 0
			displayFolder: 07. Other
 
			summarizeBy: none
			sourceColumn: Quarter of year
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the start date of the quarter related to the business acitivity.
		column 'Start of quarter'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 04. Start off
 
			summarizeBy: none
			sourceColumn: Start of quarter
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
		/// This column contains the end date of the quarter related to the business acitivity.
		column 'End of quarter'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 05. End off
 
			summarizeBy: none
			sourceColumn: End of quarter
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
		/// This column says what week of the year the business activity occured in.
		column 'Week of year'
			dataType: int64
			isHidden
			formatString: 0
			displayFolder: 07. Other
 
			summarizeBy: none
			sourceColumn: Week of year
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the week of the current month that is related to the business activity.
		column 'Week of month'
			dataType: int64
			formatString: 0
			displayFolder: 03. Day-week off
 
			summarizeBy: none
			sourceColumn: Week of month
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the start date of the week related to the business acitivity.
		column 'Start of week'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 04. Start off
 
			summarizeBy: none
			sourceColumn: Start of week
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
		/// This column contains the end date of the week related to the business acitivity.
		column 'End of week'
			dataType: dateTime
			formatString: Short Date
			displayFolder: 05. End off
 
			summarizeBy: none
			sourceColumn: End of week
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
		/// This column gives you the day of the current month that is related to the business activity.
		column 'Day of month'
			dataType: int64
			formatString: 0
			displayFolder: 03. Day-week off
 
			summarizeBy: none
			sourceColumn: Day of month
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the day of the current year that is related to the business activity.
		column 'Day of year'
			dataType: int64
			formatString: 0
			displayFolder: 03. Day-week off
 
			summarizeBy: none
			sourceColumn: Day of year
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the day of the current week that is related to the business activity.
		column 'Day of week'
			dataType: int64
			displayFolder: 03. Day-week off
 
			summarizeBy: none
			sourceColumn: Day of week
 
			annotation SummarizationSetBy = Automatic
 
			annotation PBI_FormatHint = {"isGeneralNumber":true}
 
		/// This column contains the name of the day a business event happend. Examples are: "Monday", "Tuesday", etc.
		column 'Day name'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Day name
			sortByColumn: 'Day of week'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the day of the current quarter that is related to the business activity.
		column 'Day of quarter'
			dataType: int64
			formatString: 0
			displayFolder: 03. Day-week off
 
			summarizeBy: none
			sourceColumn: Day of quarter
 
			annotation SummarizationSetBy = Automatic
 
		/// This column is specifically made to be used as a sort-by column. Notify the builder of the report if you can see this column as it should be hidden from end-users.
		column 'Year quarter sorting'
			dataType: int64
			isHidden
			formatString: 0
			displayFolder: 06. Sorting columns
 
			summarizeBy: none
			sourceColumn: Year quarter sorting
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the offset by day from today.
		column 'Day offset'
			dataType: int64
			formatString: 0
			displayFolder: 02. Offsets to today
 
			summarizeBy: none
			sourceColumn: Day offset
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the name of the day a business event happend. Examples are: "Mon", "Tue", etc.
		column 'Day name short'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Day name short
			sortByColumn: 'Day of week'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column is specifically made to be used as a sort-by column. Notify the builder of the report if you can see this column as it should be hidden from end-users.
		column 'Year month sorting'
			dataType: int64
			isHidden
			formatString: 0
			displayFolder: 06. Sorting columns
 
			summarizeBy: none
			sourceColumn: Year month sorting
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
		/// This column is specifically made to be used as a sort-by column. Notify the builder of the report if you can see this column as it should be hidden from end-users.
		column 'Year week sorting'
			dataType: int64
			isHidden
			formatString: 0
			displayFolder: 06. Sorting columns
 
			summarizeBy: none
			sourceColumn: Year week sorting
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the name of the week that the related business acitivty occurred in. Example data: W1, W2, ..., W53.
		column 'Week name'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Week name
			sortByColumn: 'Week of year'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the week and year that the business acitivity occurred in. Example data: W1 1991, W50 1991, W53 2025.
		column 'Week and year'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Week and year
			sortByColumn: 'Year week sorting'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the short name of the month that the business activity happend in. Example data: Jan, Feb, Mar, etc.
		column 'Month name short'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Month name short
			sortByColumn: 'Month of year'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the month and year that the business acitivity occurred in. Example data: Jan 2025, Feb 2025, Oct 2030, etc.
		column 'Month and year'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Month and year
			sortByColumn: 'Year month sorting'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the name of the quarter that a business activity happend in. Example data: Quarter 1, Quarter 2, etc.
		column 'Quarter name'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Quarter name
			sortByColumn: 'Quarter of year'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the short name of the quarter that a business activity happend in. Example data: Q1, Q2, Q3 and Q4.
		column 'Quarter name short'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Quarter name short
			sortByColumn: 'Quarter of year'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column gives you the quarter and year that the business acitivity occurred in. Example data: Q1 2025, Q2, 2025, etc.
		column 'Quarter and year'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Quarter and year
			sortByColumn: 'Year quarter sorting'
 
			changedProperty = SortByColumn
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the offset by year from today. So if the current year would be 2025 then 2024 would be -1 and 2026 would be 1.
		column 'Year offset'
			dataType: int64
			formatString: 0
			displayFolder: 02. Offsets to today
 
			summarizeBy: none
			sourceColumn: Year offset
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the offset by quarter from today.
		column 'Quarter offset'
			dataType: int64
			formatString: 0
			displayFolder: 02. Offsets to today
 
			summarizeBy: none
			sourceColumn: Quarter offset
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the offset by month from today.
		column 'Month offset'
			dataType: int64
			formatString: 0
			displayFolder: 02. Offsets to today
 
			summarizeBy: none
			sourceColumn: Month offset
 
			annotation SummarizationSetBy = Automatic
 
		/// This column contains the offset by week from today. So if the current week would be 2025 week 15  then 2025 week 14 would be -1 and 2025 week 16 would be 1.
		column 'Week offset'
			dataType: int64
			formatString: 0
			displayFolder: 02. Offsets to today
 
			summarizeBy: none
			sourceColumn: Week offset
 
			annotation SummarizationSetBy = Automatic
 
		/// This column tells you if the business acitivity is in the present, past or future.
		column 'Date period'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Date period
 
			annotation SummarizationSetBy = Automatic
 
		/// This column says if the business activity happend in a weekday.
		column 'Is a weekday'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Is a weekday
 
			annotation SummarizationSetBy = Automatic
 
		/// This column says if the business activity happend in the weekend.
		column 'Is a weekend'
			dataType: string
			displayFolder: 01. Columns
 
			summarizeBy: none
			sourceColumn: Is a weekend
 
			annotation SummarizationSetBy = Automatic
 
		/// This column is used to create a relationship between fact tables and their dates. This then allows you to use this table to slice and dice.
		column 'Date ID'
			dataType: dateTime
			isHidden
			isKey
			formatString: Short Date
			isAvailableInMdx: false
			displayFolder: 07. Other
 
			summarizeBy: none
			sourceColumn: Date ID
 
			changedProperty = IsHidden
 
			annotation SummarizationSetBy = Automatic
 
			annotation UnderlyingDateTimeDataType = Date
 
			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["DATECOLUMN_FORMATSTRING"]}
 
		/// This hierarchy contains the year -> quarter -> month -> week -> date of the business activity.
		hierarchy '00. Hierarchy'
 
 
			level Year
 
				column: Year
 
			level 'Quarter name short'
 
				column: 'Quarter name short'
 
			level 'Month name short'
 
				column: 'Month name short'
 
			level 'Week name'
 
				column: 'Week name'
 
			level Date
 
				column: Date
 
		partition 'Dim Date' = m
			mode: import
			source =
					let
					    Source = "",
					    Today = Date.From(DateTime.LocalNow()),
					    FromYear = 1900,
					    ToYear = 2100,
					    FromDate = #date(FromYear,1,1),
					    ToDate = #date(ToYear,12,31),
					    ListOfDates = List.Dates(
					        FromDate,
					        Duration.Days(ToDate-FromDate)+1,
					        #duration(1,0,0,0)
					    ),
					    #"Converted to Date Table" = Table.FromList(ListOfDates, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
					    #"Changed Type" = Table.TransformColumnTypes(#"Converted to Date Table",{{"Column1", type date}}),
					    #"Renamed Columns" = Table.RenameColumns(#"Changed Type",{{"Column1", "Date"}}),
					    #"Inserted Age" = Table.AddColumn(#"Renamed Columns", "Day offset", each [Date] - Date.From(DateTime.LocalNow()), type duration),
					    #"Inserted Year" = Table.AddColumn(#"Inserted Age", "Year", each Date.Year([Date]), Int64.Type),
					    #"Inserted Start of Year" = Table.AddColumn(#"Inserted Year", "Start of Year", each Date.StartOfYear([Date]), type date),
					    #"Inserted End of Year" = Table.AddColumn(#"Inserted Start of Year", "End of Year", each Date.EndOfYear([Date]), type date),
					    #"Inserted Month" = Table.AddColumn(#"Inserted End of Year", "Month", each Date.Month([Date]), Int64.Type),
					    #"Inserted Start of Month" = Table.AddColumn(#"Inserted Month", "Start of Month", each Date.StartOfMonth([Date]), type date),
					    #"Inserted End of Month" = Table.AddColumn(#"Inserted Start of Month", "End of Month", each Date.EndOfMonth([Date]), type date),
					    #"Inserted Days in Month" = Table.AddColumn(#"Inserted End of Month", "Days in Month", each Date.DaysInMonth([Date]), Int64.Type),
					    #"Inserted Month Name" = Table.AddColumn(#"Inserted Days in Month", "Month Name", each Date.MonthName([Date]), type text),
					    #"Inserted Quarter" = Table.AddColumn(#"Inserted Month Name", "Quarter", each Date.QuarterOfYear([Date]), Int64.Type),
					    #"Inserted Start of Quarter" = Table.AddColumn(#"Inserted Quarter", "Start of Quarter", each Date.StartOfQuarter([Date]), type date),
					    #"Inserted End of Quarter" = Table.AddColumn(#"Inserted Start of Quarter", "End of Quarter", each Date.EndOfQuarter([Date]), type date),
					    #"Inserted Week of Year" = Table.AddColumn(#"Inserted End of Quarter", "Week of Year", each Date.WeekOfYear([Date]), Int64.Type),
					    #"Inserted Week of Month" = Table.AddColumn(#"Inserted Week of Year", "Week of Month", each Date.WeekOfMonth([Date]), Int64.Type),
					    #"Inserted Start of Week" = Table.AddColumn(#"Inserted Week of Month", "Start of Week", each Date.StartOfWeek([Date]), type date),
					    #"Inserted End of Week" = Table.AddColumn(#"Inserted Start of Week", "End of Week", each Date.EndOfWeek([Date]), type date),
					    #"Inserted Day" = Table.AddColumn(#"Inserted End of Week", "Day of month", each Date.Day([Date]), Int64.Type),
					    #"Inserted Day of Week" = Table.AddColumn(#"Inserted Day", "Day of Week", each Date.DayOfWeek([Date]), Int64.Type),
					    #"Inserted Day of Year" = Table.AddColumn(#"Inserted Day of Week", "Day of Year", each Date.DayOfYear([Date]), Int64.Type),
					    #"Added - Day of week" = Table.AddColumn(#"Inserted Day of Year", "Day of week", each [Day of Week] + 1, type number),
					    #"Added - Day name" = Table.AddColumn(#"Added - Day of week", "Day name", each if [Day of week] = 1 then "Monday" else if [Day of week] = 2 then "Tuesday" else if [Day of week] = 3 then "Wednesday" else if [Day of week] = 4 then "Thursday" else if [Day of week] = 5 then "Friday" else if [Day of week] = 6 then "Saturday" else if [Day of week] = 7 then "Sunday" else null),
					    #"Added - Day name short" = Table.AddColumn(#"Added - Day name", "Day name short", each if [Day of week] = 1 then "Mon" else if [Day of week] = 2 then "Tue" else if [Day of week] = 3 then "Wed" else if [Day of week] = 4 then "Thu" else if [Day of week] = 5 then "Fri" else if [Day of week] = 6 then "Sat" else if [Day of week] = 7 then "Sun" else null),
					    #"Added - Day of quarter" = Table.AddColumn(#"Added - Day name short", "Day of quarter", each Int16.From([Date] - [Start of Quarter]) + 1),
					    #"Added - Year quarter sorting" = Table.AddColumn(#"Added - Day of quarter", "Year quarter sorting", each Text.Combine({Text.From([Year]), Text.From([Quarter])})),
					    #"Added - Year month sorting" = Table.AddColumn(#"Added - Year quarter sorting", "year month sorting", each [Year]*100 + [Month]),
					    #"Added - Year week sorting" = Table.AddColumn(#"Added - Year month sorting", "Year week sorting", each [Year] * 100 + [Week of Year]),
					    #"Added - Week name" = Table.AddColumn(#"Added - Year week sorting", "Week name", each Text.Combine({"W", Text.From(00 + [Week of Year])})),
					    #"Added - Week and year" = Table.AddColumn(#"Added - Week name", "Week and year", each Text.Combine({[Week name], " ", Text.From([Year])})),
					    #"Added - Month name short" = Table.AddColumn(#"Added - Week and year", "Month name short", each if [Month] = 1 then "Jan" else if [Month] = 2 then "Feb" else if [Month] = 3 then "Mar" else if [Month] = 4 then "Apr" else if [Month] = 5 then "May" else if [Month] = 6 then "Jun" else if [Month] = 7 then "Jul" else if [Month] = 8 then "Aug" else if [Month] = 9 then "Sep" else if [Month] = 10 then "Oct" else if [Month] = 11 then "Nov" else if [Month] = 12 then "Dec" else null),
					    #"Added - Month and year" = Table.AddColumn(#"Added - Month name short", "Month and year", each Text.Combine({[Month name short], " ", Text.From([Year])})),
					    #"Added - Quarter name" = Table.AddColumn(#"Added - Month and year", "Quarter name", each Text.Combine({"Quarter ", Text.From([Quarter])})),
					    #"Added - Qaurter name short" = Table.AddColumn(#"Added - Quarter name", "Quarter name short", each Text.Combine({"Q", Text.From([Quarter])})),
					    #"Added - Quarter and year" = Table.AddColumn(#"Added - Qaurter name short", "Quarter and year", each Text.Combine({[Quarter name short], " ", Text.From([Year])})),
					    #"Added - Year offset" = Table.AddColumn(#"Added - Quarter and year", "Year offset", each [Year] - Date.Year(Date.From(DateTime.LocalNow()))),
					    #"Added - Quarter offset" = Table.AddColumn(#"Added - Year offset", "Quarter offset", each (([Year]-Date.Year(Today))*4)
					+([Quarter]-Date.QuarterOfYear(Today))),
					    #"Added - Month offset" = Table.AddColumn(#"Added - Quarter offset", "Month offset", each (([Year]-Date.Year(Today))*12)
					+([Month]-Date.Month(Today))),
					    #"Added - Week offset" = Table.AddColumn(#"Added - Month offset", "Week offset", each (Date.StartOfWeek([Date])-Date.StartOfWeek(DateTime.Date(DateTime.LocalNow())))/7),
					    #"Added - Date period" = Table.AddColumn(#"Added - Week offset", "Date period", each if [Day offset] < #duration(0, 0, 0, 0) then "Past" else if [Day offset] = #duration(0, 0, 0, 0) then "Present" else if [Day offset] > #duration(0, 0, 0, 0) then "Future" else null),
					    #"Added - Is a weekday" = Table.AddColumn(#"Added - Date period", "Is a weekday", each if [Day name] = "Saturday" then "No" else if [Day name] = "Sunday" then "No" else "Yes"),
					    #"Added - Is a weekend" = Table.AddColumn(#"Added - Is a weekday", "Is a weekend", each if [Is a weekday] = "No" then "Yes" else "No"),
					    #"Added - Date ID" = Table.DuplicateColumn(#"Added - Is a weekend", "Date", "Date ID"),
					    #"Removed unnecessary or duplicate columns" = Table.RemoveColumns(#"Added - Date ID",{"Day of Week"}),
					    #"Changing datetypes" = Table.TransformColumnTypes(#"Removed unnecessary or duplicate columns",{{"Day offset", Int64.Type}, {"Day of week", Int64.Type}, {"Day name", type text}, {"Day name short", type text}, {"Day of quarter", Int64.Type}, {"Year quarter sorting", Int64.Type}, {"year month sorting", Int64.Type}, {"Year week sorting", Int64.Type}, {"Week name", type text}, {"Week and year", type text}, {"Month name short", type text}, {"Month and year", type text}, {"Quarter name", type text}, {"Quarter name short", type text}, {"Quarter and year", type text}, {"Year offset", Int64.Type}, {"Quarter offset", Int64.Type}, {"Month offset", Int64.Type}, {"Week offset", Int64.Type}, {"Date period", type text}, {"Is a weekday", type text}, {"Is a weekend", type text}, {"Date", type date}, {"Start of Year", type date}, {"End of Year", type date}}),
					    #"Reordered Columns" = Table.ReorderColumns(#"Changing datetypes",{"Date ID", "Date", "Day name", "Day name short", "Week name", "Week and year", "Month Name", "Month name short", "Month and year", "Quarter name", "Quarter name short", "Quarter and year", "Date period", "Is a weekday", "Is a weekend", "Year", "Days in Month", "Start of Week", "End of Week", "Start of Month", "End of Month", "Start of Quarter", "End of Quarter", "Start of Year", "End of Year", "Day of week", "Day of month", "Day of quarter", "Day of Year", "Week of Year", "Week of Month", "Month", "Quarter", "Day offset", "Week offset", "Month offset", "Quarter offset", "Year offset", "Year week sorting", "year month sorting", "Year quarter sorting"}),
					    #"Renaming columns" = Table.RenameColumns(#"Reordered Columns",{{"Start of Year", "Start of year"}, {"End of Year", "End of year"}, {"Start of Month", "Start of month"}, {"End of Month", "End of month"}, {"Days in Month", "Days in month"}, {"Month Name", "Month name"}, {"Start of Quarter", "Start of quarter"}, {"End of Quarter", "End of quarter"}, {"Week of Year", "Week of year"}, {"Week of Month", "Week of month"}, {"Start of Week", "Start of week"}, {"End of Week", "End of week"}, {"year month sorting", "Year month sorting"}, {"Day of Year", "Day of year"}, {"Month", "Month of year"}, {"Quarter", "Quarter of year"}})
					in
					    #"Renaming columns"
 
			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["MINIMIZE_POWER_QUERY_TRANSFORMATIONS"]}
 
		annotation PBI_NavigationStepName = Navigation
 
		annotation PBI_ResultType = Table
```

## Design documentation

### What's our goal with this script

The goal is to have created a date table template for Power BI that can be used in SQL as well as non SQL. The reason for this is that we want to create a simple and free solution for others to use.

The SQL template should have an acompanying TMDL script. This is so that we can create consistent descriptions, folders and overall niceness in Power BI without expending crucial developer time.

??? warning "Uncertainty - parameters needed?"

    Figure out if the TMDL scripts needs a parameter table to come with it or not.
    This would be used for the SQL server and database references.
    Can we include this in one TMDL script? Should we include both in 1 script?

#### Requirements

Each script should handle the following requirements

- Handle missing data
  - -1 for missing data
  - -2 for invalid data
- Take a start and end date input
- datekey is either a date or integer value. [This article](https://www.sqlbi.com/articles/choosing-between-date-or-integer-to-represent-dates-in-power-bi-and-tabular/) describes that in Power BI it's unnecessary to have a integer or date column as primary key. Default should be date
- Create the columns that are described in [column definitions](#column-definitions)
- If it's a SQL based

#### Column definitions


| SQL column name           | Description                                                                                                                                                                                                                                                                                                                                                                        | Sample data                                                        | Power BI column name   |
| :-------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------- | ------------------------ |
| date_key                  | This column acts as the primary key of the table. This field is parameterized, some people claim an integer key is a best practice, but in[this article](https://www.sqlbi.com/articles/choosing-between-date-or-integer-to-represent-dates-in-power-bi-and-tabular/) it's described that it's irrelevant what data type it is. Use the one that's best used in the source system. | 20200101, 20210131, 20251231 OR 01-01-2020, 31-01-2021, 31-12-2025 | Date key               |
| <br />short_date          | Used so that it's possible to conform to proper relationships Best Practice Analyzer rules. You can now hide the key column in Power BI and still easily use the date column.                                                                                                                                                                                                      | 01-01-2020, 31-01-2021, 31-12-2025                                 | Short Date             |
| day_name                  |                                                                                                                                                                                                                                                                                                                                                                                    | Monday, Tuesday, Wed                                               | Day name               |
| day_name_short            |                                                                                                                                                                                                                                                                                                                                                                                    | Mon, Tue, Wed                                                      | Day name abbreviated   |
| day_of_year               |                                                                                                                                                                                                                                                                                                                                                                                    | 1,2,3                                                              | Day of year            |
| day_of_quarter            |                                                                                                                                                                                                                                                                                                                                                                                    | 1,2,3                                                              | Day of quarter         |
| day_of_month              |                                                                                                                                                                                                                                                                                                                                                                                    | 1,2,3                                                              | Day of month           |
| day_of_week               |                                                                                                                                                                                                                                                                                                                                                                                    | 1,2,3                                                              | Day of week            |
| season                    |                                                                                                                                                                                                                                                                                                                                                                                    | Fall, Summer, Spring, Winter                                       | Season                 |
| quarter_number_sorting    |                                                                                                                                                                                                                                                                                                                                                                                    | 1 to 12                                                            | Month number sorting   |
| month_number_sorting      |                                                                                                                                                                                                                                                                                                                                                                                    | 1 to 4                                                             | Quarter number sorting |
| week_number_sorting<br /> |                                                                                                                                                                                                                                                                                                                                                                                    | 1 to 52/53                                                         |                        |
| year                      |                                                                                                                                                                                                                                                                                                                                                                                    | 2025, 2026                                                         |                        |
| month_name                |                                                                                                                                                                                                                                                                                                                                                                                    | January, February                                                  |                        |
| month_name_abbreviated    |                                                                                                                                                                                                                                                                                                                                                                                    | Jan, Feb, Mar                                                      |                        |
| quarter_name              |                                                                                                                                                                                                                                                                                                                                                                                    | Quarter 1, Quarter 2, Quarter 3, Quarter 4                         |                        |
| quarter_name_short        |                                                                                                                                                                                                                                                                                                                                                                                    | Q1, Q2, Q3, Q4                                                     |                        |
| year_offset               |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
| quarter_offset            |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
| month_offset              |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
| week_offset               |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
| day_offset                |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
| date_type                 |                                                                                                                                                                                                                                                                                                                                                                                    | Past, Present, Future                                              |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |
|                           |                                                                                                                                                                                                                                                                                                                                                                                    |                                                                    |                        |