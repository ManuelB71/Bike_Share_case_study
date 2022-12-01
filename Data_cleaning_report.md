# Data cleaning report
The cleaning process is performed with BigQuery SQL.

## Data format consistency
The consistency of the imported tables is checked in the Schema by verifying whether all Field names (columns) and related Data types match. No inconsistencies were noted between the imported tables.  

## Data merge
Next, the imported tables are merged to work with one overall dataset.

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

## Data cleaning




