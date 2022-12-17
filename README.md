# Bike_Share_case_study
Google Data Analytics Capstone case study

## INTRODUCTION
Cyclistic, a fictitious bike-share company in Chicago, believes the company’s future success depends on maximizing the number of annual memberships. To design a new marketing strategy to convert casual riders into annual members, the marketing analyst team (which you are part of) needs to understand how the use of Cyclistic bikes differs between casual riders and annual members.

The case study will follow the data analysis process steps: *Ask*, *Prepare*, *Process*, *Analyze*, *Share*, and *Act*  


## ASK
The director of marketing believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, she believes there is a very good chance to convert casual riders into members with a dedicated marketing strategy. To guide the design of the marketing campaign, the marketing analyst team needs to understand 1) how annual members and casual riders differ, 2) why casual riders would buy a membership, and 3) how digital media could affect their marketing tactics. You are tasked to answer the first question.

### *Business Task*: 
Analyze Cyclistic historical bike trip data to identify trends in how annual members and casual riders use Cyclistic bikes differently. Based on key findings, provide the executive team the top three recommendations to convert casual riders into members.


## PREPARE
The datasets are made available through a repository with 'Cyclistic’s' historical trip data. As Cyclistic is a fictional company, appropriate data has been made available by Motivate International Inc. (now: Lyft) under this [license](https://ride.divvybikes.com/data-license-agreement).) for the purposes of this case study. The data is sourced directly from the company that operates the bikesharing service in a large US city, and made public with the consent of that city. Due to data-privacy considerations, no attempt may be made to correlate the data with personally identifiable information (e.g. analysis related to the place of residence or purchase history of riders). Data is available from 2013 until October 2022, either per month or per quarter (repacked into zip files). In view of the above, the data is considered to be credible (reliable, original, comprehensive, current, and cited/vetted).

For the purposes of this analysis, only the 12 most recent months will be used (November 2021 to October 2022). The data is downloaded and stored onto a local drive. The data is organized as (zipped) csv files, in rows and columns, and defined data types. As the smallest file contains 100k+ rows and the largest 800k+ rows, its best to make use of a tool data can handle large data sizes for the purposes of processing and analysis, like SQL or R. 


## PROCESS
Microsoft Excel is used for gaining quick insights into the data (sorting and filtering of single CSV / Excel files) and SQL (BigQuery) is used for proper data processing for large datasets. Edit: due to data size import limitations in BigQuery (free version), moved to MS SQL Server Studio for main processing tasks. 

### Data upload
All datasets (12) are uploaded into MS SQL Server via the import functionality.

### Data cleaning
The clean-up process using SQL is described separately, see [Data_cleaning_report](https://github.com/ManuelB71/Bike_Share_case_study/blob/main/Data_cleaning_report).
This also includes data enhancement with variables as suggested in the case study (ride length, weekday) as well as some others related to datetime that may be beneficial when making more detailed analysis (e.g. week number, day of the week, quarter, year, season). Finally, cleansed data is exported for further analysis.


## ANALYZE
Now that the data has been processed for analysis, the Analyze phase circles back to the business task: "identify trends how casual riders and member riders use bikes differently". The analysis process to identify trends (if any) is described here [Data_analysis_report](https://github.com/ManuelB71/Bike_Share_case_study/blob/main/Data_analysis_report.md).


## SHARE

### Main insights analysis phase:

**Work travel rides** - Casual riders appear to show a bike usage trend around work travel days (Monday to Friday) and hours (6 am-9 am / 4 pm-6 pm) that is comparable to Member riders bike usage. A Casual rider could have a monetary incentive to take on an annual membership if he/she for example travels to work every business day (back and forth), for 5 minutes per ride and 8 weeks in a year. This is also achievable if Casual riders only ride during favorable weather conditions (late Spring and early Fall). 

**Non-work travel rides** - Casual riders that periodically ride for longer than 20 mins (trend in average ride length) may be cheaper off if they use the bikes above a certain periodicity (example 1: classic bike rental > 2.5 rides per month; example 2: electric bike rental > 2 rides per month [based on Divvy pricing plans in Chicago]). 

**Weekends / Holidays rides** - Casual riders have a tendency to rent bikes more often during the weekends and around the Summer holidays. Although an annual membership may be too expensive if riders in this group do not ride above a certain threshold, they may be inclined to take on a less expensive tailored membership.

### Top 3 recommendations to convert casual riders into taking a membership:

1. Create a marketing campaign specifically targetting casual riders that regularly make use of the bike sharing service to travel to / from work (and perhaps also students).

2. Create a marketing campaign specifically targetting casual riders that take several rides per month lasting 20 mins(average ride length) or longer.

3. Introduce specialized memberships to target casual riders that frequently ride during the weekends or holidays (weekend / holiday memberships at a reduced price compared to a full annual membership). Create a marketing campaign specifically targetting casual riders that make use of the bike sharing service in these periods.

*Concentrate individual marketing efforts around those stations and coordinates that show a correlation to the recommendations. To interpret these correctly knowledge of work / leisure movements in the city is a necessity.* 

Note: the analysis can be further enhanced / confirmed by additional clean up of the missing / ambiguous data in the original dataset (station names, IDs, coordinates). 

## ACT
The Cyclistic BikeShare Executive team should decide on a course for action based on the presented analysis and recommendations and devise (a) marketing campaign(s) together with the Marketing team.

### This concludes the capstone case study.
