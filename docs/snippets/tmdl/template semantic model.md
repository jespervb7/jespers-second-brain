# Template Semantic model

```tmdl title="Template Semantic model TMDL Script" linenums="1"
createOrReplace

	/// This is a template description of the model. Remind a developer to edit this.
	model Model
		culture: en-US
		defaultPowerBIDataSourceVersion: powerBI_V3
		discourageImplicitMeasures
		sourceQueryCulture: en-GB
		dataAccessOptions
			legacyRedirects
			returnErrorValuesAsNull

		/// This table contains information about the last time the semantic model/report was refreshed.
		table 'Last refresh'
			isHidden

			/// Contains the date and time the report was refreshed.
			column Datetime
				dataType: dateTime
				isHidden
				isUnique
				formatString: dd/mm/yyyy hh:nn:ss
				isAvailableInMdx: false
				displayFolder: 01. Columns
				summarizeBy: none
				sourceColumn: Datetime

				changedProperty = IsHidden

				annotation SummarizationSetBy = Automatic

				annotation PBI_FormatHint = {"isDateTimeCustom":true}

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["DATECOLUMN_FORMATSTRING"]}

			/// Contains the date when the report was refreshed.
			column Date
				dataType: dateTime
				isHidden
				isUnique
				formatString: d mmmm
				isAvailableInMdx: false
				displayFolder: 01. Columns
				summarizeBy: none
				sourceColumn: Date

				changedProperty = IsHidden

				annotation SummarizationSetBy = Automatic

				annotation UnderlyingDateTimeDataType = Date

				annotation PBI_FormatHint = {"isDateTimeCustom":true}

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["DATECOLUMN_FORMATSTRING"]}

			/// Contains the time when the report was refreshed.
			column Time
				dataType: dateTime
				isHidden
				isUnique
				formatString: Short Time
				isAvailableInMdx: false
				displayFolder: 01. Columns
				summarizeBy: none
				sourceColumn: Time

				changedProperty = IsHidden

				annotation SummarizationSetBy = Automatic

				annotation UnderlyingDateTimeDataType = Time

			partition 'Last refresh' = m
				mode: import
				source = 
						let
							Source = DateTime.LocalNow(),
							#"Converted to Table" = #table(1, {{Source}}),
							#"Renamed Columns" = Table.RenameColumns(#"Converted to Table", {{"Column1", "Datetime"}}),
							#"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns", {{"Datetime", type datetime}}),
							#"Duplicated Column" = Table.DuplicateColumn(#"Changed Type", "Datetime", "Datetime - Copy"),
							#"Extracted Date" = Table.TransformColumns(#"Duplicated Column", {{"Datetime - Copy", DateTime.Date, type date}}),
							#"Duplicated Column1" = Table.DuplicateColumn(#"Extracted Date", "Datetime", "Datetime - Copy.1"),
							#"Changed Type1" = Table.TransformColumnTypes(#"Duplicated Column1", {{"Datetime - Copy.1", type time}}),
							#"Renamed Columns1" = Table.RenameColumns(
								#"Changed Type1", {{"Datetime - Copy", "Date"}, {"Datetime - Copy.1", "Time"}}
							)
						in
							#"Renamed Columns1"

			changedProperty = IsHidden

			annotation PBI_NavigationStepName = Navigation

			annotation PBI_ResultType = Table

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

		/// This table contains documentaiton of the mode. Included are measures, relationships, tables and columns.
		table 'Model documentation'
			isHidden

			/// Type of the documentation. Is it a measure, column, table or relationship.
			column Type
				isHidden
				isAvailableInMdx: false
				displayFolder: 01.Columns
				summarizeBy: none
				isNameInferred
				sourceColumn: [Type]

				annotation SummarizationSetBy = Automatic

			/// The name of the object in the model.
			column Name
				isHidden
				isAvailableInMdx: false
				displayFolder: 01.Columns
				summarizeBy: none
				isNameInferred
				sourceColumn: [Name]

				annotation SummarizationSetBy = Automatic

			/// Contains the description of the object.
			column Description
				isHidden
				isAvailableInMdx: false
				displayFolder: 01.Columns
				summarizeBy: none
				isNameInferred
				sourceColumn: [Description]

				annotation SummarizationSetBy = Automatic

			/// The location, read table, the object resides.
			column Location
				isHidden
				isAvailableInMdx: false
				displayFolder: 01.Columns
				summarizeBy: none
				isNameInferred
				sourceColumn: [Location]

				annotation SummarizationSetBy = Automatic

			/// The dax expression of the object that was used to create it.
			column Expression
				isHidden
				isAvailableInMdx: false
				displayFolder: 01.Columns
				summarizeBy: none
				isNameInferred
				sourceColumn: [Expression]

				annotation SummarizationSetBy = Automatic

			partition 'Model documentation' = calculated
				mode: import
				source = ```

						VAR _columns =
						    SELECTCOLUMNS (
						        FILTER (
						            INFO.VIEW.COLUMNS (),
						            [Table] <> "Data Dictionary"
						                && NOT ( [IsHidden] )
						        ),
						        "Type", "Column",
						        "Name", [Name],
						        "Description", [Description],
						        "Location", [Table],
						        "Expression", [Expression]
						    )
						VAR _measures =
						    SELECTCOLUMNS (
						        FILTER (
						            INFO.VIEW.MEASURES (),
						            [Table] <> "Data Dictionary"
						                && NOT ( [IsHidden] )
						        ),
						        "Type", "Measure",
						        "Name", [Name],
						        "Description", [Description],
						        "Location", [Table],
						        "Expression", [Expression]
						    )
						VAR _tables =
						    SELECTCOLUMNS (
						        FILTER (
						            INFO.VIEW.TABLES (),
						            [Name] <> "Data Dictionary"
						                && [Name] <> "Calculations"
						                && NOT ( [IsHidden] )
						        ),
						        "Type", "Table",
						        "Name", [Name],
						        "Description", [Description],
						        "Location", BLANK (),
						        "Expression", [Expression]
						    )
						VAR _relationships =
						    SELECTCOLUMNS (
						        INFO.VIEW.RELATIONSHIPS (),
						        "Type", "Relationship",
						        "Name", [Relationship],
						        "Description", BLANK (),
						        "Location", BLANK (),
						        "Expression", [Relationship]
						    )
						RETURN
						    UNION ( _columns, _measures, _tables, _relationships )
					```

			annotation PBI_Id = 094b4e21181944b38595aeedaacdbfa2

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["REDUCE_USAGE_OF_CALCULATED_TABLES","ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

		cultureInfo en-US
			translations

				model Model
					table 'Last refresh'
						caption: Last refresh
						description: This table contains information about the last time the semantic model/report was refreshed.
						column Datetime
							caption: Datetime
							description: Contains the date and time the report was refreshed.
							displayFolder: 01. Columns
						column Date
							caption: Date
							description: Contains the date when the report was refreshed.
							displayFolder: 01. Columns
						column Time
							caption: Time
							description: Contains the time when the report was refreshed.
							displayFolder: 01. Columns
					table 'Model documentation'
						caption: Model documentation
						description: This table contains documentaiton of the mode. Included are measures, relationships, tables and columns.
						column Type
							caption: Type
							description: Type of the documentation. Is it a measure, column, table or relationship.
							displayFolder: 01. Columns
						column Name
							caption: Name
							description: The name of the object in the model.
							displayFolder: 01. Columns
						column Description
							caption: Description
							description: Contains the description of the object.
							displayFolder: 01. Columns
						column Location
							caption: Location
							description: The location, read table, the object resides.
							displayFolder: 01. Columns
						column Expression
							caption: Expression
							description: The dax expression of the object that was used to create it.
							displayFolder: 01. Columns

			linguisticMetadata = 
					{
					  "Version": "1.0.0",
					  "Language": "en-US"
					}
				contentType: json

		cultureInfo nl-NL
			translations

				model Model
					table 'Last refresh'
						caption: Laatste verversing
						description: Deze tabel bevat informatie over het laatste moment dat dit semantisch model/report is ververst.
						column Datetime
							caption: Datumtijd
							description: Bevat de datum en tijd dat dit report was ververst.
							displayFolder: 01. Kolommen
						column Date
							caption: Datum
							description: Bevat de datum wanneer dit report is ververst.
							displayFolder: 01. Kolommen
						column Time
							caption: Tijd
							description: Geeft de tijd aan waarop dit report is ververst.
							displayFolder: 01. Kolommen
					table 'Model documentation'
						caption: Model documentatie
						column Type
							caption: Type
							description: Type van de documentatie, is dit een meting, kolom, tabel of relatie.
							displayFolder: 01. Kolommen
						column Name
							caption: Naam
							description: De naam van het object in het model.
							displayFolder: 01. Kolommen
						column Description
							caption: Beschrijving
							description: Bevat de beschrijving van het object.
							displayFolder: 01. Kolommen
						column Location
							caption: Locatie
							description: De locatie, lees tabel, waar het object zit.
							displayFolder: 01. Kolommen
						column Expression
							caption: Expressie
							description: De dax expressie van het object dat gebruikt is om het te maken.
							displayFolder: 01. Kolommen

		annotation __PBI_TimeIntelligenceEnabled = 0

		annotation PBIDesktopVersion = 2.144.679.0 (25.06)+ef40146207b86a826d1c72a7727a743ee37450e9

		annotation PBI_QueryOrder = ["Last refresh"]

		annotation __TEdtr = 1

		annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

		annotation PBI_ProTooling = ["TMDLView_Desktop"]
```