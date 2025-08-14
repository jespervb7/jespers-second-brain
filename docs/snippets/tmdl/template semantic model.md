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
			lineageTag: f029ea41-0c5e-413f-aba2-44d62bf581e1

			/// Contains the date and time the report was refreshed.
			column Datetime
				dataType: dateTime
				isHidden
				isUnique
				formatString: dd/mm/yyyy hh:nn:ss
				isAvailableInMdx: false
				displayFolder: 01. Columns
				lineageTag: 266ee585-9638-4bf8-bc6d-d01e9274533f
				summarizeBy: none
				sourceColumn: Datetime

				changedProperty = IsHidden

				annotation SummarizationSetBy = Automatic

				annotation PBI_FormatHint = {"isDateTimeCustom":true}

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["DATECOLUMN_FORMATSTRING","UNNECESSARY_COLUMNS"]}

			/// Contains the date when the report was refreshed.
			column Date
				dataType: dateTime
				isHidden
				isUnique
				formatString: d mmmm
				isAvailableInMdx: false
				displayFolder: 01. Columns
				lineageTag: 33cebe4f-4bd5-4385-bd29-62e0645dd0a2
				summarizeBy: none
				sourceColumn: Date

				changedProperty = IsHidden

				annotation SummarizationSetBy = Automatic

				annotation UnderlyingDateTimeDataType = Date

				annotation PBI_FormatHint = {"isDateTimeCustom":true}

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["DATECOLUMN_FORMATSTRING","UNNECESSARY_COLUMNS"]}

			/// Contains the time when the report was refreshed.
			column Time
				dataType: dateTime
				isHidden
				isUnique
				formatString: Short Time
				isAvailableInMdx: false
				displayFolder: 01. Columns
				lineageTag: 5a9b444f-c003-44ee-bbce-c1bfabccc1c8
				summarizeBy: none
				sourceColumn: Time

				changedProperty = IsHidden

				annotation SummarizationSetBy = Automatic

				annotation UnderlyingDateTimeDataType = Time

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

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
			lineageTag: c2e1096e-4a0a-473d-987e-598df9f641ca

			/// Type of the documentation. Is it a measure, column, table or relationship.
			column Type
				isHidden
				isAvailableInMdx: false
				displayFolder: 01 .Columns
				lineageTag: 6dabf036-849f-4476-9d26-fe76ea737123
				summarizeBy: none
				isNameInferred
				sourceColumn: [Type]

				annotation SummarizationSetBy = Automatic

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			/// The name of the object in the model.
			column Name
				isHidden
				isAvailableInMdx: false
				displayFolder: 01 .Columns
				lineageTag: ce989e71-5832-4ee7-b308-00cbc4f3aa1f
				summarizeBy: none
				isNameInferred
				sourceColumn: [Name]

				annotation SummarizationSetBy = Automatic

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			/// Contains the description of the object.
			column Description
				isHidden
				isAvailableInMdx: false
				displayFolder: 01 .Columns
				lineageTag: e38e6259-b40e-4b5d-aa32-7969c57dad78
				summarizeBy: none
				isNameInferred
				sourceColumn: [Description]

				annotation SummarizationSetBy = Automatic

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			/// The location, read table, the object resides.
			column Location
				isHidden
				isAvailableInMdx: false
				displayFolder: 01 .Columns
				lineageTag: 1f0a7d03-41f7-42ed-8334-721a9737f3f6
				summarizeBy: none
				isNameInferred
				sourceColumn: [Location]

				annotation SummarizationSetBy = Automatic

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			column 'DAX Expression'
				isHidden
				isAvailableInMdx: false
				displayFolder: 01 .Columns
				lineageTag: 5d30fcf1-e76f-401e-85b2-f295ae5cd606
				summarizeBy: none
				isNameInferred
				sourceColumn: [DAX Expression]

				annotation SummarizationSetBy = Automatic

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

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
						            "DAX Expression", [Expression]
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
						            "DAX Expression", [Expression]
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
						            "DAX Expression", [Expression]
						        )
						    VAR _relationships =
						        SELECTCOLUMNS (
						            INFO.VIEW.RELATIONSHIPS (),
						            "Type", "Relationship",
						            "Name", [Relationship],
						            "Description", BLANK (),
						            "Location", BLANK (),
						            "DAX Expression", [Relationship]
						        )
						    RETURN
						        UNION ( _columns, _measures, _tables, _relationships )
						```

			annotation PBI_Id = 094b4e21181944b38595aeedaacdbfa2

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["REDUCE_USAGE_OF_CALCULATED_TABLES","ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

		/// This table contains all global measures. These are measures created by the main semantic model developer.
		table 'Global Measures'
			lineageTag: 8acfc79e-a927-4956-a66f-b2a52ac94782

			measure dummy1
				isHidden
				displayFolder: i. Base measures\Budget;i. Base measures\Sales
				lineageTag: 8259c5ad-0d8a-41be-aaf4-1a0cc5b8c172

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy2
				isHidden
				displayFolder: i. Base measures\Sales
				lineageTag: e5da08fc-6c16-4800-8a42-d5cf19db08eb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy3
				isHidden
				displayFolder: i. Base measures\Books
				lineageTag: 5d563e19-2876-4933-b47a-6eb7c137ae8d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy4
				isHidden
				displayFolder: ii. KPI Targets
				lineageTag: 2f3462e1-6027-4285-8225-cf7587c2d2e9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy5
				isHidden
				displayFolder: iii. Comparison
				lineageTag: 91f41e76-615d-4618-8b64-f7545d1b39b5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy6
				isHidden
				displayFolder: iv. Time Intelligence\i. MTD
				lineageTag: 68142437-f0a4-4220-8c94-567b28c0a1a9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy7
				isHidden
				displayFolder: iv. Time Intelligence\ii. QTD
				lineageTag: 317eb9f6-fa60-4b69-88f0-0d3964864b99

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy8
				isHidden
				displayFolder: iv. Time Intelligence\iii. YTD
				lineageTag: ff65652e-e78d-480c-9f68-a69a10452f34

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy9
				isHidden
				displayFolder: iv. Time Intelligence\iv. LY
				lineageTag: 8878f524-cbbc-4eea-b5ad-ed0a843482a4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy10
				isHidden
				displayFolder: iv. Time Intelligence\v. LY %
				lineageTag: ed4931ae-8a5b-4157-aa7a-52e49f4f1fed

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy11
				isHidden
				displayFolder: iv. Time Intelligence\vi. MoM
				lineageTag: 257b32de-8b4c-471f-a675-ba6a24752c4f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy12
				isHidden
				displayFolder: iv. Time Intelligence\vii. QoQ
				lineageTag: f179afb3-11c5-41d8-b8a2-727fd025fdcd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy13
				isHidden
				displayFolder: iv. Time Intelligence\viii. YoY
				lineageTag: 7b1e1d62-8c3b-4daa-b227-da89ab1fd37e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy14
				isHidden
				displayFolder: v. Ratio
				lineageTag: 9579de8e-ac61-4430-a028-a13daef5cc72

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy15
				isHidden
				displayFolder: vi. Report Layout\i. Last Refresh
				lineageTag: f80046c2-1d37-4e0f-bc99-83a03ccceca2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			/// Gives you the count of all the measures in the model.
			/// 
			/// DAX Expression:
			/// 
			/// "CALCULATE (
			/// COUNTROWS ( 'Model documentation' ),
			/// 'Model documentation'[Type] = ""Measure""
			/// )"
			measure '# of Measures' =
					
					CALCULATE (
					    COUNTROWS ( 'Model documentation' ),
					    'Model documentation'[Type] = "Measure"
					)
				formatString: #,0
				displayFolder: vi. Report Layout\ii. Model metadata measures
				lineageTag: 41758d0f-c397-4f7a-8a2b-8081c55f40f1

			measure dummy17
				isHidden
				displayFolder: vi. Report Layout\iv. Dynamic Titles
				lineageTag: 9f798b63-8c9c-439c-b117-145849cb6b07

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy18
				isHidden
				displayFolder: vi. Report Layout\v. SVG's
				lineageTag: 45ebe238-4077-4d56-961c-7352216bbb91

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure dummy19
				isHidden
				displayFolder: vi. Report Layout\vi. Cascading Slicers
				lineageTag: 705f0e19-4ce8-43b0-a608-4f3e1caa9d1d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			/// Gives you the count of all the columns in the model.
			/// 
			/// DAX Expression:
			/// 
			/// "CALCULATE (
			/// COUNTROWS ( 'Model documentation' ),
			/// 'Model documentation'[Type] = ""Column""
			/// )"
			/// 
			measure '# of Columns' =
					
					CALCULATE (
					    COUNTROWS ( 'Model documentation' ),
					    'Model documentation'[Type] = "Column"
					)
				formatString: #,0
				displayFolder: vi. Report Layout\ii. Model metadata measures
				lineageTag: 939e1d4e-236f-42eb-92f7-01999781fea0

			/// Gives you the count of all the tables in the model.
			/// 
			/// DAX Expression:
			/// 
			/// "CALCULATE (
			/// COUNTROWS ( 'Model documentation' ),
			/// 'Model documentation'[Type] = ""Table""
			/// )"
			/// 
			/// 
			measure '# of Tables' =
					
					CALCULATE (
					    COUNTROWS ( 'Model documentation' ),
					    'Model documentation'[Type] = "Tables"
					)
				formatString: #,0
				displayFolder: vi. Report Layout\ii. Model metadata measures
				lineageTag: 67e367bd-5060-4adb-99c8-2e1666fa090b

			/// Gives you the count of all the relationships in the model.
			/// 
			/// DAX Expression:
			/// 
			/// "CALCULATE (
			/// COUNTROWS ( 'Model documentation' ),
			/// 'Model documentation'[Type] = ""Relationship""
			/// )"
			/// 
			/// 
			measure '# of Relationships' =
					
					CALCULATE (
					    COUNTROWS ( 'Model documentation' ),
					    'Model documentation'[Type] = "Relationship"
					)
				formatString: #,0
				displayFolder: vi. Report Layout\ii. Model metadata measures
				lineageTag: a3a27552-ef0b-4e2d-a5b2-b19d00e7b44d

			measure dummy16
				isHidden
				displayFolder: vi. Report Layout\iii. HEX Colors
				lineageTag: 78980f8f-3480-4217-a73a-35881d0db485

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			partition 'Global Measures' = m
				mode: import
				source = ```
						let  
						    _Measures = #table({},{})  
						in  
						    _Measures
						```

			changedProperty = Name

			annotation PBI_ResultType = Table

			annotation PBI_NavigationStepName = Navigation

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

		/// This table contains all measures created by local report developers. This is meant as your personal collection of measures.
		table 'Local Measures'
			lineageTag: abd31670-9c4e-4bf7-873f-8fa1a9e148a0

			measure 'Local measure dummy'
				isHidden
				displayFolder: i. Base measures
				lineageTag: 77378ba9-6470-4b99-8f62-f777c6db771f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","EXPRESSION_RELIANT_OBJECTS_MUST_HAVE_AN_EXPRESSION","INTEGER_FORMATTING","UNNECESSARY_MEASURES"]}

			partition 'Local Measures' = m
				mode: import
				source = ```
						let  
						    _Measures = #table({},{})  
						in  
						    _Measures
						```

			annotation PBI_ResultType = Table

			annotation PBI_NavigationStepName = Navigation

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

		/// This table contains all Unicode Library from https://www.nudgebi.com/unicode-library
		table 'Unicode Measures'
			isHidden
			lineageTag: 122e154c-7879-4a90-b4bb-233cdb0fce21

			measure '↑ Upwards Arrow' = UNICHAR(8593)
				isHidden
				displayFolder: Arrows
				lineageTag: 0f8c7c9f-51a3-4116-8ea0-6f1c56d2fe94

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↓ Downwards Arrow' = UNICHAR(8595)
				isHidden
				displayFolder: Arrows
				lineageTag: ee602048-c48d-4768-8c0c-3afe7bcc4097

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '→ Rightwards Arrow' = UNICHAR(8594)
				isHidden
				displayFolder: Arrows
				lineageTag: 44a0b2dc-c9d5-4d7a-a91e-27a86dcec1fd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '← Leftwards Arrow' = UNICHAR(8592)
				isHidden
				displayFolder: Arrows
				lineageTag: 8a7b6125-2374-4597-9105-31b6990e886a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↔ Left Right Arrow' = UNICHAR(8596)
				isHidden
				displayFolder: Arrows
				lineageTag: 63d0127a-0122-4cf7-a45e-85c26d338016

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↕ Up Down Arrow' = UNICHAR(8597)
				isHidden
				displayFolder: Arrows
				lineageTag: 2ea07236-9d23-4041-be71-37746a06534b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↗ North East Arrow' = UNICHAR(8599)
				isHidden
				displayFolder: Arrows
				lineageTag: a45d1de1-3a79-4809-a29d-2194fb306579

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↘ South East Arrow' = UNICHAR(8600)
				isHidden
				displayFolder: Arrows
				lineageTag: c8c71ff0-e5d0-4e4f-b117-0e99334c9704

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↙ South West Arrow' = UNICHAR(8601)
				isHidden
				displayFolder: Arrows
				lineageTag: 3e683f49-d6f6-4ba3-8ab2-aa59d04bdd7d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↖ North West Arrow' = UNICHAR(8598)
				isHidden
				displayFolder: Arrows
				lineageTag: 9e29d058-9779-472e-a2a5-3f2967537061

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇑ Upwards Double Arrow' = UNICHAR(8657)
				isHidden
				displayFolder: Arrows
				lineageTag: 361a550e-470d-4fe0-8338-74f8d9f127e4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇓ Downwards Double Arrow' = UNICHAR(8659)
				isHidden
				displayFolder: Arrows
				lineageTag: b2f2d0c6-ef0e-4572-95e1-d8289f163d42

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇒ Rightwards Double Arrow' = UNICHAR(8658)
				isHidden
				displayFolder: Arrows
				lineageTag: 12310a7a-b730-4cd2-9bfa-e68464453b98

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇐ Leftwards Double Arrow' = UNICHAR(8656)
				isHidden
				displayFolder: Arrows
				lineageTag: 9b9b21df-f1a7-4a42-85d9-69f80f989a9c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇔ Left Right Double Arrow' = UNICHAR(8660)
				isHidden
				displayFolder: Arrows
				lineageTag: 0085421c-3787-410f-8a33-6bb8b456849f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇕ Up Down Double Arrow' = UNICHAR(8661)
				isHidden
				displayFolder: Arrows
				lineageTag: 09c36ba5-d9c3-4ebb-9579-ac20a0e40654

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇗ North East Double Arrow' = UNICHAR(8663)
				isHidden
				displayFolder: Arrows
				lineageTag: 689549ca-03cb-4bf5-82d0-31807e898310

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇘ South East Double Arrow' = UNICHAR(8664)
				isHidden
				displayFolder: Arrows
				lineageTag: 62dd5f6d-7199-4d93-a4f4-8299523a988b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇙ South West Double Arrow' = UNICHAR(8665)
				isHidden
				displayFolder: Arrows
				lineageTag: be3d7060-b9ea-416f-9f09-58acfc022aab

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇖ North West Double Arrow' = UNICHAR(8662)
				isHidden
				displayFolder: Arrows
				lineageTag: 92ee7a4c-5f54-4ace-9a2b-be590fedb0ed

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↺ Anticlockwise Open Circle Arrow' = UNICHAR(8634)
				isHidden
				displayFolder: Arrows
				lineageTag: c2b127cc-a4b9-4d76-8792-af4da5fa8a4d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↻ Clockwise Open Circle Arrow' = UNICHAR(8635)
				isHidden
				displayFolder: Arrows
				lineageTag: 84094210-d613-48b7-9e72-d1aecfb16c6b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇄ Rightwards Arrow Over Leftwards Arrow' = UNICHAR(8644)
				isHidden
				displayFolder: Arrows
				lineageTag: 4e055719-3836-470a-b09e-dc89d6731014

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇆ Leftwards Arrow Over Rightwards Arrow' = UNICHAR(8646)
				isHidden
				displayFolder: Arrows
				lineageTag: 0c6688b6-f960-4df2-a242-a8f563ef0de1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇿ Left Right Arrow With Vertical Stroke' = UNICHAR(8703)
				isHidden
				displayFolder: Arrows
				lineageTag: 98440843-dc05-4eaa-b3f5-517191383651

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➔ Heavy Rightwards Arrow' = UNICHAR(10132)
				isHidden
				displayFolder: Arrows
				lineageTag: 8fdaa846-2e54-4552-a880-0488c65b70dc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➘ Heavy South East Arrow' = UNICHAR(10136)
				isHidden
				displayFolder: Arrows
				lineageTag: 0563c8d0-7512-437f-a098-a884381119bc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➙ Heavy Rightwards Arrow' = UNICHAR(10137)
				isHidden
				displayFolder: Arrows
				lineageTag: 297d91c1-d4c7-492b-adf3-f3362e51ce12

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➚ Heavy North East Arrow' = UNICHAR(10138)
				isHidden
				displayFolder: Arrows
				lineageTag: 1c9a87da-f7d7-43d2-8002-0d40c581e2f6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➛ Drafting Point Rightwards Arrow' = UNICHAR(10139)
				isHidden
				displayFolder: Arrows
				lineageTag: 29e8cd75-d397-42c6-8a90-3b8b159e803f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➜ Heavy Round-Tipped Rightwards Arrow' = UNICHAR(10140)
				isHidden
				displayFolder: Arrows
				lineageTag: e4be622b-1400-48ac-8084-76f9ade7ee70

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➝ Triangle-Headed Rightwards Arrow' = UNICHAR(10141)
				isHidden
				displayFolder: Arrows
				lineageTag: e839ba6d-3c7b-43cc-bb43-6aae2a98de70

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➞ Heavy Triangle-Headed Rightwards Arrow' = UNICHAR(10142)
				isHidden
				displayFolder: Arrows
				lineageTag: dc1dc8cb-9cf5-4f9b-a9f6-ff1294d235eb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➟ Dashed Triangle-Headed Rightwards Arrow' = UNICHAR(10143)
				isHidden
				displayFolder: Arrows
				lineageTag: 7420628a-a900-4f78-bcc3-aa91555ec1fd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➠ Heavy Dashed Triangle-Headed Rightwards Arrow' = UNICHAR(10144)
				isHidden
				displayFolder: Arrows
				lineageTag: 13170ed6-3747-4682-b8b1-09b22c344b76

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➡ Black Rightwards Arrow' = UNICHAR(10145)
				isHidden
				displayFolder: Arrows
				lineageTag: 1a670a77-8315-4a8c-a224-603b01179302

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➢ Three-D Top-Lighted Rightwards Arrowhead' = UNICHAR(10146)
				isHidden
				displayFolder: Arrows
				lineageTag: f6e27a0c-3bef-4adf-900b-d9f7d55e0af1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➣ Three-D Bottom-Lighted Rightwards Arrowhead' = UNICHAR(10147)
				isHidden
				displayFolder: Arrows
				lineageTag: 5d437a3e-7a3c-4122-8b28-1d9e1accb547

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➤ Black Rightwards Arrowhead' = UNICHAR(10148)
				isHidden
				displayFolder: Arrows
				lineageTag: f4d38dd3-b41e-40ff-8d9b-007e81b66c8f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➥ Heavy Black Curved Downwards And Rightwards Arrow' = UNICHAR(10149)
				isHidden
				displayFolder: Arrows
				lineageTag: 69a2b39b-bc49-4044-9f09-15a03c29769f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➦ Heavy Black Curved Upwards And Rightwards Arrow' = UNICHAR(10150)
				isHidden
				displayFolder: Arrows
				lineageTag: 331d44eb-ae37-4146-8596-c80e2d328f46

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➧ Squat Black Rightwards Arrow' = UNICHAR(10151)
				isHidden
				displayFolder: Arrows
				lineageTag: 2a00cdd1-f824-4480-9efe-d49ec45e4fd4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➨ Heavy Concave-Pointed Black Rightwards Arrow' = UNICHAR(10152)
				isHidden
				displayFolder: Arrows
				lineageTag: f62c93eb-02b1-4c06-87b9-4b90c32f9f56

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➩ Right-Shaded White Rightwards Arrow' = UNICHAR(10153)
				isHidden
				displayFolder: Arrows
				lineageTag: 17cfbefc-c564-4c73-8907-07e68eab2a41

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➪ Left-Shaded White Rightwards Arrow' = UNICHAR(10154)
				isHidden
				displayFolder: Arrows
				lineageTag: fc0ad6f7-6749-4fa2-bef7-c3595cb8e56f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➫ Back-Tilted Shadowed White Rightwards Arrow' = UNICHAR(10155)
				isHidden
				displayFolder: Arrows
				lineageTag: b7533364-fda4-4e54-b473-cacb90f3b605

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➬ Front-Tilted Shadowed White Rightwards Arrow' = UNICHAR(10156)
				isHidden
				displayFolder: Arrows
				lineageTag: 542da000-a5c3-42ad-a55f-d6ff270abc47

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➭ Heavy Lower Right-Shadowed White Rightwards Arrow' = UNICHAR(10157)
				isHidden
				displayFolder: Arrows
				lineageTag: 61c27676-e4ec-466b-9a55-cbdd5c77dca9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➮ Heavy Upper Right-Shadowed White Rightwards Arrow' = UNICHAR(10158)
				isHidden
				displayFolder: Arrows
				lineageTag: 6828434b-aa40-47fd-b033-d5ec9f45465e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➯ Notched Lower Right-Shadowed White Rightwards Arrow' = UNICHAR(10159)
				isHidden
				displayFolder: Arrows
				lineageTag: 9acc992c-823e-4246-93ff-3fcd7cf4313e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➱ Notched Upper Right-Shadowed White Rightwards Arrow' = UNICHAR(10161)
				isHidden
				displayFolder: Arrows
				lineageTag: a043f03b-1c88-414b-b4a3-f697735024e9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➲ Circled Heavy White Rightwards Arrow' = UNICHAR(10162)
				isHidden
				displayFolder: Arrows
				lineageTag: baae51d7-e647-4e62-bcb7-9c885554ac51

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➳ White-Feathered Rightwards Arrow' = UNICHAR(10163)
				isHidden
				displayFolder: Arrows
				lineageTag: 6dde8e32-fae4-4266-a5fb-77f7a2986c71

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➴ Heavy White-Feathered South East Arrow' = UNICHAR(10164)
				isHidden
				displayFolder: Arrows
				lineageTag: 2ad24a4b-aaba-452e-86be-b9d5e791c696

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➵ Heavy White-Feathered Rightwards Arrow' = UNICHAR(10165)
				isHidden
				displayFolder: Arrows
				lineageTag: dbb579bd-8ed9-4bec-8dd6-e91c009e498a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➶ Heavy White-Feathered North East Arrow' = UNICHAR(10166)
				isHidden
				displayFolder: Arrows
				lineageTag: b7c86b24-eb68-403b-b4a3-d39588aaa57a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➷ Heavy Stemmed Feathered South East Arrow' = UNICHAR(10167)
				isHidden
				displayFolder: Arrows
				lineageTag: f021c727-9fa6-4a3b-9af9-694b2276b008

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➸ Heavy Stemmed Feathered Rightwards Arrow' = UNICHAR(10168)
				isHidden
				displayFolder: Arrows
				lineageTag: 7195d447-a80a-4b30-8b0c-45df38fadc37

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➹ Heavy Stemmed Feathered North East Arrow' = UNICHAR(10169)
				isHidden
				displayFolder: Arrows
				lineageTag: 878b7fbf-6e2d-40ab-aee2-7211d123b355

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➺ Heavy Sharp-Feathered South East Arrow' = UNICHAR(10170)
				isHidden
				displayFolder: Arrows
				lineageTag: 2a7a9b60-68c8-45e9-a513-38294666818b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➻ Heavy Sharp-Feathered Rightwards Arrow' = UNICHAR(10171)
				isHidden
				displayFolder: Arrows
				lineageTag: 7bb59840-a333-4845-bbd5-2a65f3b96ab3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➼ Heavy Sharp-Feathered North East Arrow' = UNICHAR(10172)
				isHidden
				displayFolder: Arrows
				lineageTag: 999fe8d0-2bf0-4647-aadb-a0e265b88eaa

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➽ Heavy Arrow Shaft Rightwards Arrow' = UNICHAR(10173)
				isHidden
				displayFolder: Arrows
				lineageTag: 3eca8e40-cbc6-4dba-bb82-2a477ddb9362

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤴ Arrow Pointing Rightwards Then Curving Upwards' = UNICHAR(10548)
				isHidden
				displayFolder: Arrows
				lineageTag: 84fa05d9-89aa-4b5d-9c49-591a1c3daf44

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤵ Arrow Pointing Rightwards Then Curving Downwards' = UNICHAR(10549)
				isHidden
				displayFolder: Arrows
				lineageTag: 20198b5b-d4ec-4072-876d-8d6d034b9a7e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↵ Downwards Arrow With Corner Leftwards' = UNICHAR(8629)
				isHidden
				displayFolder: Arrows
				lineageTag: e0ba6fb2-b98c-4a3f-9b18-cab7c617673f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↴ Downwards Arrow With Tip Leftwards' = UNICHAR(8628)
				isHidden
				displayFolder: Arrows
				lineageTag: a76c52e9-ef7d-4c67-8e32-90f388412110

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↳ Upwards Arrow With Tip Leftwards' = UNICHAR(8624)
				isHidden
				displayFolder: Arrows
				lineageTag: cfe5ef4e-9af2-4906-8f32-4cc51f08639d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↶ Anticlockwise Top Semicircle Arrow' = UNICHAR(8630)
				isHidden
				displayFolder: Arrows
				lineageTag: c7dfa545-6185-4a4c-82de-cc57b6721bce

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↷ Clockwise Top Semicircle Arrow' = UNICHAR(8631)
				isHidden
				displayFolder: Arrows
				lineageTag: 41644096-4105-4a9b-ae82-e8a379835ac5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '￩ Leftwards Arrow With Stroke' = UNICHAR(8678)
				isHidden
				displayFolder: Arrows
				lineageTag: 69981356-ede6-43da-b3f3-8b0413a29a88

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '￫ Rightwards Arrow With Stroke' = UNICHAR(8680)
				isHidden
				displayFolder: Arrows
				lineageTag: 7cf52f36-685c-4629-b41c-b889d728961f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↚ Leftwards Arrow With Stroke' = UNICHAR(8602)
				isHidden
				displayFolder: Arrows
				lineageTag: fc21410f-e28e-47f3-be89-2ef4273f9fbc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↛ Rightwards Arrow With Stroke' = UNICHAR(8603)
				isHidden
				displayFolder: Arrows
				lineageTag: 21d4c59b-48a7-4470-b1d5-30b39a0a5c29

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↜ Leftwards Wave Arrow' = UNICHAR(8604)
				isHidden
				displayFolder: Arrows
				lineageTag: 598fc200-0b58-40e6-9a85-9de75cd18a89

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↝ Rightwards Wave Arrow' = UNICHAR(8605)
				isHidden
				displayFolder: Arrows
				lineageTag: 0ab52103-7c93-48b7-9a2f-3c5d8014f246

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↞ Leftwards Two Headed Arrow' = UNICHAR(8606)
				isHidden
				displayFolder: Arrows
				lineageTag: 9852fe41-b7cf-4582-b5ad-f4f50ff6493d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↠ Rightwards Two Headed Arrow' = UNICHAR(8607)
				isHidden
				displayFolder: Arrows
				lineageTag: 7c27b865-a335-4849-a072-eb6118fe925a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↢ Leftwards Arrow With Tail' = UNICHAR(8610)
				isHidden
				displayFolder: Arrows
				lineageTag: 80bc69bd-e7ce-464b-addb-068486d44bec

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↣ Rightwards Arrow With Tail' = UNICHAR(8611)
				isHidden
				displayFolder: Arrows
				lineageTag: abe19cf5-4327-417d-91b1-a501b3e4eea7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↤ Leftwards Arrow From Bar' = UNICHAR(8612)
				isHidden
				displayFolder: Arrows
				lineageTag: ad14a514-f608-47d8-863f-6273c401e196

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↥ Upwards Arrow From Bar' = UNICHAR(8613)
				isHidden
				displayFolder: Arrows
				lineageTag: f97186f3-b80d-480a-aa2d-3d89e370b021

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↦ Rightwards Arrow From Bar' = UNICHAR(8614)
				isHidden
				displayFolder: Arrows
				lineageTag: 73651d4a-2512-4558-9d6a-77b905f35d5c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↧ Downwards Arrow From Bar' = UNICHAR(8615)
				isHidden
				displayFolder: Arrows
				lineageTag: 9a3d6054-b1ed-45a5-969e-288d1452acee

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↨ Up Down Arrow With Base' = UNICHAR(8616)
				isHidden
				displayFolder: Arrows
				lineageTag: 7ee46b7a-1bae-4dee-baa2-09fb00b66931

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↩ Leftwards Arrow With Hook' = UNICHAR(8617)
				isHidden
				displayFolder: Arrows
				lineageTag: 2581cddb-afd2-41cf-b80f-7fb7173ddf8b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↪ Rightwards Arrow With Hook' = UNICHAR(8618)
				isHidden
				displayFolder: Arrows
				lineageTag: 2318eeac-0a30-412f-9a2b-017b12d9d5ab

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↫ Leftwards Arrow With Loop' = UNICHAR(8619)
				isHidden
				displayFolder: Arrows
				lineageTag: 97fc4ba0-5288-4b63-b5b1-48821b7ca081

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↬ Rightwards Arrow With Loop' = UNICHAR(8620)
				isHidden
				displayFolder: Arrows
				lineageTag: 3397403e-3e3a-4161-8390-442a6e4444e3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↭ Left Right Wave Arrow' = UNICHAR(8621)
				isHidden
				displayFolder: Arrows
				lineageTag: 1b3c432c-4c41-4e0c-9a86-b37c1df9fdd8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↮ Left Right Arrow With Stroke' = UNICHAR(8622)
				isHidden
				displayFolder: Arrows
				lineageTag: 92de49a8-4297-43f0-a739-397ff7230e5b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↯ Downwards Zigzag Arrow' = UNICHAR(8623)
				isHidden
				displayFolder: Arrows
				lineageTag: f074644b-df30-4b26-b086-596de89a9927

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↱ Upwards Arrow With Tip Rightwards' = UNICHAR(8625)
				isHidden
				displayFolder: Arrows
				lineageTag: 05be86e3-0787-4909-9a86-4c899b2e81cd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↲ Downwards Arrow With Tip Rightwards' = UNICHAR(8626)
				isHidden
				displayFolder: Arrows
				lineageTag: df0ba1b9-3e52-4a83-b3d7-88e18453c2cb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↼ Leftwards Harpoon With Barb Upwards' = UNICHAR(8636)
				isHidden
				displayFolder: Arrows
				lineageTag: 3445c8c5-c2f1-4a16-8b59-5a2de6d44292

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↽ Leftwards Harpoon With Barb Downwards' = UNICHAR(8637)
				isHidden
				displayFolder: Arrows
				lineageTag: c5527909-970b-477b-9625-7814a1a91675

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↾ Upwards Harpoon With Barb Rightwards' = UNICHAR(8638)
				isHidden
				displayFolder: Arrows
				lineageTag: e9ec288d-2fed-4faf-9f10-9c6e6838abda

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↿ Upwards Harpoon With Barb Leftwards' = UNICHAR(8639)
				isHidden
				displayFolder: Arrows
				lineageTag: 7a3ccba9-5713-4a12-aa01-4ec687883c84

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇀ Rightwards Harpoon With Barb Upwards' = UNICHAR(8640)
				isHidden
				displayFolder: Arrows
				lineageTag: 8fe5c520-adc9-473a-9427-04970206f3f8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇁ Rightwards Harpoon With Barb Downwards' = UNICHAR(8641)
				isHidden
				displayFolder: Arrows
				lineageTag: 824a6484-fe76-43e6-b34c-d52bba759f13

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇂ Downwards Harpoon With Barb Rightwards' = UNICHAR(8642)
				isHidden
				displayFolder: Arrows
				lineageTag: 20f31423-8625-4a25-abc1-3103d89d68d4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇃ Downwards Harpoon With Barb Leftwards' = UNICHAR(8643)
				isHidden
				displayFolder: Arrows
				lineageTag: cc9a8a0e-400f-4ea8-8715-530f3e522b3c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇋ Leftwards Harpoon Over Rightwards Harpoon' = UNICHAR(8651)
				isHidden
				displayFolder: Arrows
				lineageTag: caf77d03-9ba1-4f91-a9ce-0eeef639eaf9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇌ Rightwards Harpoon Over Leftwards Harpoon' = UNICHAR(8652)
				isHidden
				displayFolder: Arrows
				lineageTag: 5fad75e5-1941-4456-9081-7b42a2d9e443

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇍ Leftwards Double Arrow With Stroke' = UNICHAR(8653)
				isHidden
				displayFolder: Arrows
				lineageTag: 5251cf69-f67f-49ad-97e0-9e8492ebd930

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇎ Left Right Double Arrow With Stroke' = UNICHAR(8654)
				isHidden
				displayFolder: Arrows
				lineageTag: 9a9738a6-d1ba-4c4c-9ba5-78253b79d5e8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇏ Rightwards Double Arrow With Stroke' = UNICHAR(8655)
				isHidden
				displayFolder: Arrows
				lineageTag: 43e24099-3e0d-4ca3-97b0-f3dbc32b0f1d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇜ Leftwards Squiggle Arrow' = UNICHAR(8668)
				isHidden
				displayFolder: Arrows
				lineageTag: d7274843-2817-4c4e-a8d0-c5d0d37ebc5d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇝ Rightwards Squiggle Arrow' = UNICHAR(8669)
				isHidden
				displayFolder: Arrows
				lineageTag: e7120512-91d3-413d-8be2-afee4ec12a42

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇞ Upwards Arrow With Double Stroke' = UNICHAR(8670)
				isHidden
				displayFolder: Arrows
				lineageTag: 2cdc80fb-2a59-41b8-b6cc-3559508b87a6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇟ Downwards Arrow With Double Stroke' = UNICHAR(8671)
				isHidden
				displayFolder: Arrows
				lineageTag: 7a44bede-6fe1-44fd-aace-3154cd30d891

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇠ Leftwards Dashed Arrow' = UNICHAR(8672)
				isHidden
				displayFolder: Arrows
				lineageTag: 262863e6-e929-4ef6-9e32-1a23d1174d8e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇡ Upwards Dashed Arrow' = UNICHAR(8673)
				isHidden
				displayFolder: Arrows
				lineageTag: 4dfa0a0e-bacc-4c4c-a5fc-ab3ce131502f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇢ Rightwards Dashed Arrow' = UNICHAR(8674)
				isHidden
				displayFolder: Arrows
				lineageTag: 11d4b556-dc8d-45cd-a84c-2d2dc73ae4c9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇣ Downwards Dashed Arrow' = UNICHAR(8675)
				isHidden
				displayFolder: Arrows
				lineageTag: 91e5c819-c347-4c77-9861-e630aa48dcd3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇤ Leftwards Arrow To Bar' = UNICHAR(8676)
				isHidden
				displayFolder: Arrows
				lineageTag: 600adcaf-66ce-4386-a869-af8a58cef60c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇥ Rightwards Arrow To Bar' = UNICHAR(8677)
				isHidden
				displayFolder: Arrows
				lineageTag: 7bd56f3f-ab44-4dbe-a7d1-70e6212364da

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇩ Downwards White Arrow' = UNICHAR(8681)
				isHidden
				displayFolder: Arrows
				lineageTag: 1b6a2354-7718-4b7e-8ab5-037970c9cc06

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇪ Upwards White Arrow From Bar' = UNICHAR(8682)
				isHidden
				displayFolder: Arrows
				lineageTag: f6b9f4e3-61c9-4a42-af64-3c142c240166

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇫ Downwards White Arrow From Bar' = UNICHAR(8683)
				isHidden
				displayFolder: Arrows
				lineageTag: 6ce8b000-63cd-440e-8c87-2b38a1f51ab8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇬ Upwards White Arrow On Pedestal' = UNICHAR(8684)
				isHidden
				displayFolder: Arrows
				lineageTag: eb10a28f-2e04-48c9-bc77-40c0bffb8378

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇭ Upwards White Arrow On Pedestal With Horizontal Bar' = UNICHAR(8685)
				isHidden
				displayFolder: Arrows
				lineageTag: 0100de0c-94a9-41ea-80d1-c26d59b0b81f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇮ Upwards White Arrow On Pedestal With Vertical Bar' = UNICHAR(8686)
				isHidden
				displayFolder: Arrows
				lineageTag: bb8ae767-4660-4667-ad3d-a32cce6c0f8d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇯ Upwards White Double Arrow' = UNICHAR(8687)
				isHidden
				displayFolder: Arrows
				lineageTag: c371003d-874e-4b6e-a4d5-63911f6f3a33

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇰ Rightwards White Arrow From Wall' = UNICHAR(8688)
				isHidden
				displayFolder: Arrows
				lineageTag: cd33f28a-a1f7-4ddf-90ee-df7dfc6a1dbf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇱ North West Arrow To Corner' = UNICHAR(8689)
				isHidden
				displayFolder: Arrows
				lineageTag: 5433e138-0d47-48d6-a25e-6553fef73012

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇲ South East Arrow To Corner' = UNICHAR(8690)
				isHidden
				displayFolder: Arrows
				lineageTag: 5a772771-8cec-401e-80cf-51e8ca45ea31

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇳ Up Down White Arrow' = UNICHAR(8691)
				isHidden
				displayFolder: Arrows
				lineageTag: 1d8f8054-9d16-4724-8c30-a3d4e942c838

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇴ Right Arrow With Small Circle' = UNICHAR(8692)
				isHidden
				displayFolder: Arrows
				lineageTag: 58a834f8-6c42-4d27-a8f4-ce8b426e9377

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇵ Upwards Arrow Leftwards Of Downwards Arrow' = UNICHAR(8693)
				isHidden
				displayFolder: Arrows
				lineageTag: cb9e9180-bc9f-4495-9174-367daa06c40d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇶ Three Rightwards Arrows' = UNICHAR(8694)
				isHidden
				displayFolder: Arrows
				lineageTag: e523e568-73e6-47cb-904b-fffaf707fe29

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇷ Leftwards Arrow With Vertical Stroke' = UNICHAR(8695)
				isHidden
				displayFolder: Arrows
				lineageTag: 934f51c9-c0ea-4189-b5c0-ee5c24240dd1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇸ Rightwards Arrow With Vertical Stroke' = UNICHAR(8696)
				isHidden
				displayFolder: Arrows
				lineageTag: 46565740-50df-4352-ba60-012dfd08439d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇺ Leftwards Arrow With Double Vertical Stroke' = UNICHAR(8698)
				isHidden
				displayFolder: Arrows
				lineageTag: bf0a57b7-f13c-481d-80ec-4c65ab03f3a7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇻ Rightwards Arrow With Double Vertical Stroke' = UNICHAR(8699)
				isHidden
				displayFolder: Arrows
				lineageTag: 70342d84-085b-46dc-93e3-047ddb77c7db

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇼ Left Right Arrow With Double Vertical Stroke' = UNICHAR(8700)
				isHidden
				displayFolder: Arrows
				lineageTag: 066f41b5-6711-4faa-87cf-5cf385dd317d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇽ Leftwards Open-Headed Arrow' = UNICHAR(8701)
				isHidden
				displayFolder: Arrows
				lineageTag: 52dcc654-f92a-4f52-ab25-95cd9610f525

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇾ Rightwards Open-Headed Arrow' = UNICHAR(8702)
				isHidden
				displayFolder: Arrows
				lineageTag: 6b37311c-0716-4b4f-88f2-5396fa1dcb99

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟰ Upwards Quadruple Arrow' = UNICHAR(10224)
				isHidden
				displayFolder: Arrows
				lineageTag: f8b4a266-b525-4466-8a75-f9f377291e27

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟱ Downwards Quadruple Arrow' = UNICHAR(10225)
				isHidden
				displayFolder: Arrows
				lineageTag: b7279cad-6ec6-4669-96e8-cbfc0d6e6988

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟲ Anticlockwise Gapped Circle Arrow' = UNICHAR(10226)
				isHidden
				displayFolder: Arrows
				lineageTag: aad7b3b3-d671-4856-9d4a-14b96bfa3b75

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟳ Clockwise Gapped Circle Arrow' = UNICHAR(10227)
				isHidden
				displayFolder: Arrows
				lineageTag: 0e4853b4-31f7-45b0-9cf7-b9124bf85248

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟴ Right Arrow With Circled Plus' = UNICHAR(10228)
				isHidden
				displayFolder: Arrows
				lineageTag: 9d8b85c0-5fe1-4962-89a7-a55b21d25c05

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟵ Long Leftwards Arrow' = UNICHAR(10229)
				isHidden
				displayFolder: Arrows
				lineageTag: fa07a9b3-fd26-4784-9e5a-b027f250c724

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟶ Long Rightwards Arrow' = UNICHAR(10230)
				isHidden
				displayFolder: Arrows
				lineageTag: 2938c12b-35c1-4f9b-9781-4c52ec3bf253

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟷ Long Left Right Arrow' = UNICHAR(10231)
				isHidden
				displayFolder: Arrows
				lineageTag: 0086317c-0853-44a1-84e6-d03c976be3b8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟸ Long Leftwards Double Arrow' = UNICHAR(10232)
				isHidden
				displayFolder: Arrows
				lineageTag: 756ab14d-520a-49b0-b825-6b93e858267f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟹ Long Rightwards Double Arrow' = UNICHAR(10233)
				isHidden
				displayFolder: Arrows
				lineageTag: e6511806-9841-4086-8fde-b38dc7370dad

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟺ Long Left Right Double Arrow' = UNICHAR(10234)
				isHidden
				displayFolder: Arrows
				lineageTag: dc6211dd-3226-4d9e-9aa1-51d112a19cb9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟻ Long Leftwards Arrow From Bar' = UNICHAR(10235)
				isHidden
				displayFolder: Arrows
				lineageTag: 14f418f3-c945-4094-a591-9953a3346b9b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟼ Long Rightwards Arrow From Bar' = UNICHAR(10236)
				isHidden
				displayFolder: Arrows
				lineageTag: 2791c1d4-936c-41f6-884c-3ef6fce46b01

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟽ Long Leftwards Double Arrow From Bar' = UNICHAR(10237)
				isHidden
				displayFolder: Arrows
				lineageTag: bfc05552-71e6-47c5-96c6-5d0689d0d7c4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟾ Long Rightwards Double Arrow From Bar' = UNICHAR(10238)
				isHidden
				displayFolder: Arrows
				lineageTag: a1e8b289-593b-43f0-a245-be53282e9f7a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟿ Long Rightwards Squiggle Arrow' = UNICHAR(10239)
				isHidden
				displayFolder: Arrows
				lineageTag: f7bfff57-0ef0-4845-981b-ddf00d1f5cc9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤀ Rightwards Two-Headed Arrow With Vertical Stroke' = UNICHAR(10496)
				isHidden
				displayFolder: Arrows
				lineageTag: 1f7fce56-ab87-4e6e-aa57-cd083425bd9a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤁ Rightwards Two-Headed Arrow With Double Vertical Stroke' = UNICHAR(10497)
				isHidden
				displayFolder: Arrows
				lineageTag: 8296c409-7a5a-4951-b008-f2c2d7583169

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤂ Leftwards Double Arrow With Vertical Stroke' = UNICHAR(10498)
				isHidden
				displayFolder: Arrows
				lineageTag: 173dff64-fc83-4554-9b71-3bc1317093bb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤄ Left Right Double Arrow With Vertical Stroke' = UNICHAR(10500)
				isHidden
				displayFolder: Arrows
				lineageTag: 30b8c086-989c-47f2-b476-526dd25e9214

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤅ Rightwards Two-Headed Arrow From Bar' = UNICHAR(10501)
				isHidden
				displayFolder: Arrows
				lineageTag: 685ce7ff-928c-4a9e-a425-2e11b748efe5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤆ Leftwards Double Arrow From Bar' = UNICHAR(10502)
				isHidden
				displayFolder: Arrows
				lineageTag: 636a27ac-13c6-4c38-b2a7-655cdbfee534

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤇ Rightwards Double Arrow From Bar' = UNICHAR(10503)
				isHidden
				displayFolder: Arrows
				lineageTag: 668f38b9-291e-42c6-b837-8069c8371b99

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤈ Downwards Arrow With Horizontal Stroke' = UNICHAR(10504)
				isHidden
				displayFolder: Arrows
				lineageTag: fecae623-4961-44d3-9b9a-d1f89a0e1bb5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤉ Upwards Arrow With Horizontal Stroke' = UNICHAR(10505)
				isHidden
				displayFolder: Arrows
				lineageTag: 092e7b10-c1f3-4121-8de1-0216413916ce

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤊ Upwards Triple Arrow' = UNICHAR(10506)
				isHidden
				displayFolder: Arrows
				lineageTag: 8cc7169f-c630-4839-9a65-d0b191d2fbd1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤋ Downwards Triple Arrow' = UNICHAR(10507)
				isHidden
				displayFolder: Arrows
				lineageTag: 979036b8-0bb4-4f1f-9c1a-1765c84b2be7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤌ Leftwards Double Dash Arrow' = UNICHAR(10508)
				isHidden
				displayFolder: Arrows
				lineageTag: 172f9a36-a1fe-4b08-a226-78a33a82dc6b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤍ Rightwards Double Dash Arrow' = UNICHAR(10509)
				isHidden
				displayFolder: Arrows
				lineageTag: 9af75bdc-697b-45e2-89ac-d1d98787b76d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤎ Leftwards Triple Dash Arrow' = UNICHAR(10510)
				isHidden
				displayFolder: Arrows
				lineageTag: 8559eef9-68c3-4fc9-8e1e-05e778dd5721

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤏ Rightwards Triple Dash Arrow' = UNICHAR(10511)
				isHidden
				displayFolder: Arrows
				lineageTag: 34e56e0f-24e8-47d4-b933-a7b193652b57

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤐ Rightwards Two-Headed Triple Dash Arrow' = UNICHAR(10512)
				isHidden
				displayFolder: Arrows
				lineageTag: 0759066a-8a87-4a1d-9106-98df7202a52c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤑ Rightwards Arrow With Dotted Stem' = UNICHAR(10513)
				isHidden
				displayFolder: Arrows
				lineageTag: fe15da90-f77f-4df4-ad11-304966f35c73

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤒ Upwards Arrow To Bar' = UNICHAR(10514)
				isHidden
				displayFolder: Arrows
				lineageTag: cb48e731-2931-44cd-9dac-f84a8f0d6c78

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤓ Downwards Arrow To Bar' = UNICHAR(10515)
				isHidden
				displayFolder: Arrows
				lineageTag: 934112d1-bdd7-4de6-b2a3-fa0c87df520a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤔ Rightwards Arrow With Tail With Vertical Stroke' = UNICHAR(10516)
				isHidden
				displayFolder: Arrows
				lineageTag: 398c1dd7-91cb-446b-b469-936de3754b37

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤕ Rightwards Arrow With Tail With Double Vertical Stroke' = UNICHAR(10517)
				isHidden
				displayFolder: Arrows
				lineageTag: ad134ba4-6476-48ce-b9aa-cc229a83f094

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤖ Rightwards Two-Headed Arrow With Tail' = UNICHAR(10518)
				isHidden
				displayFolder: Arrows
				lineageTag: 90f65301-b97c-4214-a5b3-bb6f4405e15b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤗ Rightwards Two-Headed Arrow With Tail With Vertical Stroke' = UNICHAR(10519)
				isHidden
				displayFolder: Arrows
				lineageTag: 7c9e8bce-1858-4a77-bdb3-4492dfa64e2d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤘ Rightwards Two-Headed Arrow With Tail With Double Vertical Stroke' = UNICHAR(10520)
				isHidden
				displayFolder: Arrows
				lineageTag: 92eb75cc-ea3d-4c0a-bbc0-12c32440d8f7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤙ Leftwards Arrow-Tail' = UNICHAR(10521)
				isHidden
				displayFolder: Arrows
				lineageTag: c0d3f3cf-7d29-4f51-8dd3-7f1c3796d233

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤚ Rightwards Arrow-Tail' = UNICHAR(10522)
				isHidden
				displayFolder: Arrows
				lineageTag: 96dd86ea-0690-41a5-8991-db824594173b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤛ Leftwards Double Arrow-Tail' = UNICHAR(10523)
				isHidden
				displayFolder: Arrows
				lineageTag: 53e8a6f3-7652-495e-8a0f-67466c5ce07e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤜ Rightwards Double Arrow-Tail' = UNICHAR(10524)
				isHidden
				displayFolder: Arrows
				lineageTag: c863e5ae-f576-448b-ba51-edbee993e7d6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤝ Leftwards Arrow To Black Diamond' = UNICHAR(10525)
				isHidden
				displayFolder: Arrows
				lineageTag: 55bb2418-2d44-4a4c-b43b-dd0d1ac164cf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤞ Rightwards Arrow To Black Diamond' = UNICHAR(10526)
				isHidden
				displayFolder: Arrows
				lineageTag: cdec196c-0eb9-454d-8eb8-d077e4b3c7c1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤟ Leftwards Arrow From Bar To Black Diamond' = UNICHAR(10527)
				isHidden
				displayFolder: Arrows
				lineageTag: c050da5c-bb45-4cb7-be2f-12e1cde07d5a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤠ Rightwards Arrow From Bar To Black Diamond' = UNICHAR(10528)
				isHidden
				displayFolder: Arrows
				lineageTag: 4b9f3230-a1c6-484e-a492-f2792a2a82d0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤡ North West And South East Arrow' = UNICHAR(10529)
				isHidden
				displayFolder: Arrows
				lineageTag: 15aabd39-c389-443f-b2a1-38abfd8d9e11

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤢ North East And South West Arrow' = UNICHAR(10530)
				isHidden
				displayFolder: Arrows
				lineageTag: cdcd4fe9-0afa-49f9-8ed8-587bd8b2e107

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤣ North West Arrow With Hook' = UNICHAR(10531)
				isHidden
				displayFolder: Arrows
				lineageTag: 95ae484a-0c15-4805-b94f-55faf4ea7555

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤤ North East Arrow With Hook' = UNICHAR(10532)
				isHidden
				displayFolder: Arrows
				lineageTag: 208e9b9b-4e18-4e19-b4a4-5bee4835483b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤥ South East Arrow With Hook' = UNICHAR(10533)
				isHidden
				displayFolder: Arrows
				lineageTag: 56969ee4-b29f-4cc8-a638-706aa021e5dc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤦ South West Arrow With Hook' = UNICHAR(10534)
				isHidden
				displayFolder: Arrows
				lineageTag: 37a8ed68-e0f2-4301-9893-de4b84a2c3d4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤧ North West Arrow And North East Arrow' = UNICHAR(10535)
				isHidden
				displayFolder: Arrows
				lineageTag: 2002df8f-ac89-4339-ac95-9ccdff55c0fe

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤨ North East Arrow And South East Arrow' = UNICHAR(10536)
				isHidden
				displayFolder: Arrows
				lineageTag: ec88189c-3395-4239-b4a4-f8b59e09d992

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤩ South East Arrow And South West Arrow' = UNICHAR(10537)
				isHidden
				displayFolder: Arrows
				lineageTag: 37ed798f-8d68-44d6-ab32-cede7bb66c47

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤪ South West Arrow And North West Arrow' = UNICHAR(10538)
				isHidden
				displayFolder: Arrows
				lineageTag: 39a901d4-60fb-4ba8-b3e1-62d877f66038

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤫ Rising Diagonal Crossing Falling Diagonal' = UNICHAR(10539)
				isHidden
				displayFolder: Arrows
				lineageTag: c2db6799-14a1-40a1-9b2b-fed8dc6d932f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤬ Falling Diagonal Crossing Rising Diagonal' = UNICHAR(10540)
				isHidden
				displayFolder: Arrows
				lineageTag: e2e38859-80ac-406b-be68-e02b99b2a886

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤭ South East Arrow Crossing North East Arrow' = UNICHAR(10541)
				isHidden
				displayFolder: Arrows
				lineageTag: 9b3c940e-511b-4db2-b875-df015fd48710

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤮ North East Arrow Crossing South East Arrow' = UNICHAR(10542)
				isHidden
				displayFolder: Arrows
				lineageTag: 3aa5debd-06c9-4299-8f39-5e88b9923ed9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤯ Falling Diagonal Crossing North East Arrow' = UNICHAR(10543)
				isHidden
				displayFolder: Arrows
				lineageTag: 940e17f0-fd93-4e3a-b0c0-05192f1c3f0a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤰ Rising Diagonal Crossing South East Arrow' = UNICHAR(10544)
				isHidden
				displayFolder: Arrows
				lineageTag: 554d504b-9a3f-41ff-98d8-4a3462b25a1a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤱ North East Arrow Crossing North West Arrow' = UNICHAR(10545)
				isHidden
				displayFolder: Arrows
				lineageTag: 0fa29a8b-5648-4818-b191-c0ff82cf6a9b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤲ North West Arrow Crossing North East Arrow' = UNICHAR(10546)
				isHidden
				displayFolder: Arrows
				lineageTag: a31aee8a-688b-4975-be5d-d855a30a49e0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤳ Wave Arrow Pointing Directly Right' = UNICHAR(10547)
				isHidden
				displayFolder: Arrows
				lineageTag: 00217abd-b384-42ab-9079-0b3506e57bd6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤶ Arrow Pointing Downwards Then Curving Leftwards' = UNICHAR(10550)
				isHidden
				displayFolder: Arrows
				lineageTag: ddfdd29e-051c-4b29-875b-fff6a8eba628

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤷ Arrow Pointing Downwards Then Curving Rightwards' = UNICHAR(10551)
				isHidden
				displayFolder: Arrows
				lineageTag: bed2b5d3-fe09-443b-a930-4258502e37e9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤸ Right-Side Arc Clockwise Arrow' = UNICHAR(10552)
				isHidden
				displayFolder: Arrows
				lineageTag: ac29f57d-bb40-4b80-92ed-90aa3c50f45a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤹ Left-Side Arc Anticlockwise Arrow' = UNICHAR(10553)
				isHidden
				displayFolder: Arrows
				lineageTag: cba3df99-2b85-4e31-ae11-4b29798efc20

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤺ Top Arc Anticlockwise Arrow' = UNICHAR(10554)
				isHidden
				displayFolder: Arrows
				lineageTag: 2fa46dfa-6bfe-4c8d-80fc-4665a3e23d70

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤻ Bottom Arc Anticlockwise Arrow' = UNICHAR(10555)
				isHidden
				displayFolder: Arrows
				lineageTag: 8ecfdbc4-dc02-4196-9d08-da89224a8a6c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤼ Top Arc Clockwise Arrow With Minus' = UNICHAR(10556)
				isHidden
				displayFolder: Arrows
				lineageTag: 607f42f8-24de-41e2-92e5-99a6fe4225d3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤽ Top Arc Clockwise Arrow With Plus' = UNICHAR(10557)
				isHidden
				displayFolder: Arrows
				lineageTag: 28e4847a-a2da-47e2-b49d-6a9f618c10cb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤾ Lower Right Semicircular Clockwise Arrow' = UNICHAR(10558)
				isHidden
				displayFolder: Arrows
				lineageTag: 5e284432-f739-4b16-b27b-606028883207

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⤿ Lower Left Semicircular Anticlockwise Arrow' = UNICHAR(10559)
				isHidden
				displayFolder: Arrows
				lineageTag: bc6153fe-9a5b-4b35-b071-a2295564afa5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥀ Anticlockwise Closed Circle Arrow' = UNICHAR(10560)
				isHidden
				displayFolder: Arrows
				lineageTag: d40c70ca-70a0-41d5-9386-9c3dc8ae9bc7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥁ Clockwise Closed Circle Arrow' = UNICHAR(10561)
				isHidden
				displayFolder: Arrows
				lineageTag: 0c7450d0-23ee-4913-bb58-db7d89c3845a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥂ Rightwards Arrow Above Short Leftwards Arrow' = UNICHAR(10562)
				isHidden
				displayFolder: Arrows
				lineageTag: 46ab88b5-f66b-4470-a8db-fec28ad2ca81

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥃ Leftwards Arrow Above Short Rightwards Arrow' = UNICHAR(10563)
				isHidden
				displayFolder: Arrows
				lineageTag: a8105475-b5db-4b37-8426-611360b6058a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥄ Short Rightwards Arrow Above Leftwards Arrow' = UNICHAR(10564)
				isHidden
				displayFolder: Arrows
				lineageTag: 7721bd45-c830-4ce1-8158-8d178e348d7e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥅ Rightwards Arrow With Plus Below' = UNICHAR(10565)
				isHidden
				displayFolder: Arrows
				lineageTag: 3b7ce6c0-4094-4e31-9638-70b0ad91d42f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥆ Leftwards Arrow With Plus Below' = UNICHAR(10566)
				isHidden
				displayFolder: Arrows
				lineageTag: 2da79078-a445-4e06-a4d1-c1a2bdb531ac

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥇ Rightwards Arrow Through X' = UNICHAR(10567)
				isHidden
				displayFolder: Arrows
				lineageTag: 960769a5-a8e9-4f96-9388-5c7345f8d817

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥈ Left Right Arrow Through Small Circle' = UNICHAR(10568)
				isHidden
				displayFolder: Arrows
				lineageTag: 7c654e7a-d122-49f2-908c-b23d3cb540f7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥉ Upwards Two-Headed Arrow From Small Circle' = UNICHAR(10569)
				isHidden
				displayFolder: Arrows
				lineageTag: 69da3b7d-d97e-43af-be03-7de5d0c2890c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥊ Left Barb Up Right Barb Down Harpoon' = UNICHAR(10570)
				isHidden
				displayFolder: Arrows
				lineageTag: db72c44a-f78d-4b75-91df-8fab1c8e3092

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥋ Left Barb Down Right Barb Up Harpoon' = UNICHAR(10571)
				isHidden
				displayFolder: Arrows
				lineageTag: 1c469905-428c-44aa-abd8-43d01df7c751

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥌ Up Barb Right Down Barb Left Harpoon' = UNICHAR(10572)
				isHidden
				displayFolder: Arrows
				lineageTag: 74a6d945-210e-4894-a0b1-48f800f3d92e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥍ Up Barb Left Down Barb Right Harpoon' = UNICHAR(10573)
				isHidden
				displayFolder: Arrows
				lineageTag: 4bd99bb5-6312-4ea4-9c0f-ed6ac5e363d5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥎ Left Barb Up Right Barb Up Harpoon' = UNICHAR(10574)
				isHidden
				displayFolder: Arrows
				lineageTag: da0d9d93-a00c-4a6f-b961-a70b015e6cdc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥏ Up Barb Right Down Barb Right Harpoon' = UNICHAR(10575)
				isHidden
				displayFolder: Arrows
				lineageTag: 1cc66aa0-e827-4a55-8e91-5a603a735a6c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥐ Left Barb Down Right Barb Down Harpoon' = UNICHAR(10576)
				isHidden
				displayFolder: Arrows
				lineageTag: 054f53ea-87ad-4e95-b78f-7ec0c3bd66c5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥑ Up Barb Left Down Barb Left Harpoon' = UNICHAR(10577)
				isHidden
				displayFolder: Arrows
				lineageTag: 456f3081-b025-4a64-a053-ff61470e8025

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥒ Leftwards Harpoon With Barb Up To Bar' = UNICHAR(10578)
				isHidden
				displayFolder: Arrows
				lineageTag: 41f04aac-a6f6-4fcf-8d88-d0a0bb471f8b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥓ Rightwards Harpoon With Barb Up To Bar' = UNICHAR(10579)
				isHidden
				displayFolder: Arrows
				lineageTag: 7ab13bd3-95ab-4fbd-a5cd-655d49fb7c8e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥔ Upwards Harpoon With Barb Right To Bar' = UNICHAR(10580)
				isHidden
				displayFolder: Arrows
				lineageTag: 70bf183f-f85e-4240-aa74-62508f82df05

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥕ Downwards Harpoon With Barb Right To Bar' = UNICHAR(10581)
				isHidden
				displayFolder: Arrows
				lineageTag: d9af0029-863d-43b0-9079-f0dc9760495a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥖ Leftwards Harpoon With Barb Down To Bar' = UNICHAR(10582)
				isHidden
				displayFolder: Arrows
				lineageTag: d0c8ce89-c115-475b-82ee-b2e8c295b2f0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥗ Rightwards Harpoon With Barb Down To Bar' = UNICHAR(10583)
				isHidden
				displayFolder: Arrows
				lineageTag: e6410c33-d1f5-4ee6-be27-58ace0239975

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥘ Upwards Harpoon With Barb Left To Bar' = UNICHAR(10584)
				isHidden
				displayFolder: Arrows
				lineageTag: bab8d134-eb2d-458c-a9ca-cdcfd5f10ac6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥙ Downwards Harpoon With Barb Left To Bar' = UNICHAR(10585)
				isHidden
				displayFolder: Arrows
				lineageTag: fc775537-2d56-49fb-8ef7-5f659bf4136f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥚ Leftwards Harpoon With Barb Up From Bar' = UNICHAR(10586)
				isHidden
				displayFolder: Arrows
				lineageTag: bdfd1d34-82ee-4c89-8adf-06a9160147e8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥛ Rightwards Harpoon With Barb Up From Bar' = UNICHAR(10587)
				isHidden
				displayFolder: Arrows
				lineageTag: db37db4c-b277-4d36-b4b3-ea11a88c77ed

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥜ Upwards Harpoon With Barb Right From Bar' = UNICHAR(10588)
				isHidden
				displayFolder: Arrows
				lineageTag: 934b55b7-928f-4379-874b-d6ec5e11e002

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥝ Downwards Harpoon With Barb Right From Bar' = UNICHAR(10589)
				isHidden
				displayFolder: Arrows
				lineageTag: 1363b982-7407-41fc-bc33-9f74e5047233

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥞ Leftwards Harpoon With Barb Down From Bar' = UNICHAR(10590)
				isHidden
				displayFolder: Arrows
				lineageTag: a33bedab-2518-4a3f-a6f3-c1c36f79f74f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥟ Rightwards Harpoon With Barb Down From Bar' = UNICHAR(10591)
				isHidden
				displayFolder: Arrows
				lineageTag: 2141415f-cce0-46de-a7b4-1578a2b7d080

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥠ Upwards Harpoon With Barb Left From Bar' = UNICHAR(10592)
				isHidden
				displayFolder: Arrows
				lineageTag: 53ead531-3a90-4043-bb7d-0db5be1945f7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥡ Downwards Harpoon With Barb Left From Bar' = UNICHAR(10593)
				isHidden
				displayFolder: Arrows
				lineageTag: db02b329-f637-4ab7-b6d0-f64e8f866217

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥢ Leftwards Harpoon With Barb Up Above Leftwards Harpoon With Barb Down' = UNICHAR(10594)
				isHidden
				displayFolder: Arrows
				lineageTag: 9c7afed5-e288-4c46-8a6b-442d77e9bd2d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥤ Upwards Harpoon With Barb Left Beside Upwards Harpoon With Barb Right' = UNICHAR(10595)
				isHidden
				displayFolder: Arrows
				lineageTag: cf023d49-b36c-496b-abb5-5e3bb238d2db

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥥ Rightwards Harpoon With Barb Up Above Rightwards Harpoon With Barb Down' = UNICHAR(10596)
				isHidden
				displayFolder: Arrows
				lineageTag: a18e82b4-dc00-4946-8e21-085d3ba95d8a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥦ Downwards Harpoon With Barb Left Beside Downwards Harpoon With Barb Right' = UNICHAR(10597)
				isHidden
				displayFolder: Arrows
				lineageTag: 29fa0b70-01dd-4f0e-a90f-fa793faf2920

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥧ Leftwards Harpoon With Barb Down Above Rightwards Harpoon With Barb Down' = UNICHAR(10599)
				isHidden
				displayFolder: Arrows
				lineageTag: 868506e6-45cd-46da-9a72-1d9863fd18b2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥨ Rightwards Harpoon With Barb Up Above Leftwards Harpoon With Barb Up' = UNICHAR(10600)
				isHidden
				displayFolder: Arrows
				lineageTag: bbbda999-2391-4fc4-9f5d-83f7ea171a36

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥩ Rightwards Harpoon With Barb Down Above Leftwards Harpoon With Barb Down' = UNICHAR(10601)
				isHidden
				displayFolder: Arrows
				lineageTag: 6bd7b26a-fac8-481c-9243-4fdc87b04190

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥪ Leftwards Harpoon With Barb Up Above Long Dash' = UNICHAR(10602)
				isHidden
				displayFolder: Arrows
				lineageTag: d3fa3166-5f36-4460-b1bb-e84e817b834b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥫ Leftwards Harpoon With Barb Down Below Long Dash' = UNICHAR(10603)
				isHidden
				displayFolder: Arrows
				lineageTag: ccca954b-898e-4424-9817-dbae2edc962a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥬ Rightwards Harpoon With Barb Up Above Long Dash' = UNICHAR(10604)
				isHidden
				displayFolder: Arrows
				lineageTag: 2db0c7c1-b6d0-473c-b00e-c8dea378d40a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥭ Rightwards Harpoon With Barb Down Below Long Dash' = UNICHAR(10605)
				isHidden
				displayFolder: Arrows
				lineageTag: 10681d6c-d848-4665-8f39-44f8359af135

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥮ Upwards Harpoon With Barb Left Beside Downwards Harpoon With Barb Right' = UNICHAR(10606)
				isHidden
				displayFolder: Arrows
				lineageTag: 52f1f215-c577-4f48-b113-e319f6ca87e9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥯ Downwards Harpoon With Barb Left Beside Upwards Harpoon With Barb Right' = UNICHAR(10607)
				isHidden
				displayFolder: Arrows
				lineageTag: 720ec93f-e1a3-49ce-afce-1034217db04f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥰ Right Double Arrow With Rounded Head' = UNICHAR(10608)
				isHidden
				displayFolder: Arrows
				lineageTag: a971b74c-c138-430c-9956-03ed07f74a34

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥱ Equals Sign Above Rightwards Arrow' = UNICHAR(10609)
				isHidden
				displayFolder: Arrows
				lineageTag: 7f93bb67-6afe-4a80-a77c-ac16e2b37ef2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥲ Tilde Operator Above Rightwards Arrow' = UNICHAR(10610)
				isHidden
				displayFolder: Arrows
				lineageTag: 18e71990-9ab8-4390-a1c7-1e621e21001e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥳ Leftwards Arrow Above Tilde Operator' = UNICHAR(10611)
				isHidden
				displayFolder: Arrows
				lineageTag: e87e95ea-509e-473f-8897-5afba86f0bbe

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥴ Rightwards Arrow Above Tilde Operator' = UNICHAR(10612)
				isHidden
				displayFolder: Arrows
				lineageTag: 8547a55e-b5ef-4a08-88d5-a99c8c234240

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥵ Rightwards Arrow Above Almost Equal To' = UNICHAR(10613)
				isHidden
				displayFolder: Arrows
				lineageTag: e5b22577-261f-4a07-9905-68adca9ba2c3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥶ Less-Than Above Leftwards Arrow' = UNICHAR(10614)
				isHidden
				displayFolder: Arrows
				lineageTag: b815e78c-d2ca-4231-bcf8-a941144b94ee

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥷ Leftwards Arrow Through Less-Than' = UNICHAR(10615)
				isHidden
				displayFolder: Arrows
				lineageTag: d95860b2-558c-4352-ac5e-44b7f22ea03b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥸ Greater-Than Above Rightwards Arrow' = UNICHAR(10616)
				isHidden
				displayFolder: Arrows
				lineageTag: ef8a886a-5942-4bcf-8032-c01e2e48f6f9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥹ Rightwards Arrow Through Greater-Than' = UNICHAR(10617)
				isHidden
				displayFolder: Arrows
				lineageTag: 21c09ff3-f725-4980-b661-5bb9a25be12e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥺ Subset Above Rightwards Arrow' = UNICHAR(10618)
				isHidden
				displayFolder: Arrows
				lineageTag: 4da2cb4b-eb07-43f4-863d-93d726098c7d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥻ Leftwards Arrow Through Subset' = UNICHAR(10619)
				isHidden
				displayFolder: Arrows
				lineageTag: ac738343-199c-4184-9456-9b2a3820a3a9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥼ Superset Above Leftwards Arrow' = UNICHAR(10620)
				isHidden
				displayFolder: Arrows
				lineageTag: 6cca1995-7873-41ba-b7fb-c26619458513

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥽ Rightwards Arrow Through Superset' = UNICHAR(10621)
				isHidden
				displayFolder: Arrows
				lineageTag: 0c65dbd8-46fb-44cd-83e8-449d18179ab5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥾ Left Fishtail' = UNICHAR(10622)
				isHidden
				displayFolder: Arrows
				lineageTag: 26a5bb79-b4d5-4b1b-b7e8-ca5f2cd583b8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⥿ Right Fishtail' = UNICHAR(10623)
				isHidden
				displayFolder: Arrows
				lineageTag: 9a44ef03-9636-4898-a46c-d32f0a32cd46

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❯ Rightwards Arrow In Black Circle' = UNICHAR(129150)
				isHidden
				displayFolder: Arrows
				lineageTag: 37dcd300-2f03-4506-8fc6-758b0c98d957

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'V Downwards Arrow In Black Circle' = UNICHAR(129151)
				isHidden
				displayFolder: Arrows
				lineageTag: 52628d38-43ce-495a-b41b-d1447ef20c62

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬀ North East White Arrow' = UNICHAR(11008)
				isHidden
				displayFolder: Arrows
				lineageTag: ed75fd33-2349-4146-a21e-0e88359a3058

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬁ North West White Arrow' = UNICHAR(11009)
				isHidden
				displayFolder: Arrows
				lineageTag: d714213f-725a-435f-9d34-f0f8e91663f5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬂ South East White Arrow' = UNICHAR(11010)
				isHidden
				displayFolder: Arrows
				lineageTag: e7a86931-6729-4801-906a-5e915b9bb79a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬃ South West White Arrow' = UNICHAR(11011)
				isHidden
				displayFolder: Arrows
				lineageTag: 3326ff63-b0f4-45c4-82de-16b33512eb7c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬄ Left Right White Arrow' = UNICHAR(11012)
				isHidden
				displayFolder: Arrows
				lineageTag: 94bdc7d5-35a7-413e-885c-3d45df17e074

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬅ Leftwards Black Arrow' = UNICHAR(11013)
				isHidden
				displayFolder: Arrows
				lineageTag: 172c91ce-27e3-4d19-b0f5-c436468067d8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬆ Upwards Black Arrow' = UNICHAR(11014)
				isHidden
				displayFolder: Arrows
				lineageTag: c8fd1e50-65ac-4cef-8a65-bd01df3486d9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬇ Downwards Black Arrow' = UNICHAR(11015)
				isHidden
				displayFolder: Arrows
				lineageTag: b4d55890-1ddb-407e-9545-a2f641c054c4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬍ North East Black Arrow' = UNICHAR(11021)
				isHidden
				displayFolder: Arrows
				lineageTag: 278ea2e0-181b-4a42-8692-f0464f09062a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬎ North West Black Arrow' = UNICHAR(11022)
				isHidden
				displayFolder: Arrows
				lineageTag: 0a51a871-018a-4494-94a6-9731e9e0e6dd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬏ South East Black Arrow' = UNICHAR(11023)
				isHidden
				displayFolder: Arrows
				lineageTag: 458868c5-0155-4e18-b862-be8731049176

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬐ South West Black Arrow' = UNICHAR(11024)
				isHidden
				displayFolder: Arrows
				lineageTag: 1408600b-1ca4-4825-b4ce-5254741b8fdb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬑ Left Right Black Arrow' = UNICHAR(11025)
				isHidden
				displayFolder: Arrows
				lineageTag: 58961369-4a80-4fc0-989c-9d252df1dee4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬒ Up Down Black Arrow' = UNICHAR(11026)
				isHidden
				displayFolder: Arrows
				lineageTag: 59be52aa-a352-4ead-9d44-80e079665242

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬓ Rightwards Arrow With Tip Downwards' = UNICHAR(11027)
				isHidden
				displayFolder: Arrows
				lineageTag: 8bfc3b08-d17b-47f4-8e90-76fdc15d1729

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬔ Rightwards Arrow With Tip Upwards' = UNICHAR(11028)
				isHidden
				displayFolder: Arrows
				lineageTag: 588b88de-83c3-45f5-905a-05bc34cf6c54

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬕ Leftwards Arrow With Tip Downwards' = UNICHAR(11029)
				isHidden
				displayFolder: Arrows
				lineageTag: 19ff1e23-4fd5-4b19-9f1d-9f5f987153f2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬖ Leftwards Arrow With Tip Upwards' = UNICHAR(11030)
				isHidden
				displayFolder: Arrows
				lineageTag: f7d03720-ed8f-4179-9ef6-bb7f90b3b4ac

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬰ Left Arrow With Small Circle' = UNICHAR(11056)
				isHidden
				displayFolder: Arrows
				lineageTag: d6c71ce4-4b60-4e8a-8d81-0ca943b42c2a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬱ Three Leftwards Arrows' = UNICHAR(11057)
				isHidden
				displayFolder: Arrows
				lineageTag: ef7fc8f2-0136-4261-a20e-442fad09036a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬲ Left Arrow With Circled Plus' = UNICHAR(11058)
				isHidden
				displayFolder: Arrows
				lineageTag: c668c9b3-5938-4721-8e35-47bfd6df6965

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬳ Long Leftwards Squiggle Arrow' = UNICHAR(11059)
				isHidden
				displayFolder: Arrows
				lineageTag: a57c95e8-8f6c-475c-9840-cee529557397

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬴ Leftwards Two-Headed Arrow With Vertical Stroke' = UNICHAR(11060)
				isHidden
				displayFolder: Arrows
				lineageTag: 8d4ebb64-3946-4863-8a51-738d835edb84

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬵ Leftwards Two-Headed Arrow With Double Vertical Stroke' = UNICHAR(11061)
				isHidden
				displayFolder: Arrows
				lineageTag: d02e8506-5402-4e12-abf2-75d0f6e2be46

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬶ Leftwards Two-Headed Arrow From Bar' = UNICHAR(11062)
				isHidden
				displayFolder: Arrows
				lineageTag: de31b43d-3358-4e4b-aa97-a1692e32538f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬷ Leftwards Two-Headed Triple Dash Arrow' = UNICHAR(11063)
				isHidden
				displayFolder: Arrows
				lineageTag: 8e605cff-aa0b-48eb-9e52-c640ebf6fce3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬸ Leftwards Arrow With Dotted Stem' = UNICHAR(11064)
				isHidden
				displayFolder: Arrows
				lineageTag: 0ea667fe-d4ab-4a64-a873-428c306752ff

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬹ Leftwards Arrow With Tail With Vertical Stroke' = UNICHAR(11065)
				isHidden
				displayFolder: Arrows
				lineageTag: f0e035ca-b4d9-4dd5-8f91-63750b5bd28a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬺ Leftwards Arrow With Tail With Double Vertical Stroke' = UNICHAR(11066)
				isHidden
				displayFolder: Arrows
				lineageTag: 895eda55-d2bb-4135-b273-070c231c8687

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬻ Leftwards Two-Headed Arrow With Tail' = UNICHAR(11067)
				isHidden
				displayFolder: Arrows
				lineageTag: 6eefca48-bd77-457e-8868-0ad058e67567

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬼ Leftwards Two-Headed Arrow With Tail With Vertical Stroke' = UNICHAR(11068)
				isHidden
				displayFolder: Arrows
				lineageTag: 9ea044ec-f303-426f-881c-08e187ff56e2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬽ Leftwards Two-Headed Arrow With Tail With Double Vertical Stroke' = UNICHAR(11069)
				isHidden
				displayFolder: Arrows
				lineageTag: cab90fe0-dc93-46a5-8ef8-bc5a18d72324

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬾ Leftwards Arrow Through X' = UNICHAR(11070)
				isHidden
				displayFolder: Arrows
				lineageTag: 221cc1cf-32ae-41e2-b62a-70d928bde3ef

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬿ Wave Arrow Pointing Directly Left' = UNICHAR(11071)
				isHidden
				displayFolder: Arrows
				lineageTag: ef2c6670-8cb9-4bc0-b06e-1cfd48739f6a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭀ Equals Sign Above Leftwards Arrow' = UNICHAR(11072)
				isHidden
				displayFolder: Arrows
				lineageTag: 2a5e8fb7-5882-4d39-a7ac-02fc11ae7815

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭁ Reverse Tilde Operator Above Leftwards Arrow' = UNICHAR(11073)
				isHidden
				displayFolder: Arrows
				lineageTag: c2a5fb7a-d8d4-4944-9563-3e2eb82b10cd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭂ Leftwards Arrow Above Reverse Almost Equal To' = UNICHAR(11074)
				isHidden
				displayFolder: Arrows
				lineageTag: 40cb92c1-f783-4814-9467-93a42306ed56

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭃ Rightwards Arrow Through Greater-Than Or Equal To' = UNICHAR(11075)
				isHidden
				displayFolder: Arrows
				lineageTag: f719f861-3194-431a-831a-d7d16bb4f66d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭅ Leftwards Quadruple Arrow' = UNICHAR(11077)
				isHidden
				displayFolder: Arrows
				lineageTag: e75e3674-a1ab-4943-b15e-a61ce8f9f716

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭆ Rightwards Quadruple Arrow' = UNICHAR(11078)
				isHidden
				displayFolder: Arrows
				lineageTag: 81cc908a-ccfa-44ac-9dd6-773ecac38405

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭇ Reverse Tilde Operator Above Rightwards Arrow' = UNICHAR(11079)
				isHidden
				displayFolder: Arrows
				lineageTag: 8e860aff-ace8-4d97-9182-cc86df7e0a2a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭈ Rightwards Arrow Above Reverse Almost Equal To' = UNICHAR(11080)
				isHidden
				displayFolder: Arrows
				lineageTag: 226b4fb9-e743-4843-a2ea-cfdeb3e8d4af

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭉ Tilde Operator Above Leftwards Arrow' = UNICHAR(11081)
				isHidden
				displayFolder: Arrows
				lineageTag: 1b75ed10-39a9-439b-83dc-59b1bd097762

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭊ Leftwards Arrow Above Almost Equal To' = UNICHAR(11082)
				isHidden
				displayFolder: Arrows
				lineageTag: b75859cf-bbc2-458b-8024-add04620608c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭋ Leftwards Arrow Above Reverse Tilde Operator' = UNICHAR(11083)
				isHidden
				displayFolder: Arrows
				lineageTag: a2427289-0241-409b-abf1-f48313bfcce8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭌ Rightwards Arrow Above Reverse Tilde Operator' = UNICHAR(11084)
				isHidden
				displayFolder: Arrows
				lineageTag: 1c0058cb-1ad2-432b-9b04-f2eebd90e0cf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⇧ Upwards White Arrow' = UNICHAR(8679)
				isHidden
				displayFolder: Arrows
				lineageTag: a9bd50b6-b0cd-4007-805d-573de7758a2e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '± Plus-Minus Sign' = UNICHAR(177)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 523867ae-aa62-4088-a516-1ca182abf097

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∓ Minus-Plus Sign' = UNICHAR(8723)
				isHidden
				displayFolder: Math Symbols
				lineageTag: c51bf324-bd3c-4578-9552-1d435f136a25

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '× Multiplication Sign' = UNICHAR(215)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6b898d1a-a5dc-4024-8aa8-767e5acddcd4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '÷ Division Sign' = UNICHAR(247)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1bf5b730-53ac-4f41-aee9-97444fa2ce99

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∕ Division Slash' = UNICHAR(8725)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 37ee21e6-696d-494e-958d-eafa052aa994

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁄ Fraction Slash' = UNICHAR(8260)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e5ae050a-ad3c-4c27-8436-9b854592f343

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∑ N-Ary Summation' = UNICHAR(8721)
				isHidden
				displayFolder: Math Symbols
				lineageTag: d1ccee83-897e-42a4-991a-7d555b6cffbc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∏ N-Ary Product' = UNICHAR(8719)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 007bac59-694a-41e1-b550-b15f91690393

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '√ Square Root' = UNICHAR(8730)
				isHidden
				displayFolder: Math Symbols
				lineageTag: d7049405-b147-48f6-9716-10fa386dbcf8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∛ Cube Root' = UNICHAR(8731)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ab96aaa6-6455-448b-9f66-416f45c87759

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∜ Fourth Root' = UNICHAR(8732)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 0aff44e0-661f-4ce9-b2e4-9373a6ee5971

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∞ Infinity' = UNICHAR(8734)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 85cb5182-cc3a-4372-a027-052fc40ba50e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∫ Integral' = UNICHAR(8747)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ec440903-5590-41b2-a1f8-e35fd14fe2c2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∬ Double Integral' = UNICHAR(8748)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 911883b0-7ec2-402b-848a-aeb1009aec28

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∮ Contour Integral' = UNICHAR(8750)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6b7d3b2c-471b-4e86-b9ed-e780ec1f07fa

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≈ Almost Equal To' = UNICHAR(8776)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8eb4036f-c657-4c42-9d2c-c26c0d9e7481

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≠ Not Equal To' = UNICHAR(8800)
				isHidden
				displayFolder: Math Symbols
				lineageTag: da0dad6b-0cad-4f6f-870a-0d4c30c4a3e8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≤ Less-Than Or Equal To' = UNICHAR(8804)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 59b6fda3-9aa6-4e7b-bcea-b5807edab44d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≥ Greater-Than Or Equal To' = UNICHAR(8805)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e7836291-38b7-49e5-8858-d29a0bc12033

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≪ Much Less-Than' = UNICHAR(8810)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6173e6ab-6dfb-4d38-bde7-216325638fc9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≫ Much Greater-Than' = UNICHAR(8811)
				isHidden
				displayFolder: Math Symbols
				lineageTag: bb2b7040-9983-4161-b4ae-9230f6ce2b39

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≡ Identical To' = UNICHAR(8801)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1234d9fb-8bd5-4573-95da-3f84f110d0d8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∼ Tilde Operator' = UNICHAR(8764)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7ea627cf-c109-4905-a490-b5108d5f9a35

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∝ Proportional To' = UNICHAR(8733)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 5489406d-45be-4262-84d5-c83fe7f3e711

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∂ Partial Differential' = UNICHAR(8706)
				isHidden
				displayFolder: Math Symbols
				lineageTag: faeda308-969f-4d83-9f7e-cc906469b8de

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∇ Nabla' = UNICHAR(8711)
				isHidden
				displayFolder: Math Symbols
				lineageTag: b7fdd477-0000-473d-a5e2-6d156f7eb9d7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∈ Element Of' = UNICHAR(8712)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 187dbc39-93a5-48ae-8a0c-3d7c7729d58e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∉ Not An Element Of' = UNICHAR(8713)
				isHidden
				displayFolder: Math Symbols
				lineageTag: b3e05474-7de0-4a9d-9a12-a7a727b1f838

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∋ Contains As Member' = UNICHAR(8715)
				isHidden
				displayFolder: Math Symbols
				lineageTag: acc524fa-3398-44bb-b724-f3003fcefded

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊆ Subset Of Or Equal To' = UNICHAR(8838)
				isHidden
				displayFolder: Math Symbols
				lineageTag: be03e9d4-e4bf-4aa8-8704-700bf9d3a80a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊇ Superset Of Or Equal To' = UNICHAR(8839)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 33d852fa-57f0-4925-9e6a-ad471fc0d6b9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊂ Subset Of' = UNICHAR(8834)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 0d18e875-1356-4833-9c66-571eae7dac66

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊃ Superset Of' = UNICHAR(8835)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 5f0a9fee-8441-405d-b226-69a30580bd57

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∪ Union' = UNICHAR(8746)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 18f0cc7e-3863-4e8b-b978-0451c4a48186

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∩ Intersection' = UNICHAR(8745)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 736ac5ff-ab9b-4138-9824-87f66d12c7c1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∧ Logical And' = UNICHAR(8743)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 2ea71039-16a8-412a-822f-d4cb4144a4d0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∨ Logical Or' = UNICHAR(8744)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 500896e8-993e-4d07-8348-414d07e511bd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¬ Not Sign' = UNICHAR(172)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 12951489-7141-42ba-8140-ad7ed3b487f0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∀ For All' = UNICHAR(8704)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 9bc63b3a-c1b0-4bf1-9e3a-a376afe7a0ef

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∃ There Exists' = UNICHAR(8707)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 5bb2b80e-5c73-4a1b-bfa1-a9238db28df7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∴ Therefore' = UNICHAR(8756)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1a08f26c-bb9e-4b5c-8dcd-bc922c0283e6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∵ Because' = UNICHAR(8757)
				isHidden
				displayFolder: Math Symbols
				lineageTag: c114f299-8ab8-42a3-8d0c-d8103fda103c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∠ Angle' = UNICHAR(8736)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 986de6b6-9630-401a-95bf-8c63cae6bf4b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊥ Up Tack' = UNICHAR(8869)
				isHidden
				displayFolder: Math Symbols
				lineageTag: c455cb05-1e3c-4209-b050-a6b86667c5f8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∥ Parallel To' = UNICHAR(8741)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 23fa0816-1228-43ea-80f2-b916ad8bcff8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '° Degree Sign' = UNICHAR(176)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 152d8e5a-f77d-48d9-9087-168373802ed6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '′ Prime' = UNICHAR(8242)
				isHidden
				displayFolder: Math Symbols
				lineageTag: f3cb88c6-3814-43c9-b406-cd5a7e16beb6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '″ Double Prime' = UNICHAR(8243)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1db1d151-ecf0-449e-894b-dd1cd950bf92

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'µ Micro Sign' = UNICHAR(181)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 17eb4edb-0b22-4949-ba89-634be448d6b3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'π Greek Small Letter Pi' = UNICHAR(960)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6aff3be0-8820-4558-8560-9f32bb3e054f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure '∅ Empty Set' = UNICHAR(8709)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 4e28fc25-46bc-4d86-8bf4-aebc15292892

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∆ Increment' = UNICHAR(8710)
				isHidden
				displayFolder: Math Symbols
				lineageTag: c257d966-6420-46a2-9af6-3482480f3e04

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∎ End Of Proof' = UNICHAR(8718)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 42cadcd3-f8c7-42b6-9db2-b4d48fdb0e97

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∶ Ratio' = UNICHAR(8758)
				isHidden
				displayFolder: Math Symbols
				lineageTag: bab4be46-fc79-4462-9600-0594b0136d84

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∷ Proportion' = UNICHAR(8759)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 4b5772fd-8217-4eb5-979c-810ecdebe5f3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∽ Reversed Tilde' = UNICHAR(8765)
				isHidden
				displayFolder: Math Symbols
				lineageTag: b0c5e4c5-273f-4282-9e4e-6c4fae295e00

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊕ Circled Plus' = UNICHAR(8853)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1dd62329-f947-41a6-99fe-93894e4f49ce

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊖ Circled Minus' = UNICHAR(8854)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 34b8b961-0f6c-45ad-b52f-83613bc15e29

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊗ Circled Times' = UNICHAR(8855)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 4968cc3f-0c52-4901-9d1c-6e57350d24a7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊘ Circled Division Slash' = UNICHAR(8856)
				isHidden
				displayFolder: Math Symbols
				lineageTag: a4ef375f-1102-4a6b-9131-6a97ccf3d77b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊙ Circled Dot Operator' = UNICHAR(8857)
				isHidden
				displayFolder: Math Symbols
				lineageTag: f7822e1b-b141-47a7-9012-f26ca5634f74

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∘ Ring Operator' = UNICHAR(8728)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 94335b95-2e3c-4bd2-9a18-93e7eeda4b94

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∙ Bullet Operator' = UNICHAR(8729)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 01ab23b6-667a-4349-8668-7b78cc8db15e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∟ Right Angle' = UNICHAR(8735)
				isHidden
				displayFolder: Math Symbols
				lineageTag: a8e7b5fe-95db-41c3-915d-5e65026506f1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∭ Triple Integral' = UNICHAR(8749)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 0485ae25-bc3b-40d1-82b1-91cad76e5f09

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∯ Surface Integral' = UNICHAR(8751)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 61594c9b-5a61-487f-9295-62eae0d3c815

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∰ Volume Integral' = UNICHAR(8752)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 0e2dd22d-82b2-4b1f-981f-6964f2a14937

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∱ Clockwise Integral' = UNICHAR(8753)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 50cb86e0-5802-4e0f-bbd3-b6de34b7412b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∲ Clockwise Contour Integral' = UNICHAR(8754)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 3f22577b-5149-4e5f-9165-4f0b5f795fcc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∳ Anticlockwise Contour Integral' = UNICHAR(8755)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 31220d21-13aa-47dd-b09f-d2bd3d8d7cdc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≌ All Equal To' = UNICHAR(8780)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ac615b40-3716-475a-9542-33e89658bd64

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≅ Approximately Equal To' = UNICHAR(8773)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 0c831cff-fc5f-468e-a0a7-5dca7c3ae7df

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≦ Less-Than Over Equal To' = UNICHAR(8806)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7d796a09-f786-4f9f-bfe8-493f57a2b2a7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '≧ Greater-Than Over Equal To' = UNICHAR(8807)
				isHidden
				displayFolder: Math Symbols
				lineageTag: f297fe77-d218-43f0-8fb0-eb4bce5eb4cb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊄ Not A Subset Of' = UNICHAR(8836)
				isHidden
				displayFolder: Math Symbols
				lineageTag: df85a56a-c4ba-4720-a9d8-59c7d4088709

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊅ Not A Superset Of' = UNICHAR(8837)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e7b35e77-ce02-453c-b8b9-8e1fa51d3e77

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊈ Neither A Subset Of Nor Equal To' = UNICHAR(8840)
				isHidden
				displayFolder: Math Symbols
				lineageTag: a60a58c6-3bfd-4c56-9a13-d8d1c0f28fbc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊉ Neither A Superset Of Nor Equal To' = UNICHAR(8841)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 9df90b56-0e59-4210-bfcb-37d25e7b764a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊊ Subset Of With Not Equal To' = UNICHAR(8842)
				isHidden
				displayFolder: Math Symbols
				lineageTag: a5f050db-8651-4a07-9040-844395096ee0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊋ Superset Of With Not Equal To' = UNICHAR(8843)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e94941a8-d2cc-45ca-b4d4-16633ffb5bb3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊤ Down Tack' = UNICHAR(8868)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7bce8634-8aae-4bd4-b4f4-d60c26e4d6b1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊢ Right Tack' = UNICHAR(8866)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8839876e-4a5a-43a9-99df-c38dc8c3da67

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⊣ Left Tack' = UNICHAR(8867)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 71ee553b-dd3b-4413-92e3-7e293c97ae27

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∣ Divides' = UNICHAR(8739)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 62620187-5302-4771-8644-2eae2f479503

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∤ Does Not Divide' = UNICHAR(8740)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ad387ca3-b932-440c-9140-6c0ea98a0d51

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '∦ Not Parallel To' = UNICHAR(8742)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 5989170d-ba6f-4ef8-b7a5-2a33f17fe34e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌈ Left Ceiling' = UNICHAR(8968)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 08f028da-d2fc-4d87-a5ad-af65a9d6b012

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌉ Right Ceiling' = UNICHAR(8969)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 38a0ad5d-ae28-435f-8042-dc632eb62d6a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌊ Left Floor' = UNICHAR(8970)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 9e275b9d-1e24-446a-8a14-d113d0af3abd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌋ Right Floor' = UNICHAR(8971)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 323414d6-2862-45e5-8361-ea1bedf3479a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ℏ Planck Constant Over Two Pi' = UNICHAR(8463)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 090662ab-96c0-461b-b568-097051081fbe

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ℵ Alef Symbol' = UNICHAR(8501)
				isHidden
				displayFolder: Math Symbols
				lineageTag: a43d6d8e-7555-4ab4-b4c4-bc77a0d11307

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ℶ Bet Symbol' = UNICHAR(8502)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 53e07900-e93e-498a-b9da-e8c9b09c3b44

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ℷ Gimel Symbol' = UNICHAR(8503)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 9496928c-326f-400a-805f-43d8baf81f2b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ℸ Dalet Symbol' = UNICHAR(8504)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e865d85d-c4da-494a-9004-5a0757fe8642

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¹ Superscript One' = UNICHAR(185)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7eccee8a-375c-490d-942d-406145243983

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '² Superscript Two' = UNICHAR(178)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6c8d0c6d-8657-44db-9837-cef49e810b55

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '³ Superscript Three' = UNICHAR(179)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ab4fef81-8de5-410a-9e4c-0e3c5884bb55

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁴ Superscript Four' = UNICHAR(8308)
				isHidden
				displayFolder: Math Symbols
				lineageTag: f6bbc335-2fd0-4268-ad9e-5ad8f6053cba

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁵ Superscript Five' = UNICHAR(8309)
				isHidden
				displayFolder: Math Symbols
				lineageTag: c6cd681b-f9ae-4e82-b78f-f49be199b012

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁶ Superscript Six' = UNICHAR(8310)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8318662c-98c0-4b1e-825c-0d947592b910

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁷ Superscript Seven' = UNICHAR(8311)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e14a3a33-4db3-45e9-8fa7-608508e8b19c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁸ Superscript Eight' = UNICHAR(8312)
				isHidden
				displayFolder: Math Symbols
				lineageTag: d5cfc176-2bd9-4896-873a-d347c7ae7283

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁹ Superscript Nine' = UNICHAR(8313)
				isHidden
				displayFolder: Math Symbols
				lineageTag: acd5165c-faa4-4e87-b419-7c67a11c5c5e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁰ Superscript Zero' = UNICHAR(8304)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 3d6732f1-e94e-487a-a88a-873f78af65a0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁺ Superscript Plus Sign' = UNICHAR(8314)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e09153bc-b89d-4196-b203-e9993b5a9fbc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁻ Superscript Minus' = UNICHAR(8315)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 149be884-8205-4bfc-95e0-01564c7a8283

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁼ Superscript Equals Sign' = UNICHAR(8316)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 197e7a6b-8701-4c5d-bd6d-717864f17cb9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁽ Superscript Left Parenthesis' = UNICHAR(8317)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8411599a-9760-42ba-b87a-ad9299767e67

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁾ Superscript Right Parenthesis' = UNICHAR(8318)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7ae1462d-e859-46a4-8491-506be6f5d217

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ⁿ Superscript Latin Small Letter N' = UNICHAR(8319)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 668d58f2-4912-443c-9de6-b86898e5522d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₀ Subscript Zero' = UNICHAR(8320)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 13136a97-e75c-4097-b113-04f9806a54e6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₁ Subscript One' = UNICHAR(8321)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 977393db-fc5a-4b7e-be4a-5186399e3806

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₂ Subscript Two' = UNICHAR(8322)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 308c9c6b-31c3-4ea6-a794-9d3f83dad2a6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₃ Subscript Three' = UNICHAR(8323)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 41b17bf1-70ba-4b43-9f2f-d2286be0ed14

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₄ Subscript Four' = UNICHAR(8324)
				isHidden
				displayFolder: Math Symbols
				lineageTag: fc90732f-60f0-4093-91dd-6dd4bb45215d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₅ Subscript Five' = UNICHAR(8325)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 4010cc2c-4504-4167-810e-60c5247fcf19

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₆ Subscript Six' = UNICHAR(8326)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1a8f7300-5ccb-4650-a909-280747c9a032

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₇ Subscript Seven' = UNICHAR(8327)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 9fc99dd1-c2f1-486a-8c5e-9edc3fa99bb3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₈ Subscript Eight' = UNICHAR(8328)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8c28a118-a52d-456a-9744-1334415d9615

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₉ Subscript Nine' = UNICHAR(8329)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ee26e107-aa52-404b-9d95-e1179b443a5c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₊ Subscript Plus Sign' = UNICHAR(8330)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 851ce77c-3f61-4d21-b9ac-fe7da99558c7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₋ Subscript Minus' = UNICHAR(8331)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 56d78477-4c46-4a07-b5e4-81ab5f629437

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₌ Subscript Equals Sign' = UNICHAR(8332)
				isHidden
				displayFolder: Math Symbols
				lineageTag: bd8d46d6-28d8-41ce-a1e0-c40dfcd53cef

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₍ Subscript Left Parenthesis' = UNICHAR(8333)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 3fb0efcf-cded-435c-9306-7ebe51ded96b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₎ Subscript Right Parenthesis' = UNICHAR(8334)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 4d40cd15-a4e8-40c2-b414-25d228403a2f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅟ Fraction Numerator One' = UNICHAR(8543)
				isHidden
				displayFolder: Math Symbols
				lineageTag: dee88b3d-d1bb-4e89-b3ec-6fd84c10f89b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '½ Vulgar Fraction One Half' = UNICHAR(189)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ebeb23be-eaa4-4552-b515-a0c6697260ff

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅓ Vulgar Fraction One Third' = UNICHAR(8531)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8ded5634-713d-4144-b056-150d714a1e7b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅔ Vulgar Fraction Two Thirds' = UNICHAR(8532)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6a9b64c8-96e5-473a-8845-4718997a32eb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¼ Vulgar Fraction One Quarter' = UNICHAR(188)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 07a69ffe-fb44-431a-89ee-6cfef4024882

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¾ Vulgar Fraction Three Quarters' = UNICHAR(190)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 027c3615-c17d-4f2a-8105-c1ddbed50215

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅕ Vulgar Fraction One Fifth' = UNICHAR(8533)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7ed9f75e-6c5d-4a2e-8348-8ed81d729b7e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅖ Vulgar Fraction Two Fifths' = UNICHAR(8534)
				isHidden
				displayFolder: Math Symbols
				lineageTag: a2d1921a-31f9-40ae-ad0a-5c214ed29265

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅗ Vulgar Fraction Three Fifths' = UNICHAR(8535)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 33894cf0-ab3f-48bd-8912-11f7d3c4e2d0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅘ Vulgar Fraction Four Fifths' = UNICHAR(8536)
				isHidden
				displayFolder: Math Symbols
				lineageTag: afda7bd5-24bd-49cc-9c0c-0495e92256c0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅙ Vulgar Fraction One Sixth' = UNICHAR(8537)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 99931927-5a45-4eb6-b147-0d404fdd3ff1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅚ Vulgar Fraction Five Sixths' = UNICHAR(8538)
				isHidden
				displayFolder: Math Symbols
				lineageTag: eb4b6982-8551-4358-ad55-f33c3d2f4bfb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅛ Vulgar Fraction One Eighth' = UNICHAR(8539)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1676213a-3504-4fe4-847a-87fb3dcccc50

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅜ Vulgar Fraction Three Eighths' = UNICHAR(8540)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 41d7b513-2ebe-49b2-90ef-ffa65fd14406

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅝ Vulgar Fraction Five Eighths' = UNICHAR(8541)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 897346dc-044d-408e-8093-d89ce0154799

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⅞ Vulgar Fraction Seven Eighths' = UNICHAR(8542)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e48a9654-620f-4107-9031-ddab9e7b2726

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '↉ Vulgar Fraction Zero Thirds' = UNICHAR(8585)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 13428e28-2da8-4145-95c0-f54d1abe356c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅰ Roman Numeral One' = UNICHAR(8544)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 7dfd7059-159e-48f2-a4c7-a4120e6628a8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅱ Roman Numeral Two' = UNICHAR(8545)
				isHidden
				displayFolder: Math Symbols
				lineageTag: d90d19d9-897e-488c-92b4-32d387a0a605

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅲ Roman Numeral Three' = UNICHAR(8546)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 717881ea-14e9-4b4c-8f9e-02cf2fa85869

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅳ Roman Numeral Four' = UNICHAR(8547)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e85c93f6-b83c-431e-8cba-49851af8e0db

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅴ Roman Numeral Five' = UNICHAR(8548)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 5a5e4a9f-8e71-4b38-965f-668bc82f8030

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅵ Roman Numeral Six' = UNICHAR(8549)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 20077343-dc01-4b06-b6ed-f99e0252a35c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅶ Roman Numeral Seven' = UNICHAR(8550)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 74a1fecf-ed88-4c74-b172-180a0f3350f5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅷ Roman Numeral Eight' = UNICHAR(8551)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 1495d367-7df1-4aff-8731-df47aad0fa59

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅸ Roman Numeral Nine' = UNICHAR(8552)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 91d70f41-d441-4b67-a1a4-f2e18a10eea4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅹ Roman Numeral Ten' = UNICHAR(8553)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 4847619e-8156-4698-b9f4-e047f193ff20

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅺ Roman Numeral Eleven' = UNICHAR(8554)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 8a29470e-4e42-4555-9afe-30644915d17d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅻ Roman Numeral Twelve' = UNICHAR(8555)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 19c1f764-25f4-4a45-8611-899b23366cbe

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅼ Roman Numeral Fifty' = UNICHAR(8556)
				isHidden
				displayFolder: Math Symbols
				lineageTag: d65b475b-93c2-458f-ae2f-55357992e6d8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅽ Roman Numeral One Hundred' = UNICHAR(8557)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 2b9b276d-975c-4fa5-9e90-aa16cfbb4d28

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅾ Roman Numeral Five Hundred' = UNICHAR(8558)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 61b5a575-fd10-4752-9ca8-7c989aff59d1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'Ⅿ Roman Numeral One Thousand' = UNICHAR(8559)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e6789c7a-4e8d-42ce-afc1-30f7006264d4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ⅰ Small Roman Numeral One' = UNICHAR(8560)
				isHidden
				displayFolder: Math Symbols
				lineageTag: bc66ca12-56d4-4f73-89f5-521b29acdd45

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅱ Small Roman Numeral Two' = UNICHAR(8561)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 96d95920-5685-4d94-9af7-d2bbea36ecd6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅲ Small Roman Numeral Three' = UNICHAR(8562)
				isHidden
				displayFolder: Math Symbols
				lineageTag: f9ed1bef-57c2-4e08-908e-38a4728d102b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅳ Small Roman Numeral Four' = UNICHAR(8563)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 408cc8b0-e2bd-4893-920a-d4a84e75b092

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅴ Small Roman Numeral Five' = UNICHAR(8564)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 751f9db2-845a-4241-ba23-33f0a04aa197

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅵ Small Roman Numeral Six' = UNICHAR(8565)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 9754122b-79ec-4e4e-acde-0559e15da454

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅶ Small Roman Numeral Seven' = UNICHAR(8566)
				isHidden
				displayFolder: Math Symbols
				lineageTag: d05c0a64-f0f1-4d23-a2f5-f51e0b819625

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅷ Small Roman Numeral Eight' = UNICHAR(8567)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 067426ca-54ac-4958-a553-427767ab9b58

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅸ Small Roman Numeral Nine' = UNICHAR(8568)
				isHidden
				displayFolder: Math Symbols
				lineageTag: db32dfbd-ea63-4f30-9377-dc6b89fc92e6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅹ Small Roman Numeral Ten' = UNICHAR(8569)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 07049811-3927-40f5-a7cb-40dba7a4ea9a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅺ Small Roman Numeral Eleven' = UNICHAR(8570)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 67654930-e4a9-4506-b9fe-3c7d9bb3a1c3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅻ Small Roman Numeral Twelve' = UNICHAR(8571)
				isHidden
				displayFolder: Math Symbols
				lineageTag: f89a0451-7568-4440-baf9-56df6e11fa64

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅼ Small Roman Numeral Fifty' = UNICHAR(8572)
				isHidden
				displayFolder: Math Symbols
				lineageTag: ba0b4535-f97a-47da-8dab-6281655ee557

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅽ Small Roman Numeral One Hundred' = UNICHAR(8573)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 6f8b2fbf-3b54-4398-bc58-6b9b60b7b55c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅾ Small Roman Numeral Five Hundred' = UNICHAR(8574)
				isHidden
				displayFolder: Math Symbols
				lineageTag: fd87542c-a5e3-4c9b-8dfe-f76bdfa911ab

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure 'ⅿ Small Roman Numeral One Thousand' = UNICHAR(8575)
				isHidden
				displayFolder: Math Symbols
				lineageTag: 65289b05-7bd6-4199-a482-936e5a5a4cfd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure '% Percent Sign' = UNICHAR(37)
				isHidden
				displayFolder: Math Symbols
				lineageTag: b23b1d8c-2242-42b3-874d-e11882c053ab

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‰ Per Mille Sign' = UNICHAR(8240)
				isHidden
				displayFolder: Math Symbols
				lineageTag: e7a316b1-f289-4080-91d1-3575deb1a5e3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‱ Per Ten Thousand Sign' = UNICHAR(8241)
				isHidden
				displayFolder: Math Symbols
				lineageTag: b12c8bd3-5f8c-432d-ac57-340ec75de7a0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '€ Euro Sign' = UNICHAR(8364)
				isHidden
				displayFolder: Currency
				lineageTag: ca3799c0-5e52-4f03-a6fd-3c7e71fb2484

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '£ Pound Sign' = UNICHAR(163)
				isHidden
				displayFolder: Currency
				lineageTag: 00567a20-8eef-4c96-902b-70abf55861d5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '$$ Dollar Sign' = UNICHAR(36)
				isHidden
				displayFolder: Currency
				lineageTag: 720ad076-8365-4c1c-bb00-50bd2e8eadfa

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¢ Cent Sign' = UNICHAR(162)
				isHidden
				displayFolder: Currency
				lineageTag: bb72029e-e1ae-4464-ad39-665fdc89964d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¥ Yen Sign' = UNICHAR(165)
				isHidden
				displayFolder: Currency
				lineageTag: f44f47d7-5110-4574-b214-c6bf6a8a0a6e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₹ Indian Rupee Sign' = UNICHAR(8377)
				isHidden
				displayFolder: Currency
				lineageTag: 120887b0-bd64-4ce2-82f2-5787521b3437

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₽ Ruble Sign' = UNICHAR(8381)
				isHidden
				displayFolder: Currency
				lineageTag: ad41b634-ffc0-4e9e-bef9-5b79d3961d78

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '₩ Won Sign' = UNICHAR(8361)
				isHidden
				displayFolder: Currency
				lineageTag: 2ad960ab-22f9-4f8f-a580-8908fd227efb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₪ New Sheqel Sign' = UNICHAR(8362)
				isHidden
				displayFolder: Currency
				lineageTag: 4570575d-bab7-4151-99ef-67d2febf6e56

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₫ Dong Sign' = UNICHAR(8363)
				isHidden
				displayFolder: Currency
				lineageTag: 465a10d3-0db7-4317-b315-cce4195f0a5f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₭ Kip Sign' = UNICHAR(8365)
				isHidden
				displayFolder: Currency
				lineageTag: 0f14b6b9-0c46-4519-a033-84f92863e6f2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₮ Tugrik Sign' = UNICHAR(8366)
				isHidden
				displayFolder: Currency
				lineageTag: e2a5164c-41af-4121-84fb-c4902264512a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₯ Drachma Sign' = UNICHAR(8367)
				isHidden
				displayFolder: Currency
				lineageTag: cff73ae2-3abd-41fa-99e0-36b147a44fa3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₲ Guarani Sign' = UNICHAR(8370)
				isHidden
				displayFolder: Currency
				lineageTag: 8dbe532d-649b-4432-941d-15dcbb4faf32

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₴ Hryvnia Sign' = UNICHAR(8372)
				isHidden
				displayFolder: Currency
				lineageTag: 580b5eff-e126-4662-96ae-ec77c71f50d5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₵ Cedi Sign' = UNICHAR(8373)
				isHidden
				displayFolder: Currency
				lineageTag: 390a000f-44a4-4cb2-86f1-8ce4040e7a42

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₸ Tenge Sign' = UNICHAR(8376)
				isHidden
				displayFolder: Currency
				lineageTag: c3ad2edf-50e0-4880-9aa6-768cfc30e21a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '₺ Turkish Lira Sign' = UNICHAR(8378)
				isHidden
				displayFolder: Currency
				lineageTag: 247e61bb-786f-4d86-857f-51c8fb31a357

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₼ Manat Sign' = UNICHAR(8380)
				isHidden
				displayFolder: Currency
				lineageTag: e820fb3a-133f-479d-8122-b8aa360230d3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '₿ Bitcoin Sign' = UNICHAR(8383)
				isHidden
				displayFolder: Currency
				lineageTag: 7de1d930-247d-490d-8a3a-7eb448175bf1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '₾ Lari Sign' = UNICHAR(8382)
				isHidden
				displayFolder: Currency
				lineageTag: 115099c2-e1d7-474d-b8de-dbbbaeeed496

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '₠ Euro-Currency Sign' = UNICHAR(8352)
				isHidden
				displayFolder: Currency
				lineageTag: 3b74fe48-5938-4116-937a-08ed328257a9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₡ Colon Sign' = UNICHAR(8353)
				isHidden
				displayFolder: Currency
				lineageTag: 4e3beb52-8d2b-40a9-a7ff-0a6f84b1ce49

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₢ Cruzeiro Sign' = UNICHAR(8354)
				isHidden
				displayFolder: Currency
				lineageTag: cfe9bdc0-5546-4bdd-a0b3-ca6381f3a244

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₣ French Franc Sign' = UNICHAR(8355)
				isHidden
				displayFolder: Currency
				lineageTag: 2c6ddc72-05d7-4889-b6ca-0f47add8eba8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₤ Lira Sign' = UNICHAR(8356)
				isHidden
				displayFolder: Currency
				lineageTag: a42a2c0f-16b7-474d-970c-7f2375f4fffb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₥ Mill Sign' = UNICHAR(8357)
				isHidden
				displayFolder: Currency
				lineageTag: 64bded2f-7888-4952-9eaf-1be693c5406e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₦ Naira Sign' = UNICHAR(8358)
				isHidden
				displayFolder: Currency
				lineageTag: 56042f12-3b97-47cc-ad2c-85b5a029806c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₧ Peseta Sign' = UNICHAR(8359)
				isHidden
				displayFolder: Currency
				lineageTag: de5d2eae-0aac-4955-980f-c7f89ddb2d93

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₨ Rupee Sign' = UNICHAR(8360)
				isHidden
				displayFolder: Currency
				lineageTag: b263f9c4-1791-4b9c-af39-4a48ca431a5c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₰ German Penny Sign' = UNICHAR(8368)
				isHidden
				displayFolder: Currency
				lineageTag: d19f2b53-80c6-43b5-80b8-dc1a74b02be2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₱ Peso Sign' = UNICHAR(8369)
				isHidden
				displayFolder: Currency
				lineageTag: 1dbe6c48-2f9d-4420-b72b-cc86006a48be

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₳ Austral Sign' = UNICHAR(8371)
				isHidden
				displayFolder: Currency
				lineageTag: fc09c984-07b6-4294-9c2d-66f457531850

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₶ Livre Tournois Sign' = UNICHAR(8374)
				isHidden
				displayFolder: Currency
				lineageTag: 685821f7-f76f-449d-b8db-724696de2588

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '₷ Spesmilo Sign' = UNICHAR(8375)
				isHidden
				displayFolder: Currency
				lineageTag: 023a3d70-5d1d-443f-b832-24630abc7cdc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '৳ Bengali Rupee Mark' = UNICHAR(2547)
				isHidden
				displayFolder: Currency
				lineageTag: 085bda76-b342-4d26-ac6d-59c418c14058

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '฿ Baht Sign' = UNICHAR(3647)
				isHidden
				displayFolder: Currency
				lineageTag: 213aa5d3-3f0c-473f-b7de-8aa3171e82a7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '៛ Khmer Riel Sign' = UNICHAR(6107)
				isHidden
				displayFolder: Currency
				lineageTag: 774379c0-363e-44c3-8f27-78c72ca54df9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '₻ Nordic Mark Sign' = UNICHAR(8379)
				isHidden
				displayFolder: Currency
				lineageTag: bf090d2d-68ee-4c0d-951f-03ffc394db98

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure 'ƒ Latin Small Letter F With Hook' = UNICHAR(402)
				isHidden
				displayFolder: Currency
				lineageTag: 6138ec6f-b0a4-48ed-a4a9-0899ee3784bb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure '؋ Afghan Afghani Sign' = UNICHAR(1547)
				isHidden
				displayFolder: Currency
				lineageTag: 3f2a8af3-fcd0-407d-bbfc-c836850eb662

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '৲ Bengali Rupee Mark' = UNICHAR(2546)
				isHidden
				displayFolder: Currency
				lineageTag: 5dd203aa-a2b6-4716-87a3-436fd150af75

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '௹ Tamil Rupee Sign' = UNICHAR(3065)
				isHidden
				displayFolder: Currency
				lineageTag: 35756b6d-b88c-4641-be52-c27798dfa93c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'rial Rial Sign' = UNICHAR(65020)
				isHidden
				displayFolder: Currency
				lineageTag: 3c73f61b-cb5f-4393-95be-16b9c0d60b6f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING","FIRST_LETTER_OF_OBJECTS_MUST_BE_CAPITALIZED"]}

			measure '▲ Black Up-Pointing Triangle' = UNICHAR(9650)
				isHidden
				displayFolder: Shapes
				lineageTag: 8d8d66d5-0662-4282-ba3c-98dace24e00c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▼ Black Down-Pointing Triangle' = UNICHAR(9660)
				isHidden
				displayFolder: Shapes
				lineageTag: f066e9bd-a2cf-41eb-a9f8-fc76e736b03a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▶ Black Right-Pointing Triangle' = UNICHAR(9654)
				isHidden
				displayFolder: Shapes
				lineageTag: 92aca495-4efa-4de5-95a6-c79ece424ce3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◀ Black Left-Pointing Triangle' = UNICHAR(9664)
				isHidden
				displayFolder: Shapes
				lineageTag: f49bddb1-f79e-4939-bd5b-b1a9497feaec

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '△ White Up-Pointing Triangle' = UNICHAR(9651)
				isHidden
				displayFolder: Shapes
				lineageTag: 679a17ff-9283-4448-b791-1e01a32caaee

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▽ White Down-Pointing Triangle' = UNICHAR(9661)
				isHidden
				displayFolder: Shapes
				lineageTag: 8e30489e-33cb-4771-a539-9c9c8c039b42

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▷ White Right-Pointing Triangle' = UNICHAR(9655)
				isHidden
				displayFolder: Shapes
				lineageTag: 780be9ac-0ae1-498d-9d18-33399c88a3bd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◁ White Left-Pointing Triangle' = UNICHAR(9665)
				isHidden
				displayFolder: Shapes
				lineageTag: b34d8745-2dbd-473c-981e-d8a17f4ce22a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '■ Black Square' = UNICHAR(9632)
				isHidden
				displayFolder: Shapes
				lineageTag: 00c9dcb8-d5ec-4394-9440-52f4ce8b7400

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '□ White Square' = UNICHAR(9633)
				isHidden
				displayFolder: Shapes
				lineageTag: 18492286-ad33-4a9e-9241-e6aedff2efa3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▪ Black Small Square' = UNICHAR(9642)
				isHidden
				displayFolder: Shapes
				lineageTag: 56b05446-49af-4bad-a49a-d4f25a24c48c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▫ White Small Square' = UNICHAR(9643)
				isHidden
				displayFolder: Shapes
				lineageTag: 971472ad-3c0f-41d4-b184-9f43739db38c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▬ Black Rectangle' = UNICHAR(9644)
				isHidden
				displayFolder: Shapes
				lineageTag: 83bb3f86-cd15-4f35-86bb-1c2c3bf71205

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▭ White Rectangle' = UNICHAR(9645)
				isHidden
				displayFolder: Shapes
				lineageTag: bde3fd81-58af-466b-8660-227be49cf5fd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▮ Black Vertical Rectangle' = UNICHAR(9646)
				isHidden
				displayFolder: Shapes
				lineageTag: b323c985-580e-40f2-a6b0-2b5d80ee20f6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▯ White Vertical Rectangle' = UNICHAR(9647)
				isHidden
				displayFolder: Shapes
				lineageTag: 19c40ed1-0f6e-425c-acef-da8a32aa1d0c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '● Black Circle' = UNICHAR(9679)
				isHidden
				displayFolder: Shapes
				lineageTag: 3b959be7-986c-4dbe-a614-057c92fdb01d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '○ White Circle' = UNICHAR(9675)
				isHidden
				displayFolder: Shapes
				lineageTag: 8bbe43f4-e743-4770-8fc4-c6dbd264abbb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '★ Black Star' = UNICHAR(9733)
				isHidden
				displayFolder: Shapes
				lineageTag: 29a861c6-c0ef-4fbf-9030-5d66db8052d3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☆ White Star' = UNICHAR(9734)
				isHidden
				displayFolder: Shapes
				lineageTag: 24a9672c-2a75-4194-8c4c-1edd4948a879

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♦ Black Diamond Suit' = UNICHAR(9830)
				isHidden
				displayFolder: Shapes
				lineageTag: fafe1eca-2b06-48c4-8423-2d6e35876562

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♢ White Diamond Suit' = UNICHAR(9826)
				isHidden
				displayFolder: Shapes
				lineageTag: d65c08b0-e21a-44ed-9896-35282f81567a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♠ Black Spade Suit' = UNICHAR(9824)
				isHidden
				displayFolder: Shapes
				lineageTag: 5653b981-52aa-4d98-8c1b-8379f31bf74d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♤ White Spade Suit' = UNICHAR(9828)
				isHidden
				displayFolder: Shapes
				lineageTag: a930691b-55ea-4855-a24d-b9522b6ef6b4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♥ Black Heart Suit' = UNICHAR(9829)
				isHidden
				displayFolder: Shapes
				lineageTag: 13715f4e-e1c7-41c2-9fdb-37830a87d592

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♡ White Heart Suit' = UNICHAR(9825)
				isHidden
				displayFolder: Shapes
				lineageTag: 1f56d0ca-5520-4abb-b581-08a31d54c3bd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♣ Black Club Suit' = UNICHAR(9827)
				isHidden
				displayFolder: Shapes
				lineageTag: 69154e8e-ea28-446b-9748-02ac8506d7d2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♧ White Club Suit' = UNICHAR(9831)
				isHidden
				displayFolder: Shapes
				lineageTag: c7a0fc68-17c4-4d6e-9296-fa2d5040de23

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◊ Lozenge' = UNICHAR(9674)
				isHidden
				displayFolder: Shapes
				lineageTag: 1c104944-590d-431d-afe9-071606d61a1c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◆ Black Diamond' = UNICHAR(9670)
				isHidden
				displayFolder: Shapes
				lineageTag: d84dba94-262d-45ea-a95c-57df47bcd142

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◇ White Diamond' = UNICHAR(9671)
				isHidden
				displayFolder: Shapes
				lineageTag: 66706649-2b63-4f66-9021-e65ab564e754

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◼ Black Medium Square' = UNICHAR(9724)
				isHidden
				displayFolder: Shapes
				lineageTag: e5153f8d-4f22-4d0c-91cd-80de57700186

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◻ White Medium Square' = UNICHAR(9723)
				isHidden
				displayFolder: Shapes
				lineageTag: c3726b0c-eaba-4c9b-8350-208e8b7ae557

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬛ Black Large Square' = UNICHAR(11035)
				isHidden
				displayFolder: Shapes
				lineageTag: f256e852-4a44-4a3e-86dc-de1ee08e044d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬜ White Large Square' = UNICHAR(11036)
				isHidden
				displayFolder: Shapes
				lineageTag: a2c3c48e-4bbc-47dc-9d17-5c4e76d00470

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚫ Medium Black Circle' = UNICHAR(9899)
				isHidden
				displayFolder: Shapes
				lineageTag: 7cee6b13-d4a1-4a7a-88a3-e2891257508e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚪ Medium White Circle' = UNICHAR(9898)
				isHidden
				displayFolder: Shapes
				lineageTag: 5f8a0160-1c75-4dfd-8540-08cef8518a83

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✅ White Heavy Check Mark' = UNICHAR(9989)
				isHidden
				displayFolder: Shapes
				lineageTag: 02f62a9a-65d8-4994-84db-3943a021fa7b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➕ Heavy Plus Sign' = UNICHAR(10133)
				isHidden
				displayFolder: Shapes
				lineageTag: 6ee35c4b-0079-405b-b40e-2ca9cf276954

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '➖ Heavy Minus Sign' = UNICHAR(10134)
				isHidden
				displayFolder: Shapes
				lineageTag: a57afda3-cac7-444f-b78f-4cd1879c3ed8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '➗ Heavy Division Sign' = UNICHAR(10135)
				isHidden
				displayFolder: Shapes
				lineageTag: ed70d5a4-3b2e-452b-88ac-0799fd88ceed

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '❌ Cross Mark' = UNICHAR(10060)
				isHidden
				displayFolder: Shapes
				lineageTag: 2d74d49e-08bf-4b5b-aa2c-aab8e7115e46

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭕ Heavy Large Circle' = UNICHAR(11093)
				isHidden
				displayFolder: Shapes
				lineageTag: 1222ab19-12e2-4450-9191-52022ba95b21

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❏ Lower Right Drop-Shadowed White Square' = UNICHAR(10063)
				isHidden
				displayFolder: Shapes
				lineageTag: a6a8d0b5-f6e2-47b5-8d90-2f4f73e4c295

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❐ Upper Right Drop-Shadowed White Square' = UNICHAR(10064)
				isHidden
				displayFolder: Shapes
				lineageTag: 191b9683-9011-4145-ac5d-2ad188f0dc3f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❑ Lower Right Shadowed White Square' = UNICHAR(10065)
				isHidden
				displayFolder: Shapes
				lineageTag: dc765dae-3507-431c-9752-053700270ca1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❒ Upper Right Shadowed White Square' = UNICHAR(10066)
				isHidden
				displayFolder: Shapes
				lineageTag: e2107981-d224-4d1e-8021-1fd178a2d93d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❘ Light Vertical Bar' = UNICHAR(10072)
				isHidden
				displayFolder: Shapes
				lineageTag: b31c3fe6-e974-4943-a7b4-c09d5d1811ae

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❙ Medium Vertical Bar' = UNICHAR(10073)
				isHidden
				displayFolder: Shapes
				lineageTag: 661a632c-d047-4f2f-89cb-8907c6ca2f26

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '❚ Heavy Vertical Bar' = UNICHAR(10074)
				isHidden
				displayFolder: Shapes
				lineageTag: 4e2c96c7-b7db-41b0-b4ed-d6a1e8227eb3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◈ White Diamond Containing Black Small Diamond' = UNICHAR(9672)
				isHidden
				displayFolder: Shapes
				lineageTag: 4fe8154e-6c3e-433e-8138-4d822e94d49e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◉ Fisheye' = UNICHAR(9673)
				isHidden
				displayFolder: Shapes
				lineageTag: dd77d5aa-1709-418f-b631-dfc2a3c40f4d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◌ Dotted Circle' = UNICHAR(9676)
				isHidden
				displayFolder: Shapes
				lineageTag: 20ab9d5f-edee-495d-a71b-c2e4c7d70537

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◍ Circle With Vertical Fill' = UNICHAR(9677)
				isHidden
				displayFolder: Shapes
				lineageTag: 6e74c4f1-a849-4018-b33c-c5d6634c4646

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◎ Bullseye' = UNICHAR(9678)
				isHidden
				displayFolder: Shapes
				lineageTag: 12ef5a05-3888-4772-a5b8-3b8b7aafefeb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◐ Circle With Left Half Black' = UNICHAR(9680)
				isHidden
				displayFolder: Shapes
				lineageTag: d0e8b610-ff76-4045-8a57-98b54eb996b8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◑ Circle With Right Half Black' = UNICHAR(9681)
				isHidden
				displayFolder: Shapes
				lineageTag: 10796adc-546a-4cb6-ade6-0ef9c4e27d06

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◒ Circle With Lower Half Black' = UNICHAR(9682)
				isHidden
				displayFolder: Shapes
				lineageTag: cbc02721-7ea7-4108-8724-6edfc937ee00

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◓ Circle With Upper Half Black' = UNICHAR(9683)
				isHidden
				displayFolder: Shapes
				lineageTag: a04befbb-956c-40f3-9a01-a7c2c76f3ae3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◔ Circle With Upper Right Quadrant Black' = UNICHAR(9684)
				isHidden
				displayFolder: Shapes
				lineageTag: 826df875-ff67-4d55-a46a-ccdf9b6e139d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◕ Circle With All But Upper Left Quadrant Black' = UNICHAR(9685)
				isHidden
				displayFolder: Shapes
				lineageTag: 30b4e66d-0119-4e3b-984c-f61c940171ae

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◖ Left Half Black Circle' = UNICHAR(9686)
				isHidden
				displayFolder: Shapes
				lineageTag: 6fe599d5-06b1-4ca6-b418-a9b98928c0c6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◗ Right Half Black Circle' = UNICHAR(9687)
				isHidden
				displayFolder: Shapes
				lineageTag: 6ca4e9a9-569d-4d76-97f4-325ba2ff3c45

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◘ Inverse Bullet' = UNICHAR(9688)
				isHidden
				displayFolder: Shapes
				lineageTag: a209f892-8628-4692-b57e-f1eafa8f9d0b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◙ Inverse White Circle' = UNICHAR(9689)
				isHidden
				displayFolder: Shapes
				lineageTag: 9eea3e63-c7ac-42be-9720-788d9d6569e9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◚ Upper Half Inverse White Circle' = UNICHAR(9690)
				isHidden
				displayFolder: Shapes
				lineageTag: 307bc631-c8de-4320-b421-e30586bceb82

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◛ Lower Half Inverse White Circle' = UNICHAR(9691)
				isHidden
				displayFolder: Shapes
				lineageTag: 6eb046a4-9285-45a7-91db-49750d839268

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◜ Upper Left Quadrant Circular Arc' = UNICHAR(9692)
				isHidden
				displayFolder: Shapes
				lineageTag: acc5c589-d847-42f5-a4ae-caa44ce16570

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◝ Upper Right Quadrant Circular Arc' = UNICHAR(9693)
				isHidden
				displayFolder: Shapes
				lineageTag: e29b2b99-22d6-4acf-8c44-b9242e40f547

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◞ Lower Right Quadrant Circular Arc' = UNICHAR(9694)
				isHidden
				displayFolder: Shapes
				lineageTag: d98b9756-1e02-4379-8b64-8ab0465583a2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◟ Lower Left Quadrant Circular Arc' = UNICHAR(9695)
				isHidden
				displayFolder: Shapes
				lineageTag: ef29ae6c-2bca-4c92-a2ac-6215b0073b26

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◠ Upper Half Circle' = UNICHAR(9696)
				isHidden
				displayFolder: Shapes
				lineageTag: 4ccf4397-7ff3-43bc-b4c6-32c4d6ce66bd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◡ Lower Half Circle' = UNICHAR(9697)
				isHidden
				displayFolder: Shapes
				lineageTag: 8504c029-1092-46fa-8991-35fc02c12e3f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◢ Black Lower Right Triangle' = UNICHAR(9698)
				isHidden
				displayFolder: Shapes
				lineageTag: d88135ec-03ca-44c2-905d-c0879bcbe84c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◣ Black Lower Left Triangle' = UNICHAR(9699)
				isHidden
				displayFolder: Shapes
				lineageTag: 8ccecbe7-16ae-4eaa-a127-6c7f5d596256

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◤ Black Upper Left Triangle' = UNICHAR(9700)
				isHidden
				displayFolder: Shapes
				lineageTag: 4e387df7-ef72-41b2-96ff-3fb4430a4db8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◥ Black Upper Right Triangle' = UNICHAR(9701)
				isHidden
				displayFolder: Shapes
				lineageTag: 5beedc53-af54-44a2-9d1b-6690f51aa23e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◦ White Bullet' = UNICHAR(9702)
				isHidden
				displayFolder: Shapes
				lineageTag: 2ae21f92-85b7-4d7e-948f-cb9ea2fb49c8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◧ Square With Left Half Black' = UNICHAR(9703)
				isHidden
				displayFolder: Shapes
				lineageTag: d95107dc-2cda-4ae0-9465-d3e14a558e44

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◨ Square With Right Half Black' = UNICHAR(9704)
				isHidden
				displayFolder: Shapes
				lineageTag: 09caeb4b-2924-40f6-a1e0-afc40e5b9e6b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◩ Square With Upper Left Diagonal Half Black' = UNICHAR(9705)
				isHidden
				displayFolder: Shapes
				lineageTag: 786f67cc-f7c2-4e5b-91eb-1678ee799f40

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◪ Square With Lower Right Diagonal Half Black' = UNICHAR(9706)
				isHidden
				displayFolder: Shapes
				lineageTag: b515d8f4-a063-4b14-8d81-21274697359f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◫ White Square With Vertical Bisecting Line' = UNICHAR(9707)
				isHidden
				displayFolder: Shapes
				lineageTag: 57033cce-e886-438c-8092-4f00afe5cff3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◬ White Up-Pointing Triangle With Dot' = UNICHAR(9708)
				isHidden
				displayFolder: Shapes
				lineageTag: bc7cb0da-7abf-4796-bfb0-39e18e7bfae9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◭ Up-Pointing Triangle With Left Half Black' = UNICHAR(9709)
				isHidden
				displayFolder: Shapes
				lineageTag: a6a03e5a-ace4-42ec-9443-e0257212a0e9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◮ Up-Pointing Triangle With Right Half Black' = UNICHAR(9710)
				isHidden
				displayFolder: Shapes
				lineageTag: 0de65713-861b-4f32-9d90-e8aba95a82f7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◯ Large Circle' = UNICHAR(9711)
				isHidden
				displayFolder: Shapes
				lineageTag: f1defb7b-e7ad-438c-a484-3e23bdfcc615

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◰ White Square With Upper Left Quadrant' = UNICHAR(9712)
				isHidden
				displayFolder: Shapes
				lineageTag: 12d60a7b-32f1-40f6-b83b-c5a3d18901be

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◱ White Square With Lower Left Quadrant' = UNICHAR(9713)
				isHidden
				displayFolder: Shapes
				lineageTag: 6710a0a9-790b-40b8-ae46-34ecbbfe2b12

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◲ White Square With Lower Right Quadrant' = UNICHAR(9714)
				isHidden
				displayFolder: Shapes
				lineageTag: 928f4f35-4830-4d79-bd11-a2ced2e39742

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◳ White Square With Upper Right Quadrant' = UNICHAR(9715)
				isHidden
				displayFolder: Shapes
				lineageTag: 1227bf2d-6f93-4c96-a619-bf90cc35e58f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◴ White Circle With Upper Left Quadrant' = UNICHAR(9716)
				isHidden
				displayFolder: Shapes
				lineageTag: 28aa8731-86ff-43be-9c3f-5c668def6d96

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◵ White Circle With Lower Left Quadrant' = UNICHAR(9717)
				isHidden
				displayFolder: Shapes
				lineageTag: 7c4daaa9-a6fd-4ec5-bfd5-bc4e6e14ff31

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◶ White Circle With Lower Right Quadrant' = UNICHAR(9718)
				isHidden
				displayFolder: Shapes
				lineageTag: eb4730ef-bf56-4813-9c52-e1942bec3a54

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◷ White Circle With Upper Right Quadrant' = UNICHAR(9719)
				isHidden
				displayFolder: Shapes
				lineageTag: fe9c1a50-29a2-438c-9557-9aa58c6c1ec3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◸ Upper Left Triangle' = UNICHAR(9720)
				isHidden
				displayFolder: Shapes
				lineageTag: 44260b7c-f3ad-48b3-9deb-3a548b1243ef

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◹ Upper Right Triangle' = UNICHAR(9721)
				isHidden
				displayFolder: Shapes
				lineageTag: 24103cbc-93a0-44e2-8e87-63804d4795c3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◺ Lower Left Triangle' = UNICHAR(9722)
				isHidden
				displayFolder: Shapes
				lineageTag: 01201edb-ef6f-4b98-afc5-32470b63bad0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◽ White Medium Small Square' = UNICHAR(9725)
				isHidden
				displayFolder: Shapes
				lineageTag: 9bbd87a6-8f84-4962-898b-2434559284a8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '◾ Black Medium Small Square' = UNICHAR(9726)
				isHidden
				displayFolder: Shapes
				lineageTag: 1dc18ea3-5fa1-4cae-bf28-5c5f050e04c4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚀ Die Face-1' = UNICHAR(9856)
				isHidden
				displayFolder: Shapes
				lineageTag: 9f8b2206-0972-44d9-9159-67eef5593d87

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚁ Die Face-2' = UNICHAR(9857)
				isHidden
				displayFolder: Shapes
				lineageTag: d9d18529-4304-44ff-b5d4-001496a5c0da

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚂ Die Face-3' = UNICHAR(9858)
				isHidden
				displayFolder: Shapes
				lineageTag: 92b91d51-eb69-42f0-af14-2a4f0c00ed0e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚃ Die Face-4' = UNICHAR(9859)
				isHidden
				displayFolder: Shapes
				lineageTag: d04b8709-87d8-497d-b79b-ac85b2488cff

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚄ Die Face-5' = UNICHAR(9860)
				isHidden
				displayFolder: Shapes
				lineageTag: 26d29322-58d5-4c94-b94e-2c3f46cfd219

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚅ Die Face-6' = UNICHAR(9861)
				isHidden
				displayFolder: Shapes
				lineageTag: 3a66425c-4759-47db-a6e7-24690f1772e8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♩ Quarter Note' = UNICHAR(9833)
				isHidden
				displayFolder: Shapes
				lineageTag: 40d08c72-b88c-437a-8978-7dc3ac6ca872

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♪ Eighth Note' = UNICHAR(9834)
				isHidden
				displayFolder: Shapes
				lineageTag: 8d1e3217-988a-401b-816a-5d05d8f15437

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♫ Beamed Eighth Notes' = UNICHAR(9835)
				isHidden
				displayFolder: Shapes
				lineageTag: ec290632-5304-4deb-b40a-d76fad62f8b4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♬ Beamed Sixteenth Notes' = UNICHAR(9836)
				isHidden
				displayFolder: Shapes
				lineageTag: 7a57c708-0c00-4400-9183-86b68a4dd722

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♭ Music Flat Sign' = UNICHAR(9837)
				isHidden
				displayFolder: Shapes
				lineageTag: a80694de-c2ba-4c99-9e22-ef69a0af4282

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♮ Music Natural Sign' = UNICHAR(9838)
				isHidden
				displayFolder: Shapes
				lineageTag: c2650d13-0123-469b-937a-a88216b45cf2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♯ Music Sharp Sign' = UNICHAR(9839)
				isHidden
				displayFolder: Shapes
				lineageTag: d996163e-f987-4057-b006-766ab9a26448

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬠ White Pentagon' = UNICHAR(11040)
				isHidden
				displayFolder: Shapes
				lineageTag: 87cd942c-0306-4413-b181-1ddb1b583d7f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬡ White Hexagon' = UNICHAR(11041)
				isHidden
				displayFolder: Shapes
				lineageTag: 85ea4bb7-b617-4675-9eb5-6f874d38bcaa

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬢ Black Hexagon' = UNICHAR(11042)
				isHidden
				displayFolder: Shapes
				lineageTag: f3124b70-5ee7-415b-8b25-b5e71c18c743

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬣ Horizontal Black Hexagon' = UNICHAR(11043)
				isHidden
				displayFolder: Shapes
				lineageTag: 418ddb2c-8dd2-4fab-842e-9cd82c2efd3f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬤ Black Large Circle' = UNICHAR(11044)
				isHidden
				displayFolder: Shapes
				lineageTag: 2f09c344-65a3-4a19-b644-cf5e8a684fbc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬥ Black Medium Diamond' = UNICHAR(11045)
				isHidden
				displayFolder: Shapes
				lineageTag: 581b9d67-e763-4646-92fc-488eaea7b448

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬦ White Medium Diamond' = UNICHAR(11046)
				isHidden
				displayFolder: Shapes
				lineageTag: 20a83f20-5e35-405f-9f41-7d928202d3cb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬧ Black Medium Lozenge' = UNICHAR(11047)
				isHidden
				displayFolder: Shapes
				lineageTag: b10cc46d-7bea-46e3-a470-db6805cf8ea5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬨ White Medium Lozenge' = UNICHAR(11048)
				isHidden
				displayFolder: Shapes
				lineageTag: ca9ded6b-18c1-4c4d-8995-bb4729cfbb33

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬩ Black Small Diamond' = UNICHAR(11049)
				isHidden
				displayFolder: Shapes
				lineageTag: 8964338e-b57f-4451-a9bc-cb4f66a9dc99

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬪ Black Small Lozenge' = UNICHAR(11050)
				isHidden
				displayFolder: Shapes
				lineageTag: 8eee1afc-9b20-42d0-8ce7-9ec58294cfab

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⬫ White Small Lozenge' = UNICHAR(11051)
				isHidden
				displayFolder: Shapes
				lineageTag: 24e18a3b-5cc7-4fad-b915-9e8b19c632a6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭐ White Medium Star' = UNICHAR(11088)
				isHidden
				displayFolder: Shapes
				lineageTag: 6aa99b07-8e87-4457-87ea-d265d1f1ba5e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭑ Black Small Star' = UNICHAR(11089)
				isHidden
				displayFolder: Shapes
				lineageTag: aa443d66-45c7-4d0f-bc0b-e0c2a28cc32e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⭒ White Small Star' = UNICHAR(11090)
				isHidden
				displayFolder: Shapes
				lineageTag: 40fda1eb-9064-4de5-87b7-7eefc24ecf69

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔶 Large Orange Diamond' = UNICHAR(128310)
				isHidden
				displayFolder: Shapes
				lineageTag: 7dd208b2-50c8-4dda-bce8-a76608714b3e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔷 Large Blue Diamond' = UNICHAR(128311)
				isHidden
				displayFolder: Shapes
				lineageTag: bc79ffaa-dd0b-40b2-b6f5-7e85df1431fc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔸 Small Orange Diamond' = UNICHAR(128312)
				isHidden
				displayFolder: Shapes
				lineageTag: caa47b84-f183-4551-8df8-de32c7046909

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔹 Small Blue Diamond' = UNICHAR(128313)
				isHidden
				displayFolder: Shapes
				lineageTag: 77acf27f-57f7-4323-b159-c4433f3ec71f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔺 Up-Pointing Red Triangle' = UNICHAR(128314)
				isHidden
				displayFolder: Shapes
				lineageTag: 1ab4e582-4bbb-4b20-af98-e1b68801194c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔻 Down-Pointing Red Triangle' = UNICHAR(128315)
				isHidden
				displayFolder: Shapes
				lineageTag: 621b40a2-67c5-4a87-945e-f68777207f94

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟥 Large Red Square' = UNICHAR(128997)
				isHidden
				displayFolder: Shapes
				lineageTag: cf48505f-b401-4b15-82df-94a8054590ce

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟦 Large Blue Square' = UNICHAR(128998)
				isHidden
				displayFolder: Shapes
				lineageTag: debd7848-d916-4c53-a537-2d3b0d88a3a4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟧 Large Orange Square' = UNICHAR(128999)
				isHidden
				displayFolder: Shapes
				lineageTag: fce8cb3d-0549-4a3e-9d3b-3c93a3fc009c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟨 Large Yellow Square' = UNICHAR(129000)
				isHidden
				displayFolder: Shapes
				lineageTag: 4b3a0151-79f5-430f-8301-387f04022c94

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟩 Large Green Square' = UNICHAR(129001)
				isHidden
				displayFolder: Shapes
				lineageTag: c7c1f444-5f64-43b7-a226-febb5fab2ccd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟪 Large Purple Square' = UNICHAR(129002)
				isHidden
				displayFolder: Shapes
				lineageTag: eac591a9-d246-4339-88cd-930f410fd5eb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟫 Large Brown Square' = UNICHAR(129003)
				isHidden
				displayFolder: Shapes
				lineageTag: 478dd0fa-7710-4280-9d77-123b1b960b11

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔴 Large Red Circle' = UNICHAR(128308)
				isHidden
				displayFolder: Shapes
				lineageTag: 6b5ef2f4-2d2f-4ab8-ac9f-5946d8b92f83

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔵 Large Blue Circle' = UNICHAR(128309)
				isHidden
				displayFolder: Shapes
				lineageTag: 35cf379e-8541-4a6f-8063-bbbdc8bf7dd0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟠 Large Orange Circle' = UNICHAR(128992)
				isHidden
				displayFolder: Shapes
				lineageTag: 8215c8de-72c8-4bff-9ca0-7fab47b412b4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟡 Large Yellow Circle' = UNICHAR(128993)
				isHidden
				displayFolder: Shapes
				lineageTag: 2eafc13c-5e46-4025-9db3-8ea551150ab6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟢 Large Green Circle' = UNICHAR(128994)
				isHidden
				displayFolder: Shapes
				lineageTag: 65ec88a1-2c4d-4e46-a3e0-c6f99bdd3422

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟣 Large Purple Circle' = UNICHAR(128995)
				isHidden
				displayFolder: Shapes
				lineageTag: 947807d8-9a6b-41cd-b1b3-6c2bf2c50c91

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🟤 Large Brown Circle' = UNICHAR(128996)
				isHidden
				displayFolder: Shapes
				lineageTag: 9b4ab9a8-6f2d-440b-86a1-cfd88e1d4382

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▀ Upper Half Block' = UNICHAR(9600)
				isHidden
				displayFolder: Shapes
				lineageTag: 99390a90-4067-4ddb-9044-f1d3e33cea0b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▂ Lower One Quarter Block' = UNICHAR(9602)
				isHidden
				displayFolder: Shapes
				lineageTag: 8d91b75f-1624-40aa-9126-88a7367ca0f0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▃ Lower Three Eighths Block' = UNICHAR(9603)
				isHidden
				displayFolder: Shapes
				lineageTag: fbfc21bc-af41-41bc-b82d-493da85a261a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▄ Lower Half Block' = UNICHAR(9604)
				isHidden
				displayFolder: Shapes
				lineageTag: 946bad78-c734-4be1-a50a-f062c81251a5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▅ Lower Five Eighths Block' = UNICHAR(9605)
				isHidden
				displayFolder: Shapes
				lineageTag: 4abfe3cc-7d73-4324-9411-b3c55aa83055

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▆ Lower Three Quarters Block' = UNICHAR(9606)
				isHidden
				displayFolder: Shapes
				lineageTag: 505b85d3-b8d5-4d3d-94d3-857c49a85863

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▇ Lower Seven Eighths Block' = UNICHAR(9607)
				isHidden
				displayFolder: Shapes
				lineageTag: db9fa4d9-49ec-4e55-a462-8f1953853289

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '█ Full Block' = UNICHAR(9608)
				isHidden
				displayFolder: Shapes
				lineageTag: 54ec1f78-07d3-4687-937c-9a96ab97b978

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▉ Left Seven Eighths Block' = UNICHAR(9609)
				isHidden
				displayFolder: Shapes
				lineageTag: d9da8e72-d28c-4554-bdf2-1d8a6a5c6013

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▊ Left Three Quarters Block' = UNICHAR(9610)
				isHidden
				displayFolder: Shapes
				lineageTag: 89ce5f97-fba3-436f-be66-c5fdcca9f57c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▋ Left Five Eighths Block' = UNICHAR(9611)
				isHidden
				displayFolder: Shapes
				lineageTag: aaed63dd-52ac-4d3f-a670-f4f283d9d9b8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▌ Left Half Block' = UNICHAR(9612)
				isHidden
				displayFolder: Shapes
				lineageTag: 8e30f87a-cfb7-4fb2-9229-cef6f030c9d8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▍ Left Three Eighths Block' = UNICHAR(9613)
				isHidden
				displayFolder: Shapes
				lineageTag: 0c9c2eaf-3587-4311-bf46-51f2f5472e9f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▎ Left One Quarter Block' = UNICHAR(9614)
				isHidden
				displayFolder: Shapes
				lineageTag: b1b2df4d-1669-49f7-826c-41937c39a74f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▏ Left One Eighth Block' = UNICHAR(9615)
				isHidden
				displayFolder: Shapes
				lineageTag: 84695a90-34c2-4cfe-855c-e6a8b4b95bc7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▐ Right Half Block' = UNICHAR(9616)
				isHidden
				displayFolder: Shapes
				lineageTag: 885d3d9c-7a8c-48c2-b764-c93c3ac95c4e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '░ Light Shade' = UNICHAR(9617)
				isHidden
				displayFolder: Shapes
				lineageTag: b7b472a8-0663-4c52-a3e8-5d4ecb2c939e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▒ Medium Shade' = UNICHAR(9618)
				isHidden
				displayFolder: Shapes
				lineageTag: 01f8663e-7090-486d-aa39-e2187b457f4c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▓ Dark Shade' = UNICHAR(9619)
				isHidden
				displayFolder: Shapes
				lineageTag: 46418363-c5ac-4309-a2fb-a69374ba0932

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▔ Upper One Eighth Block' = UNICHAR(9620)
				isHidden
				displayFolder: Shapes
				lineageTag: 09f4e235-365d-4f29-85e2-daab8ea9423b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '▕ Right One Eighth Block' = UNICHAR(9621)
				isHidden
				displayFolder: Shapes
				lineageTag: 0e54f737-15d4-4656-a5a7-d60d835f9401

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '• Bullet' = UNICHAR(8226)
				isHidden
				displayFolder: Shapes
				lineageTag: 07de667c-6a12-4411-a8d3-8e4274a26817

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‣ Triangular Bullet' = UNICHAR(8227)
				isHidden
				displayFolder: Shapes
				lineageTag: 9b185da5-4bc7-46a9-ab01-bc93c8d9936d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁃ Hyphen Bullet' = UNICHAR(8259)
				isHidden
				displayFolder: Shapes
				lineageTag: e062a0e3-17b9-4794-ad56-c335f41871c5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♔ White Chess King' = UNICHAR(9812)
				isHidden
				displayFolder: Shapes
				lineageTag: 100ce7c8-e048-4e6e-9f3d-b66662957664

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♕ White Chess Queen' = UNICHAR(9813)
				isHidden
				displayFolder: Shapes
				lineageTag: 7545676d-2e75-4078-8619-8d658bbd3700

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♖ White Chess Rook' = UNICHAR(9814)
				isHidden
				displayFolder: Shapes
				lineageTag: 6a97e70a-84d6-4834-b868-95b0402039f5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♗ White Chess Bishop' = UNICHAR(9815)
				isHidden
				displayFolder: Shapes
				lineageTag: d99c4b31-8a33-4e04-923c-1d4ce7351ec9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♘ White Chess Knight' = UNICHAR(9816)
				isHidden
				displayFolder: Shapes
				lineageTag: d088efa0-cb14-482e-9db1-41d9671af746

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♙ White Chess Pawn' = UNICHAR(9817)
				isHidden
				displayFolder: Shapes
				lineageTag: 8af23d23-b98e-4876-9dd8-dc450b2052fd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♚ Black Chess King' = UNICHAR(9818)
				isHidden
				displayFolder: Shapes
				lineageTag: c87eaabb-29f5-4f7b-926b-bdc9bc6e9420

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♛ Black Chess Queen' = UNICHAR(9819)
				isHidden
				displayFolder: Shapes
				lineageTag: f1475d87-8f4d-4df1-b519-0dc81d437e73

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♜ Black Chess Rook' = UNICHAR(9820)
				isHidden
				displayFolder: Shapes
				lineageTag: 64b3fc31-a3e7-4a85-b265-bdd036ffa679

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♝ Black Chess Bishop' = UNICHAR(9821)
				isHidden
				displayFolder: Shapes
				lineageTag: 016916b9-f92b-4662-af66-f6507cb2031b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♞ Black Chess Knight' = UNICHAR(9822)
				isHidden
				displayFolder: Shapes
				lineageTag: 5ca85e42-8170-4ffb-b232-2d852a54f407

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♟ Black Chess Pawn' = UNICHAR(9823)
				isHidden
				displayFolder: Shapes
				lineageTag: ee62ff13-f23f-43da-99b7-d64a8323c7ba

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '─ Box Drawings Light Horizontal' = UNICHAR(9472)
				isHidden
				displayFolder: Shapes
				lineageTag: 9565f333-145c-4e58-a6c1-5cc2c3a91f56

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '│ Box Drawings Light Vertical' = UNICHAR(9474)
				isHidden
				displayFolder: Shapes
				lineageTag: a3fd2db1-1275-4d3b-bf24-3123161bc0c0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '┌ Box Drawings Light Down and Right' = UNICHAR(9484)
				isHidden
				displayFolder: Shapes
				lineageTag: 8b64e075-a159-4184-9604-d86259b7ffcb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '┐ Box Drawings Light Down and Left' = UNICHAR(9488)
				isHidden
				displayFolder: Shapes
				lineageTag: 869d97bf-6caa-4acf-9fd1-0ad0af6f7cfc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '└ Box Drawings Light Up and Right' = UNICHAR(9492)
				isHidden
				displayFolder: Shapes
				lineageTag: 579af72e-32c3-45ec-870b-6094c37e0527

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '┘ Box Drawings Light Up and Left' = UNICHAR(9496)
				isHidden
				displayFolder: Shapes
				lineageTag: 982218c3-59b0-4689-a022-aeb09a5ba0b4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '┼ Box Drawings Light Vertical and Horizontal' = UNICHAR(9532)
				isHidden
				displayFolder: Shapes
				lineageTag: e8ba66c0-9043-4bea-aa7b-a74cbd2677e7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚙ Gear' = UNICHAR(9881)
				isHidden
				displayFolder: Other
				lineageTag: 9ba5e306-3615-48e2-9707-aa135af02037

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✓ Check Mark' = UNICHAR(10003)
				isHidden
				displayFolder: Other
				lineageTag: 52d6575f-7fa8-43c2-8057-5121715c72de

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✗ Ballot X' = UNICHAR(10007)
				isHidden
				displayFolder: Other
				lineageTag: 8bb0718c-561f-44da-bae6-dcca4bb1adcc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚠ Warning Sign' = UNICHAR(9888)
				isHidden
				displayFolder: Other
				lineageTag: 8abd4c27-83d9-479d-98bc-a416641977b2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ℹ Information Source' = UNICHAR(8505)
				isHidden
				displayFolder: Other
				lineageTag: 466ffc4d-86c4-4c0d-ae7e-100b6763ab2c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔗 Link Symbol' = UNICHAR(128279)
				isHidden
				displayFolder: Other
				lineageTag: 88fa549d-ce33-4663-b9c1-e0092a96b896

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔒 Lock' = UNICHAR(128274)
				isHidden
				displayFolder: Other
				lineageTag: a2c59eb9-6991-4fc2-9c9f-a24f47b968f3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔓 Open Lock' = UNICHAR(128275)
				isHidden
				displayFolder: Other
				lineageTag: 13aeba52-fdd3-416e-9e47-abf9e687ccee

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '💡 Light Bulb' = UNICHAR(128161)
				isHidden
				displayFolder: Other
				lineageTag: 3f5f4d5e-7d53-4037-aa75-299eccc2144d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔧 Wrench' = UNICHAR(128295)
				isHidden
				displayFolder: Other
				lineageTag: 285a353e-d47d-4388-aecb-08b429d2edf1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔩 Nut And Bolt' = UNICHAR(128297)
				isHidden
				displayFolder: Other
				lineageTag: 332a097b-6375-44a8-83ee-31529bf0b662

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔨 Hammer' = UNICHAR(128296)
				isHidden
				displayFolder: Other
				lineageTag: eb149536-ac11-48e4-b442-60baaed9d48e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚡ High Voltage Sign' = UNICHAR(9889)
				isHidden
				displayFolder: Other
				lineageTag: 03f396e8-fe53-451c-b920-128e9ef3808d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⛭ Shouldered Open Centre Hexagram' = UNICHAR(9965)
				isHidden
				displayFolder: Other
				lineageTag: b1da64fb-f82d-4a93-8673-9b5cb4977c3d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌘ Place Of Interest Sign' = UNICHAR(8984)
				isHidden
				displayFolder: Other
				lineageTag: aa63f05f-389e-4cd7-b498-14c57522d2f7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌥ Option Key' = UNICHAR(8997)
				isHidden
				displayFolder: Other
				lineageTag: 1de0de5f-8181-49b0-8e2f-6e61191e14ae

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌃ Up Arrowhead' = UNICHAR(8963)
				isHidden
				displayFolder: Other
				lineageTag: 211b2223-e517-4993-ab64-8deef2952cf2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌫ Erase To The Left' = UNICHAR(9003)
				isHidden
				displayFolder: Other
				lineageTag: d201d49a-f87d-4769-bed7-804d60afe422

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌨ Keyboard' = UNICHAR(9000)
				isHidden
				displayFolder: Other
				lineageTag: cbe07d5c-aae3-4e0b-a31b-6826baa74d66

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🖱 Three Button Mouse' = UNICHAR(128433)
				isHidden
				displayFolder: Other
				lineageTag: d83c466f-456d-4628-b697-f46a3bdde891

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '💾 Floppy Disk' = UNICHAR(128190)
				isHidden
				displayFolder: Other
				lineageTag: 76f22dd9-c3f5-4454-be4c-44c51d2e0ba4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📁 File Folder' = UNICHAR(128193)
				isHidden
				displayFolder: Other
				lineageTag: aa31ab76-a42a-4a9e-8c1d-80971cbc40a9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📂 Open File Folder' = UNICHAR(128194)
				isHidden
				displayFolder: Other
				lineageTag: bbcf74b8-5cd5-4fe9-be0d-639aba5cc5c8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📄 Page Facing Up' = UNICHAR(128196)
				isHidden
				displayFolder: Other
				lineageTag: 6af367a0-c901-4c62-bb02-df2100ea3f96

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📈 Chart With Upwards Trend' = UNICHAR(128200)
				isHidden
				displayFolder: Other
				lineageTag: e60f2828-ccfd-45db-9b0c-18d74b22e9fd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📉 Chart With Downwards Trend' = UNICHAR(128201)
				isHidden
				displayFolder: Other
				lineageTag: 5e9ed733-1a0c-4cea-90ed-c5b05d50fc07

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📊 Bar Chart' = UNICHAR(128202)
				isHidden
				displayFolder: Other
				lineageTag: 5d829361-1f67-4c78-b929-fe08bfe72161

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📡 Satellite Antenna' = UNICHAR(128225)
				isHidden
				displayFolder: Other
				lineageTag: 73de4a00-7fa3-482e-939a-f14f603cddca

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📶 Antenna Bars' = UNICHAR(128246)
				isHidden
				displayFolder: Other
				lineageTag: cfd1c94a-4e8d-4240-ad60-be5228c8ab7d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔬 Microscope' = UNICHAR(128300)
				isHidden
				displayFolder: Other
				lineageTag: 7c61a685-af90-4b6f-9334-c5626750a786

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔭 Telescope' = UNICHAR(128301)
				isHidden
				displayFolder: Other
				lineageTag: 524a7fe7-ee75-4beb-b32c-cda4bcd79a12

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '💻 Laptop Computer' = UNICHAR(128187)
				isHidden
				displayFolder: Other
				lineageTag: 48e73a0d-7b42-47a9-a464-eb0b68681529

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📱 Mobile Phone' = UNICHAR(128241)
				isHidden
				displayFolder: Other
				lineageTag: 64a905c4-df18-409a-bb6b-c07aeb10036c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☎ Black Telephone' = UNICHAR(9742)
				isHidden
				displayFolder: Other
				lineageTag: 6f14d4f0-acad-493a-9ba4-0c8647e9f3d3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📞 Telephone Receiver' = UNICHAR(128222)
				isHidden
				displayFolder: Other
				lineageTag: ba6bde9f-c077-410d-8f23-4846cb6f16bf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔌 Electric Plug' = UNICHAR(128268)
				isHidden
				displayFolder: Other
				lineageTag: da50a028-e815-4ec5-9960-f6306c9a5813

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔋 Battery' = UNICHAR(128267)
				isHidden
				displayFolder: Other
				lineageTag: 401f84d1-b561-4fd1-8eae-b4657fc0bcce

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔍 Left-Pointing Magnifying Glass' = UNICHAR(128269)
				isHidden
				displayFolder: Other
				lineageTag: 7cbb3a83-b6af-4a64-beea-8119405723cc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔎 Right-Pointing Magnifying Glass' = UNICHAR(128270)
				isHidden
				displayFolder: Other
				lineageTag: 3f9aed24-06a8-42ac-967d-d7433228779a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔔 Bell' = UNICHAR(128276)
				isHidden
				displayFolder: Other
				lineageTag: 1ce55516-54a8-4fde-af18-8a320739fc1f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔕 Bell With Slash' = UNICHAR(128277)
				isHidden
				displayFolder: Other
				lineageTag: 5be36c05-1afb-4c2a-b847-911475812960

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔊 Speaker High Volume' = UNICHAR(128266)
				isHidden
				displayFolder: Other
				lineageTag: 3da3983c-1941-4fba-89fe-052d5c4d7725

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🔇 Muted Speaker' = UNICHAR(128263)
				isHidden
				displayFolder: Other
				lineageTag: e52af29e-e793-495d-a002-03db060cfd9b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚛ Atom Symbol' = UNICHAR(9883)
				isHidden
				displayFolder: Other
				lineageTag: c7a5e43c-315d-4b26-8d25-b297121d739e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚖ Scales' = UNICHAR(9878)
				isHidden
				displayFolder: Other
				lineageTag: 3860a0d0-e037-4d9e-9005-e80fb9582ade

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚕ Staff Of Asclepius' = UNICHAR(9877)
				isHidden
				displayFolder: Other
				lineageTag: a5480ef6-c5fa-4602-9d18-98f53f0d6c83

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚗ Alembic' = UNICHAR(9879)
				isHidden
				displayFolder: Other
				lineageTag: 73090a2b-4de8-4c5a-a420-5219c04538cf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☢ Radioactive Sign' = UNICHAR(9762)
				isHidden
				displayFolder: Other
				lineageTag: 64708899-e90d-4f19-b448-887b1fd109c4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☣ Biohazard Sign' = UNICHAR(9763)
				isHidden
				displayFolder: Other
				lineageTag: e3404df9-d231-4247-a2c1-159071b5b74e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☑ Ballot Box With Check' = UNICHAR(9745)
				isHidden
				displayFolder: Other
				lineageTag: fcb1b288-421c-4b43-a064-667387466c0e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☒ Ballot Box With X' = UNICHAR(9746)
				isHidden
				displayFolder: Other
				lineageTag: f8804c55-4ab3-4784-a3bd-b9fd3739031e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☐ Ballot Box' = UNICHAR(9744)
				isHidden
				displayFolder: Other
				lineageTag: 047f6e2f-c851-478b-bc56-2b33d503884a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✈ Airplane' = UNICHAR(9992)
				isHidden
				displayFolder: Other
				lineageTag: 943310ff-db2e-41b4-8e50-2d167684620e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✉ Envelope' = UNICHAR(9993)
				isHidden
				displayFolder: Other
				lineageTag: 33ec6b72-f767-4f59-85bc-92d6cbf2e0ee

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✒ Black Nib' = UNICHAR(10002)
				isHidden
				displayFolder: Other
				lineageTag: c064698b-77ef-4bec-bb8d-c83e1490b2be

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✏ Pencil' = UNICHAR(9999)
				isHidden
				displayFolder: Other
				lineageTag: c712f62b-9d26-4a46-9224-cd784fa1c01c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✐ Upper Right Pencil' = UNICHAR(10000)
				isHidden
				displayFolder: Other
				lineageTag: 55d8eb18-acae-4108-b935-5566f8f4571b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✑ White Nib' = UNICHAR(10001)
				isHidden
				displayFolder: Other
				lineageTag: 285373aa-c1c6-4708-b1a8-171d6aa1bcfc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✔ Heavy Check Mark' = UNICHAR(10004)
				isHidden
				displayFolder: Other
				lineageTag: c09dd9a7-c125-4f1b-86db-82f1168da800

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✕ Multiplication X' = UNICHAR(10005)
				isHidden
				displayFolder: Other
				lineageTag: 6459b919-3ac6-4fd5-8b82-542a3f364c0e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✖ Heavy Multiplication X' = UNICHAR(10006)
				isHidden
				displayFolder: Other
				lineageTag: a4ae1445-6dd7-4570-919d-918ad40e1bb8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✘ Heavy Ballot X' = UNICHAR(10008)
				isHidden
				displayFolder: Other
				lineageTag: 2d332687-2a16-49f2-889c-a4952c73fe91

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✇ Wheelchair Symbol' = UNICHAR(9855)
				isHidden
				displayFolder: Other
				lineageTag: 8f06fcf7-6016-4ef1-83ad-d6b1dde7fc8c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚓ Anchor' = UNICHAR(9875)
				isHidden
				displayFolder: Other
				lineageTag: 5ec3bd6d-38b5-484a-9ba5-a59ba303ee29

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚿ Squared Key' = UNICHAR(9919)
				isHidden
				displayFolder: Other
				lineageTag: 6381e478-6f6e-4eed-a403-957df165126b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '⛑ Rescue Workers Helmet' = UNICHAR(9937)
				isHidden
				displayFolder: Other
				lineageTag: 8975996b-b76c-4a5a-8f01-c0a882065c63

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⛓ Chains' = UNICHAR(9939)
				isHidden
				displayFolder: Other
				lineageTag: c38213ef-98b4-41be-b5fe-5305236c5d25

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⛏ Pick' = UNICHAR(9935)
				isHidden
				displayFolder: Other
				lineageTag: 7a6b5a7a-d75c-4cad-b3f6-2e693ad4e81d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⛽ Fuel Pump' = UNICHAR(9981)
				isHidden
				displayFolder: Other
				lineageTag: 44435829-35bd-48bd-97b1-73023efea9c5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '✂ Black Scissors' = UNICHAR(9986)
				isHidden
				displayFolder: Other
				lineageTag: acc12d4c-37ba-4985-b2db-5938d03f8fc8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '➿ Double Curly Loop' = UNICHAR(10175)
				isHidden
				displayFolder: Other
				lineageTag: 8034a339-998b-4ba6-a373-a375d7b4a824

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '♿ Wheelchair Symbol Emoji' = UNICHAR(9855)
				isHidden
				displayFolder: Other
				lineageTag: d001ea64-e875-43f0-98b0-32f8ca09c609

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["AVOID_DUPLICATE_MEASURES","UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚐ White Flag' = UNICHAR(9872)
				isHidden
				displayFolder: Other
				lineageTag: aa31839b-c107-4b8d-af6e-0a2e98032bbf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚑ Black Flag' = UNICHAR(9873)
				isHidden
				displayFolder: Other
				lineageTag: d8b91acb-0807-48f1-90bb-6ae39f84f1d0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☔ Umbrella With Rain Drops' = UNICHAR(9748)
				isHidden
				displayFolder: Other
				lineageTag: 77cd0749-4593-4d70-833b-c24262b022ed

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☕ Hot Beverage' = UNICHAR(9749)
				isHidden
				displayFolder: Other
				lineageTag: 09bd5b3d-4637-458d-8325-62642f98a9ad

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☺ White Smiling Face' = UNICHAR(9786)
				isHidden
				displayFolder: Other
				lineageTag: 5128307d-4bc5-4a39-b579-79e9ceb6ef6e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⚽ Soccer Ball' = UNICHAR(9917)
				isHidden
				displayFolder: Other
				lineageTag: a2af3985-673a-4fc0-aac3-eda5cfc13fb9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '⚾ Baseball' = UNICHAR(9918)
				isHidden
				displayFolder: Other
				lineageTag: 0d9be1a6-ebfc-4bac-bc1a-d8a7854ba0b5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '⛄ Snowman Without Snow' = UNICHAR(9924)
				isHidden
				displayFolder: Other
				lineageTag: 6aafe684-c4fc-4c43-bcf4-12673c9b92b4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '# Number Sign' = UNICHAR(35)
				isHidden
				displayFolder: Other
				lineageTag: 3246d634-8458-4082-a750-1f0521cd448a

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '@ At Sign' = UNICHAR(64)
				isHidden
				displayFolder: Other
				lineageTag: 0a9b6c58-447c-4caa-a11c-1a189a66f04c

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '& Ampersand' = UNICHAR(38)
				isHidden
				displayFolder: Other
				lineageTag: 1631ce97-4ef2-4a96-b09f-908951538c0b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '… Horizontal Ellipsis' = UNICHAR(8230)
				isHidden
				displayFolder: Other
				lineageTag: 1fc85ef2-670d-45a4-a2be-ccacfbdaa934

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '– En Dash' = UNICHAR(8211)
				isHidden
				displayFolder: Other
				lineageTag: 46d73fac-697a-44a9-b38e-46b425e4a5e4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '— Em Dash' = UNICHAR(8212)
				isHidden
				displayFolder: Other
				lineageTag: d5010b78-cede-4094-a8c4-5bb26180c05f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¡ Inverted Exclamation Mark' = UNICHAR(161)
				isHidden
				displayFolder: Other
				lineageTag: ac72ff6f-cb3c-490f-84dc-c4675634119b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¿ Inverted Question Mark' = UNICHAR(191)
				isHidden
				displayFolder: Other
				lineageTag: fc86782e-129a-40fd-b337-c2a626c91e73

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '§ Section Sign' = UNICHAR(167)
				isHidden
				displayFolder: Other
				lineageTag: 6f6b9d9e-62df-4413-8971-95a0a13b0493

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¶ Pilcrow Sign' = UNICHAR(182)
				isHidden
				displayFolder: Other
				lineageTag: 82d9fe64-8124-4edc-b8ca-d21e7b609e67

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '© Copyright Sign' = UNICHAR(169)
				isHidden
				displayFolder: Other
				lineageTag: e77c1b61-74f8-4cc4-ba7c-6722cb003f48

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '® Registered Sign' = UNICHAR(174)
				isHidden
				displayFolder: Other
				lineageTag: 02642d7f-92df-4467-a07c-b73bd51bb155

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '™ Trade Mark Sign' = UNICHAR(8482)
				isHidden
				displayFolder: Other
				lineageTag: ad073960-28e8-4732-8787-5e42c939eb1d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '℠ Service Mark' = UNICHAR(8480)
				isHidden
				displayFolder: Other
				lineageTag: 95fa573b-fa4b-4f51-8ea2-c0ad5c5fd692

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '† Dagger' = UNICHAR(8224)
				isHidden
				displayFolder: Other
				lineageTag: 73eb5e82-077a-4b97-9f22-163dead6fd22

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‡ Double Dagger' = UNICHAR(8225)
				isHidden
				displayFolder: Other
				lineageTag: f940b362-202f-4fa8-8ad9-d06765014b5e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‴ Triple Prime' = UNICHAR(8244)
				isHidden
				displayFolder: Other
				lineageTag: 3d5fc8b9-caf5-4ed6-bc9d-2f9486dec8b6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '※ Reference Mark' = UNICHAR(8251)
				isHidden
				displayFolder: Other
				lineageTag: 93f77784-a95e-43a3-9b20-7cf9ac139a03

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⁂ Asterism' = UNICHAR(8258)
				isHidden
				displayFolder: Other
				lineageTag: 594581ef-fad7-4d69-8851-7b07771e974e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‽ Interrobang' = UNICHAR(8253)
				isHidden
				displayFolder: Other
				lineageTag: 7ae42c78-31f2-4efe-a07c-f378fd664f5e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‾ Overline' = UNICHAR(8254)
				isHidden
				displayFolder: Other
				lineageTag: ae940181-32e3-4c96-aa9f-afaa3d50bcdd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¦ Broken Bar' = UNICHAR(166)
				isHidden
				displayFolder: Other
				lineageTag: 6e6bf5b7-4f19-425a-a76c-9a9a5f180b02

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '· Middle Dot' = UNICHAR(183)
				isHidden
				displayFolder: Other
				lineageTag: 5b1e644c-7bd2-4607-a251-964a1c3a67c3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '­ Soft Hyphen' = UNICHAR(173)
				isHidden
				displayFolder: Other
				lineageTag: 6dd098cb-8fb8-428d-8999-b79133e798fe

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","TRIM_OBJECT_NAMES","OBJECTS_SHOULD_NOT_START_OR_END_WITH_A_SPACE","INTEGER_FORMATTING"]}

			measure '‐ Hyphen' = UNICHAR(8208)
				isHidden
				displayFolder: Other
				lineageTag: 04664eae-e5c0-4e00-a04d-83f919fd2663

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‑ Non-Breaking Hyphen' = UNICHAR(8209)
				isHidden
				displayFolder: Other
				lineageTag: 9d653884-03a0-4d9c-a179-012200336825

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‒ Figure Dash' = UNICHAR(8210)
				isHidden
				displayFolder: Other
				lineageTag: b11e687d-c27c-4637-8157-9bdcde980f90

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‘ Left Single Quotation Mark' = UNICHAR(8216)
				isHidden
				displayFolder: Other
				lineageTag: 2af54160-3edb-4948-a4e2-fa5f6e831a50

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '’ Right Single Quotation Mark' = UNICHAR(8217)
				isHidden
				displayFolder: Other
				lineageTag: f47d5fea-18d6-4182-af1e-4dc18a84c9a7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‚ Single Low-9 Quotation Mark' = UNICHAR(8218)
				isHidden
				displayFolder: Other
				lineageTag: e212777e-6708-4354-aebf-29af2b953e7e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '“ Left Double Quotation Mark' = UNICHAR(8220)
				isHidden
				displayFolder: Other
				lineageTag: 60876259-612b-4f32-aebf-265d096d8d1b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '” Right Double Quotation Mark' = UNICHAR(8221)
				isHidden
				displayFolder: Other
				lineageTag: a96875ed-9912-4116-b1da-f63bedffacdc

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '„ Double Low-9 Quotation Mark' = UNICHAR(8222)
				isHidden
				displayFolder: Other
				lineageTag: 2b5c3c12-6782-444b-8e68-40dba45fa0a0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '‹ Single Left-Pointing Angle Quotation Mark' = UNICHAR(8249)
				isHidden
				displayFolder: Other
				lineageTag: 636060d6-bd12-4cee-9fcd-0c32912ffe8b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '› Single Right-Pointing Angle Quotation Mark' = UNICHAR(8250)
				isHidden
				displayFolder: Other
				lineageTag: 3c0a4de1-9bdf-4e6c-8a87-fd71c6a25845

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '« Left-Pointing Double Angle Quotation Mark' = UNICHAR(171)
				isHidden
				displayFolder: Other
				lineageTag: dcee50c3-ed14-4b51-af3a-b7a4a229e94f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '» Right-Pointing Double Angle Quotation Mark' = UNICHAR(187)
				isHidden
				displayFolder: Other
				lineageTag: 60d705ce-a1cc-4789-a931-635081949c57

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☜ White Left Pointing Index' = UNICHAR(9756)
				isHidden
				displayFolder: Other
				lineageTag: e3f8f771-c286-49b5-b9ec-52216ce726b9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☝ White Up Pointing Index' = UNICHAR(9757)
				isHidden
				displayFolder: Other
				lineageTag: 398229d7-3033-466b-b755-0808c721c5eb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☞ White Right Pointing Index' = UNICHAR(9758)
				isHidden
				displayFolder: Other
				lineageTag: e77b498b-6165-482b-9c0c-415c19112159

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☟ White Down Pointing Index' = UNICHAR(9759)
				isHidden
				displayFolder: Other
				lineageTag: ad7e0365-d782-405b-b2e4-fd2ab288bc95

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♀ Female Sign' = UNICHAR(9792)
				isHidden
				displayFolder: Other
				lineageTag: 063fd594-7b43-4eb6-92e1-759a8f7a2cc4

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♂ Male Sign' = UNICHAR(9794)
				isHidden
				displayFolder: Other
				lineageTag: 739b313e-1e36-433c-b5f7-cc0b81e6026f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☿ Mercury' = UNICHAR(9791)
				isHidden
				displayFolder: Other
				lineageTag: 7a7422e1-5b57-4a8a-a732-3200e236e3f6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♁ Earth' = UNICHAR(9793)
				isHidden
				displayFolder: Other
				lineageTag: 34ceef0d-2a18-4aaf-bfbc-26f4bb6064aa

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♃ Jupiter' = UNICHAR(9795)
				isHidden
				displayFolder: Other
				lineageTag: 33872f20-b524-47f7-a067-c78bb3fc0121

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♄ Saturn' = UNICHAR(9796)
				isHidden
				displayFolder: Other
				lineageTag: be5aa941-f695-4e70-b9c2-42e0ea45f3c9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♅ Uranus' = UNICHAR(9797)
				isHidden
				displayFolder: Other
				lineageTag: 2666dd8a-4208-486e-8ac0-b24bbb39bd32

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♆ Neptune' = UNICHAR(9798)
				isHidden
				displayFolder: Other
				lineageTag: 14fc2e4b-75e1-4d31-a78b-d58248178bb5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♇ Pluto' = UNICHAR(9799)
				isHidden
				displayFolder: Other
				lineageTag: 5b83a1cb-a7af-4b3e-a1c8-6f1212cd14e6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♈ Aries' = UNICHAR(9800)
				isHidden
				displayFolder: Other
				lineageTag: a11d10f9-dd0e-4ebb-8eed-283d13b090ec

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♉ Taurus' = UNICHAR(9801)
				isHidden
				displayFolder: Other
				lineageTag: 1291342d-266a-42f7-9c69-934ff83adf38

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♊ Gemini' = UNICHAR(9802)
				isHidden
				displayFolder: Other
				lineageTag: b93c1777-bc8b-44a1-8a8f-9b4e191683b9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♋ Cancer' = UNICHAR(9803)
				isHidden
				displayFolder: Other
				lineageTag: 61e932aa-bb22-4286-b00b-e2b5a308ea4b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♌ Leo' = UNICHAR(9804)
				isHidden
				displayFolder: Other
				lineageTag: 71e5b746-209a-48b4-8a6d-cba5b32371cd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♍ Virgo' = UNICHAR(9805)
				isHidden
				displayFolder: Other
				lineageTag: 95318305-af12-41dd-96e7-48d3b659becd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♎ Libra' = UNICHAR(9806)
				isHidden
				displayFolder: Other
				lineageTag: 1805b15b-e43d-41c7-a2ff-5865bb336e8e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♏ Scorpius' = UNICHAR(9807)
				isHidden
				displayFolder: Other
				lineageTag: d6849760-a4f3-4658-80a7-3c5db94ab9b9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♐ Sagittarius' = UNICHAR(9808)
				isHidden
				displayFolder: Other
				lineageTag: 54b92252-37bc-4ea0-9b29-bd99ef0bdcaf

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♑ Capricorn' = UNICHAR(9809)
				isHidden
				displayFolder: Other
				lineageTag: 94393508-404e-4c5e-9315-e4df4827eedd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♒ Aquarius' = UNICHAR(9810)
				isHidden
				displayFolder: Other
				lineageTag: c8447010-2846-475a-a768-df62764632df

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '♓ Pisces' = UNICHAR(9811)
				isHidden
				displayFolder: Other
				lineageTag: 378c78fb-4d7b-43ba-b175-174df51d760b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟨ Left-Pointing Angle Bracket' = UNICHAR(10216)
				isHidden
				displayFolder: Other
				lineageTag: ff4d7342-2cc5-4588-a1a2-f3e224e5a116

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⟩ Right-Pointing Angle Bracket' = UNICHAR(10217)
				isHidden
				displayFolder: Other
				lineageTag: 98a32491-97c5-4da8-97f0-b355b089a6e8

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⦅ Left White Square Bracket' = UNICHAR(10629)
				isHidden
				displayFolder: Other
				lineageTag: 6e857cbe-109a-4c99-a79d-24cf209fd0a1

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⦆ Right White Square Bracket' = UNICHAR(10630)
				isHidden
				displayFolder: Other
				lineageTag: de73ab04-ccb8-4748-bef8-1da96c5d6b1d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '〚 Left White Tortoise Shell Bracket' = UNICHAR(12314)
				isHidden
				displayFolder: Other
				lineageTag: f4d73aad-bfd4-4cb6-b0ce-6a3a0f2bc740

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '〛 Right White Tortoise Shell Bracket' = UNICHAR(12315)
				isHidden
				displayFolder: Other
				lineageTag: 5300e7f9-0c20-4b82-b860-cb500115db85

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '【 Left Black Lenticular Bracket' = UNICHAR(12304)
				isHidden
				displayFolder: Other
				lineageTag: d7033369-40c5-48c7-843b-1788a96ae8f5

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '】 Right Black Lenticular Bracket' = UNICHAR(12305)
				isHidden
				displayFolder: Other
				lineageTag: a0cd4dab-0451-4d8e-8bdd-02b42a1dcb5b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '〔 Left Tortoise Shell Bracket' = UNICHAR(12308)
				isHidden
				displayFolder: Other
				lineageTag: 53bc625f-503d-4161-bb85-49dd10e5d342

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '〕 Right Tortoise Shell Bracket' = UNICHAR(12309)
				isHidden
				displayFolder: Other
				lineageTag: 07885a11-aea9-47b5-a07c-434824b76659

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '〖 Left White Lenticular Bracket' = UNICHAR(12310)
				isHidden
				displayFolder: Other
				lineageTag: 0e90da0c-dd12-4e53-8617-19c1d44f1338

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '〗 Right White Lenticular Bracket' = UNICHAR(12311)
				isHidden
				displayFolder: Other
				lineageTag: f58ac845-402e-4b48-b9fa-b9a8ab38d633

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⦃ Left White Curly Bracket' = UNICHAR(10627)
				isHidden
				displayFolder: Other
				lineageTag: b7fb38c3-1928-45d6-9643-c40be9b0f4f2

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⦄ Right White Curly Bracket' = UNICHAR(10628)
				isHidden
				displayFolder: Other
				lineageTag: c69c9b76-f828-457b-bc65-19a5778b123b

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '` Grave Accent' = UNICHAR(96)
				isHidden
				displayFolder: Other
				lineageTag: 6ad09526-6cdf-4057-92d3-e6b282c7d7c6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '´ Acute Accent' = UNICHAR(180)
				isHidden
				displayFolder: Other
				lineageTag: 9df96482-e322-4a3c-adc9-3a4484adf184

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ˆ Modifier Letter Circumflex Accent' = UNICHAR(710)
				isHidden
				displayFolder: Other
				lineageTag: 1449c9ab-a4a2-4ff9-a775-f67c3e57e4f9

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '˜ Small Tilde' = UNICHAR(732)
				isHidden
				displayFolder: Other
				lineageTag: 4cb413ce-6904-47ff-a965-4c52749ef1bb

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¨ Diaeresis' = UNICHAR(168)
				isHidden
				displayFolder: Other
				lineageTag: 6d4bcbd8-7187-4698-aacd-bf5020c2e79d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¯ Macron' = UNICHAR(175)
				isHidden
				displayFolder: Other
				lineageTag: 98443c1e-7c5a-467b-a9e3-638ef60f0a96

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '˘ Breve' = UNICHAR(728)
				isHidden
				displayFolder: Other
				lineageTag: a777a901-5c2f-4b6e-baec-1a0a6ed3441e

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '˙ Dot Above' = UNICHAR(729)
				isHidden
				displayFolder: Other
				lineageTag: 855fcc5f-1894-4743-9e91-bd0653ea59ba

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '˚ Ring Above' = UNICHAR(730)
				isHidden
				displayFolder: Other
				lineageTag: 3013b434-bc65-4978-83b6-5bc40305d025

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '˛ Ogonek' = UNICHAR(731)
				isHidden
				displayFolder: Other
				lineageTag: 1f8d0459-7742-4985-b518-3768115daa57

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '˝ Double Acute Accent' = UNICHAR(733)
				isHidden
				displayFolder: Other
				lineageTag: 82658941-6d0b-47b8-89c1-45c91bb657fd

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure 'ˇ Caron' = UNICHAR(711)
				isHidden
				displayFolder: Other
				lineageTag: e5e5c320-53f1-4946-aa66-6c3442580fa6

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '¸ Cedilla' = UNICHAR(184)
				isHidden
				displayFolder: Other
				lineageTag: afa72bdf-2a8c-4fa2-9807-f859dfb68775

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '☰ Trigram for Heaven / Menu' = UNICHAR(9776)
				isHidden
				displayFolder: Other
				lineageTag: 59e3f02b-839a-4a9f-8c44-581522810d7f

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '👤 Bust in Silhouette / User' = UNICHAR(128100)
				isHidden
				displayFolder: Other
				lineageTag: 05273a17-862b-4572-bbc3-c612afc79105

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🏠 House Building / Home' = UNICHAR(127968)
				isHidden
				displayFolder: Other
				lineageTag: e56a5587-23a4-4fda-b9f6-fe580e40dce0

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📥 Inbox Tray / Download' = UNICHAR(128229)
				isHidden
				displayFolder: Other
				lineageTag: 2eb0328d-31eb-41f8-b437-6369f52808ff

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '📤 Outbox Tray / Upload/Share' = UNICHAR(128228)
				isHidden
				displayFolder: Other
				lineageTag: 3a6ce8fe-97e2-4abb-bc0d-ae38b775cd68

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⏳ Hourglass / Pending' = UNICHAR(8987)
				isHidden
				displayFolder: Other
				lineageTag: 65e2515e-4a29-4f0d-9c40-3ea16b72e4ef

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⌛ Hourglass Done' = UNICHAR(9203)
				isHidden
				displayFolder: Other
				lineageTag: cad7f19e-b169-4052-85fa-3d649f2c770d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🙂 Slightly Smiling Face' = UNICHAR(128578)
				isHidden
				displayFolder: Other
				lineageTag: db95734c-6e9c-4fbb-9ff2-994cc5dd0c37

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '😐 Neutral Face' = UNICHAR(128528)
				isHidden
				displayFolder: Other
				lineageTag: bff51cbb-f14b-47ee-863d-e865e7cfa467

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '🙁 Slightly Frowning Face' = UNICHAR(128577)
				isHidden
				displayFolder: Other
				lineageTag: 5f4263da-5b67-4830-bbca-1572e95386d7

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '👍 Thumbs Up' = UNICHAR(128077)
				isHidden
				displayFolder: Other
				lineageTag: 2ad8c360-7705-4417-a352-a896548063d3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '👎 Thumbs Down' = UNICHAR(128078)
				isHidden
				displayFolder: Other
				lineageTag: f32f23f6-7355-4dc7-ab8f-926c44a96bb3

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			measure '⠿ Braille Pattern Dots-123456' = UNICHAR(10303)
				isHidden
				displayFolder: Other
				lineageTag: 805aa611-0d61-43e4-a181-4b204cf3635d

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_MEASURES","INTEGER_FORMATTING"]}

			column Value
				isHidden
				formatString: 0
				isAvailableInMdx: false
				lineageTag: 2574b7fe-9d59-4c5a-9f5e-adf42dfac65e
				summarizeBy: none
				isNameInferred
				sourceColumn: [Value]

				annotation SummarizationSetBy = User

			partition UnicodeMeasures = calculated
				mode: import
				source = { BLANK() }

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["REDUCE_USAGE_OF_CALCULATED_TABLES","ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

		/// Table with information about the model. Key/Value representation, with properties like: last refresh, model creator, etc.
		table About
			isHidden
			lineageTag: 0ddcd0aa-5478-4cc2-9bb3-180ce84a5412

			column Key
				dataType: string
				isHidden
				isAvailableInMdx: false
				lineageTag: 235f06fd-c346-4d7a-9a1d-6450e8624128
				summarizeBy: none
				sourceColumn: Key

				changedProperty = IsHidden

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			column Value
				dataType: string
				isHidden
				isAvailableInMdx: false
				lineageTag: 5da3907d-f0a5-47c2-80a5-df0cd05ffbc5
				summarizeBy: none
				sourceColumn: Value

				changedProperty = IsHidden

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			column Order
				dataType: int64
				isHidden
				formatString: 0
				isAvailableInMdx: false
				lineageTag: cbec06f6-21d0-4b3f-9a0a-f16cef11bf3e
				summarizeBy: none
				sourceColumn: Order

				changedProperty = IsHidden

				annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["UNNECESSARY_COLUMNS"]}

			partition About = m
				mode: import
				source =
						let
							Source = #table({ "Key", "Value" },{
								{ "Developed by", "Microsoft" },
								{ "Version", "1.0" },
								{ "Description", "Sales.pbip" },
								{ "Last Refresh", DateTime.ToText(DateTime.LocalNow(), "yyyy-MM-dd HH:mm:ss") }
							}),
							#"Added Index" = Table.AddIndexColumn(Source, "Order", 1, 1),
							#"Changed Type" = Table.TransformColumnTypes(#"Added Index",{{"Key", type text},  {"Value", type text},{"Order", Int64.Type}}),
							#"Reordered Columns" = Table.ReorderColumns(#"Changed Type",{"Key", "Value", "Order"})
						in
							#"Reordered Columns"

			changedProperty = IsHidden

			annotation PBI_ResultType = Table

			annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":["ENSURE_TABLES_HAVE_RELATIONSHIPS"]}

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
							description: Type of the documentation. Is it a measure, column, table or relationship
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
						column 'DAX Expression'
							caption: DAX Expression
							displayFolder: 01. Columns
					table 'Global Measures'
						caption: Global Measures
						description: This table contains all global measures. These are measures created by the main semantic model developer.
					table 'Local Measures'
						caption: Local Measures
						description: This table contains all measures created by local report developers. This is meant as your personal collection of measures.
					table About
						caption: About
						description: Table with information about the model. Key/Value representation, with properties like: last refresh, model creator, etc.

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
							description: Type van de documentatie, is dit een meting, kolom, tabel of relatie
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
						column 'DAX Expression'
							caption: DAX Expressie
							displayFolder: 01. Kolommen
					table 'Global Measures'
						caption: Globale Metingen
						description: Deze tabel bevat alle globale metingen. Deze metingen worden onderhouden door de ontwikkelaar van het semantische model
					table 'Local Measures'
						caption: Lokale measures
						description: Deze tabel bevat alle metingen gemaakt door lokale report ontwikkelaars. Dit is bedoeld als mapje waar lokale report ontwikkelaars hun measures in kunnen stoppen
					table About
						caption: Info
						description: Tabel met informatie over dit model. Sleutel/Waarde weergave, met elementen zoals laatste refresh, model maker, etc.

		/// This parameter is used to easily modify which database you are connecting to from SQL Server
		expression Database = "database1" meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]
			lineageTag: a72fba5e-2e6d-4521-a435-5259353bb6a1

			annotation PBI_NavigationStepName = Navigation

			annotation PBI_ResultType = Text

		/// This parameter holds the SQL Server to which you are trying to connect to. This is usually used to easily switch between development or production.
		expression 'SQL Server' = "Dev Server" meta [IsParameterQuery=true, List={"Dev Server", "Test Server", "Prod Server"}, DefaultValue="Dev Server", Type="Text", IsParameterQueryRequired=true]
			lineageTag: 6afd1377-30e3-4374-ac35-475d7a54bc1f

			annotation PBI_NavigationStepName = Navigation

			annotation PBI_ResultType = Text

		annotation __PBI_TimeIntelligenceEnabled = 0

		annotation PBIDesktopVersion = 2.146.705.0 (25.08)+b71502aff0a01340c88f384884ef42771068f115

		annotation PBI_QueryOrder = ["Last refresh","Global Measures","Local Measures","Database","SQL Server","About"]

		annotation __TEdtr = 1

		annotation BestPracticeAnalyzer_IgnoreRules = {"RuleIDs":[]}

		annotation PBI_ProTooling = ["TMDLView_Desktop"]


```