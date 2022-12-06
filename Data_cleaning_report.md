# Data cleaning report
The cleaning process is performed with MS SQL SERVER STUDIO.

## Data format consistency
The consistency of the imported tables (12) is checked in the Schema by verifying whether all Field names (columns) and related Data types match. No inconsistencies were noted between the imported tables.  

## Data merge
Next, the imported tables are merged to work with one overall dataset (and backup).

````
CREATE TABLE dataset.trips AS
(SELECT *
FROM dataset.202111

UNION ALL

SELECT*
FROM dataset.202112

et cetera

);
````

````
SELECT *
INTO trips_backup
FROM
(
	SELECT * FROM trips
	)a
````

## Data cleaning
Made use of a checklist provided in the Google Data Analysis course to identify common problems encountered during data cleaning.


**Sources of errors:** 

As the data is specifically made available for this case study, any encountered data errors will not be communicated back to identify the source and potentially rectify them.

**Null data:** 

The import functionality in MS SQL SERVER shows during import whether fields are imported as 'NULL' or 'NOT NULL', the latter meaning that NULLs are not allowed. The table properties show that only the columns related to station names, station IDs, and ride start and end coordinates (long, lat) have been imported as datatypes allowed to be 'NULL'. This was verified by performing searches in SQL to confirm the previous. See below example hereof for the start_station_id column.

````
SELECT 
	SUM(CASE WHEN start_station_id IS NULL THEN 1 ELSE 0 END) AS null_count, 
	count(start_station_id) AS non_null_count
FROM trips;
--null_count  non_null_count
--878177      4877517
````
See below an example for a 'NOT NULL' column to conform that these indeed to not contain 'NULLs'.
````

````

Did you search for NULLs using conditional formatting and filters?

**Misspelled words:** 

Did you locate all misspellings?

**Mistyped numbers:** 

Did you double-check that your numeric data has been entered correctly?

**Extra spaces and characters:** 

Did you remove any extra spaces or characters using the TRIM function?

**Duplicates:** 

Did you remove duplicates in spreadsheets using the Remove Duplicates function or DISTINCT in SQL?

**Mismatched data types:** 

Did you check that numeric, date, and string data are typecast correctly?

**Messy (inconsistent) strings:** 

Did you make sure that all of your strings are consistent and meaningful?

**Messy (inconsistent) date formats:** 

Did you format the dates consistently throughout your dataset?

**Misleading variable labels (columns):** 

Did you name your columns meaningfully?

**Truncated data:** 

Did you check for truncated or missing data that needs correction?

**Business Logic:** 

Did you check that the data makes sense given your knowledge of the business? 


