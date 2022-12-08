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

The import functionality in MS SQL SERVER shows during import whether fields are imported as 'NULL' or 'NOT NULL', the latter meaning that NULLs are not allowed. The table properties show that only the columns related to station names, station IDs, and ride start and end coordinates (long, lat) have been imported as datatypes allowed to be 'NULL'. This existence of NULLs was verified by performing searches in SQL. See below example hereof for the start_station_id column.

````
SELECT 
	SUM(CASE WHEN start_station_id IS NULL THEN 1 ELSE 0 END) AS null_count, 
	count(start_station_id) AS non_null_count
FROM trips;

Results:
--null_count  non_null_count
--878177      4877517
````
See below the query for 'NOT NULL' columns to confirm that these indeed to not contain 'NULLs'.
````
SELECT
	SUM(CASE WHEN ride_id IS NULL THEN 1 ELSE 0 END) AS ride_id_null, 
	SUM(CASE WHEN rideable_type IS NULL THEN 1 ELSE 0 END) AS rideable_type_null,
	SUM(CASE WHEN started_at IS NULL THEN 1 ELSE 0 END) AS started_at_null,
	SUM(CASE WHEN ended_at IS NULL THEN 1 ELSE 0 END) AS ended_at_null,
	SUM(CASE WHEN member_casual IS NULL THEN 1 ELSE 0 END) AS member_casual_null
FROM trips;
GO

Results:
--ride_id_null	rideable_type_null	started_at_null	ended_at_null	member_casual_null
--0		0			0		0		0
````
The above confirms that NULL data is only located in the columns as specified before. If we omit all the records with NULL data from the dataset, we would also remove records with valuable data like rideable type, ride date and time information, ride length, and member v casual rider. Analysis that only makes use of these fields benefits from having more data points. So, we will leave the records with NULLs in the dataset, while being aware that analysis that involves station names, IDs and coordinates will be based on a smaller subset of records.

*NOTE: the possibility was investigated to correct some of the missing data based on data that is already included in the table. For example, a missing station name can be retrieved from other records on the condition that the data in the coordinates columns (start_lat, start_lng, OR end_lat, end_lng) are a match. This may also be achieved by cross-references between start station and end station names columns as these should in principle have the same latitude and longtitude coordinates. After an initial test run (see example query below), it was noted that the run gave inconsistent results (e.g. a station id number with two or more distinct station names). Investigation showed that the query 'worked' as intended (operationally), the origin of the inconsistent output was found in inconsistent coordinates data as recorded in the dataset. As there are no means available in the supplied dataset to unambiguously establish which combinations of station id, station name and latitude and longtitude coordinates are correct, no further effort will be taken to 'fill in the gaps'. Note that also no conducive public data was found, after a quick review, on the [Chicago Open Data website](https://data.cityofchicago.org/) (to which the dataset refers), that could be helpful in this respect.* 


Note: a start station name should always have the same latitude and longtitude coordinates. Also note that a start station name on the same coordinates as an end station name, is basically the same station name. So we are able to make cross-references between columns. In the code below an example is shown of this repair exercise. The resulting count of NULLs is shown before and after. In the first round the NULL count is brought down from 940.010 to 580.365, which is almost 360k records. In the second round an extra reduction is achieved of circa 50k records. On a total of 5.8M records, that's circa 7%.* 
````
--Example query to fill empty end station names
UPDATE t1
SET t1.end_station_name = t2.end_station_name
FROM trips t1
LEFT JOIN trips t2
	ON (
	t1.end_lat = t2.end_lat
	AND t1.end_lng = t2.end_lng)
WHERE 
	(t1.end_station_name = '' OR t1.end_station_name IS NULL) 
	AND NOT (t2.end_station_name = '' OR t2.end_station_name IS NULL)
;
````

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


