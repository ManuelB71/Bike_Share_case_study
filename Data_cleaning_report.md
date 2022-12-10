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

**NULL data:** 

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

*NOTE: the possibility was investigated to correct some of the missing data based on data that is already included in the table. For example, a missing station name can be retrieved from other records on the condition that the data in the coordinates columns (start_lat, start_lng, OR end_lat, end_lng) are a match. This may also be achieved by cross-references between start station and end station names columns as these should in principle have the same latitude and longtitude coordinates. After an initial test run (see example query below), it was noted that the run gave inconsistent results (e.g. a station id number with two or more distinct station names). Investigation showed that the query 'worked' as intended (operationally), the origin of the inconsistent output was found in inconsistent coordinates data as recorded in the dataset. As there are no means available in the supplied dataset to unambiguously establish which combinations of station id, station name and latitude and longtitude coordinates are correct, no further effort will be taken to 'fill in the gaps'. Note that also no conducive public data was found, after a quick review on the [Chicago Open Data website](https://data.cityofchicago.org/) (to which the dataset refers), that could be helpful in this respect to make a deceive call on correct combinations.* 

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
While investigating the NULL data above, it was also noted that not all station_ids refer to a unique station_name. Of the 1300+ start_station_ids, 300+ have non-unique references to a station name. For example, some names have 'amp;' added to the string (coding issue), some miss additions like E(ast) or W(est), for some station_ids the related station_name changes over time (while the streets are close to each other, both stations still appear to exist), some station_ids with added 'temp' to the name, either at first or at the end during time, and some get 'Public Rack -' inserted in front of the name. As it is not clear what version of a station_name should supersede another and considering the number of station_ids the cleaning effort would concern, the current data situation will be taken as a data limitation (as-is). Whenever any analysis will be performed later on that includes any of these fields, this limitation should be kept in mind. 

In addition to these columns, also rideable_type (classic, electric, docked) and member_casual(member, casual) were checked, but no misspellings were encountered.

**Mistyped numbers:** 
Numeric data in this dataset only relates to coordinates. Considering the above described limitation with station_ids and station_names, also no check will be performed on the coordinates correctness as there is even less reference data available to determine correctness; however, with enough time, effort and other sources of data these could be assigned a probability of correctness to determine correctness, and thus assign a level of integrity to work with the data. But for this exercise this data will be considered to be limited in its use.

**Extra spaces and characters:** 
Performed trim on all columns of nvarchar type. See query example:
````
UPDATE trips2
SET rideable_type = TRIM(rideable_type);
````

**Duplicates:** 
Checked for duplicate records, but no duplicates were encountered. 
````
SELECT ride_id, COUNT(ride_id)
FROM trips2
GROUP BY ride_id
HAVING count(ride_id) > 1;
````

**Mismatched data types:** 
The import functionality in MS SQL SERVER shows during import with which data types designation fields (columns) are (suggested to be) imported. Correct typecasting was checked during that proces. Note: string data was first all imported as nvarchar(Max) as suggested for some columns; however, this appeared to affect query processing / speed. The data type was later on changed to nvarchar(128) as this sufficed for the data in the columns after checking max length for each column. See below sample query to amend data type.
````
ALTER TABLE trips2 
ALTER COLUMN start_station_name NVARCHAR (128) NULL;
````

**Messy (inconsistent) strings:** 
Given previous comments under 'misspelled data', no further work was done on consistency.

**Messy (inconsistent) date formats:** 
The date columns were imported under the same format. No incnsistency thus.

**Misleading variable labels (columns):** 
Although variables could be improved to enhance readability for a third-party reader, they suffice for this exercise.

**Truncated data:** 
Given previous comments under 'NULL data' and 'misspelled data', no further work was done with respect to truncated or missing data.

**Business Logic:** 
The data makes sense for a bike sharing business.


## Data enhancement
Added new data variables as suggested in the case study guidance for ride_length and weekday. In addition also added extra columns for day of the week, week number(#), Month, Quarter, and Year to be able to create other data cross sections if needed.

````
--Insert new ride_length column (INT) and calculate ride_length in seconds
ALTER TABLE trips2
ADD ride_length INT NULL;
GO

UPDATE trips2
SET ride_length = DATEDIFF(second, started_at, ended_at);
GO

--Count NULL and non-NULL ride_length
SELECT 
	SUM(CASE WHEN ride_length IS NULL THEN 1 ELSE 0 END) AS null_count, 
	count(ride_length) AS non_null_count
FROM trips2;
--null_count	non_null_count
--0		5755694
GO

--Count ride lengths per category in int and % (<0, 0-1, 1-24h, >24h)
SELECT 
	sum(case when ride_length < 0 then 1 else 0 end) AS 'Less than 0 seconds',
	sum(case when ride_length between 0 and 60 then 1 else 0 end) AS 'Between 0 and 1 minute',
	sum(case when ride_length between 60 and 86400 then 1 else 0 end) AS 'Between 1m and 24h',
	sum(case when ride_length > 86400 then 1 else 0 end) AS 'More than 24h',
	count(ride_length) AS total_rides
FROM trips2;
--Less than 0 seconds	Between 0 and 1 minute	Between 1m and 24h	More than 24h	total_rides
--112			119506			5631877			5364		5755694

SELECT 
	cast(round(sum(case when ride_length < 0 then 1 else 0 end)*100.0 / count(ride_length),2,1) as decimal(10,2)) AS 'Less than 0 seconds',
	cast(round(sum(case when ride_length between 0 and 60 then 1 else 0 end)*100.0 / count(ride_length),2,1) as decimal(10,2)) AS 'Between 0 and 1 minute',
	cast(round(sum(case when ride_length between 60 and 86400 then 1 else 0 end)*100.0 / count(ride_length),2,1) as decimal(10,2)) AS 'Between 1m and 24h',
	cast(round(sum(case when ride_length > 86400 then 1 else 0 end)*100.0 / count(ride_length),2,1) as decimal(10,2)) AS 'More than 24h',
	count(ride_length) AS total_rides
FROM trips2;
--Less than 0 seconds	Between 0 and 1 minute	Between 1m and 24h	More than 24h	total_rides
--0.00			2.07			97.84			0.09		5755694
GO
````
From the analysis on the ride lengths data it is shown that there are records for ride lenghths that do not seem logical. Negative ride lenghths, rides shorter than one minute, and rides longer than 24 hours. As these make up just over 2% of the records, these can be safely excluded from the dataset. As a result 124.982 rows were deleted from the dataset.
````
DELETE
FROM trips2
WHERE ride_length <= 60 OR ride_length >= 86400;
GO
````

Below the queries are shown that create new columns and update the table with data related to days, weeks, months, quarters, year and season.
````
--Add columns and update week#, Quarter, Month, Weekday, Year
ALTER TABLE trips2
ADD week# INT NULL;
GO

UPDATE trips2
SET week# = DATEPART(WK, started_at); 

ALTER TABLE trips2
ADD Quarter INT NULL;

ALTER TABLE trips2
ADD Month INT NULL;

ALTER TABLE trips2
ADD Weekday INT NULL;
GO

UPDATE trips2
SET Quarter = DATEPART(QUARTER, started_at);

UPDATE trips2
SET Month = DATEPART(MONTH, started_at);

UPDATE trips2
SET Weekday = DATEPART(WEEKDAY, started_at);

ALTER TABLE trips2
ADD Day_name nvarchar(50) NULL;
GO

UPDATE trips2
SET Day_name = DATEName(weekday, started_at);

ALTER TABLE trips2
ADD Year INT NULL;
GO

UPDATE trips2
SET Year = DATEPART(year, started_at);

ALTER TABLE trips2
ADD Season nvarchar(50) NULL;

UPDATE trips2
SET Season = 
	CASE
		WHEN mnth = 12 THEN 'Winter'
		WHEN mnth = 1 THEN 'Winter'
		WHEN mnth = 2 THEN 'Winter'
		WHEN mnth = 3 THEN 'Spring'
		WHEN mnth = 4 THEN 'Spring'
		WHEN mnth = 5 THEN 'Spring'
		WHEN mnth = 6 THEN 'Summer'
		WHEN mnth = 7 THEN 'Summer'
		WHEN mnth = 8 THEN 'Summer'
		WHEN mnth = 9 THEN 'Autumn'
		WHEN mnth = 10 THEN 'Autumn'
		WHEN mnth = 11 THEN 'Autumn'
		ELSE NULL
	END
;
GO
````

