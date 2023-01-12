# 1.Ask Phase:
Firstly, we need to address who are our key stakeholders? In this case we have following stakeholders:

Urška Sršen: Bellabeat’s co-founder and Chief Creative Officer
Sando Mur: Mathematician and Bellabeat’s co-founder; key member of the Bellabeat executive team
Bellabeat marketing analytics team


# 2.Prepare Phase:
Public data set on Kaggle was used:
-FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius):
-This Kaggle data set contains personal fitness tracker from thirty fitbit users. 
-Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes information about daily activity, steps, and heart rate that can be used to explore users’ habits. Data is publicly available on Kaggle: FitBit Fitness Tracker Data and stored in 18 csv files.
In the Prepare phase, we identify the data being used and its limitations.

-Sample size of 30 is too small for accurate analysis
-Data is collected 7 years ago in 2016. Users’ daily activity, fitness and sleeping habits, diet and food consumption may have changed since then. 
-some information like weight is entered by the user, so it could be innacurate
-some information of calories is missing, which could indicate users did not have the devices on
-Data may not be timely or relevant.

Is Data ROCCC?

A good data source is ROCCC which stands for Reliable, Original, Comprehensive, Current, and Cited.

Reliable — LOW — Not reliable as it only has 30 person sample size

Original — LOW — Third party provider (Amazon Mechanical Turk)

Comprehensive — MED — Parameters match most of Bellabeat products’ parameters

Current — LOW — Data is 7 years old and may not be relevant

Cited — LOW — Data collected from third party, hence unknown 

Dataset is not of high enough quality to make innovative business decisions


# 3.Process Phase:
Data would be cleaned to ensure its error free and complete

Tools used : Google sheets and Bigquery

Data was first opened in google sheets to validate and clean
Columns were trimmed to ensure there were no unwanted spaces
Columns were validated to ensure even data types


Queries were done in Bigquery 

-summary of tables was collected 




## Finding from Daily Activity Data table

*sums*

SELECT id, round(sum(TotalDistance),3)as sum_TotalDistance, sum(calories) as sum_calories, sum(TotalSteps) as sum_totalsteps

FROM `fitbit-capstone-373720.dailyactivity.dailyactivity` 

group by Id

order by Id asc

![2023-01-11 07_18_10-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211816255-ba4e6663-53ea-4b1d-8add-d9607cde0a88.png)




*averages per person*

SELECT id, round(avg(TotalDistance),3)as avg_TotalDistance, round(avg(calories),3) as avg_calories, round(avg(TotalSteps),3) as avg_totalsteps

FROM `fitbit-capstone-373720.dailyactivity.dailyactivity` 

group by Id

order by id asc

![2023-01-11 07_25_23-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211817664-06ec07b3-3811-4f78-b483-ddea60ae7b5e.png)


*totalaverages*

SELECT round(avg(TotalDistance),3)as avg_TotalDistance, round(avg(calories),3) as avg_calories, round(avg(TotalSteps),3) as avg_totalsteps

FROM `fitbit-capstone-373720.dailyactivity.dailyactivity` 

![2023-01-11 07_26_11-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211817828-e4c29957-047a-4034-b6f8-02b514f2b118.png)


## Takeaways
- Average steps were 7638 with an average total distance of 5.49km and Average calories of 2303
- Average sedentary Minutes was 991.2 and average of Very active Minutes is 21.16
- Average steps were below the recommended 10,000 and average distance was below the recommended 8km

## Findings from Weight table

SELECT round(avg(BMI),2) as avg_BMI 

FROM `fitbit-capstone-373720.weightlog.weightlog`

![2023-01-11 07_26_52-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211817983-e9a5b6b8-4a54-49e9-a923-0ed7ad8715fa.png)


## Takeaways

-Most of the fields for recorded Fat information was empty, so it was not used in the analysis since we cannot use similar information for unique information like that.
-Average BMI was 25.19
-According to the CDC, the average adult man has a BMI of 26.6 and the average adult woman has a BMI of 26.5 - These are average for the USA
Healthy BMI ranges are between 18 and 24.9


## Findings from Daily sleep table

Sleepday_merged data set gave an error when importing in Bigquery
There was an issue with the date and time column validation 
The AM zone was not recognized - all fields had the same time 12:00am, so the time field is a constant that could be removed

*Calculated the average sleeptime*

SELECT sum(totalsleeprecords) as sum_Totalsleeprecords, round(sum (totalminutesasleep)/sum(totalsleeprecords),2) as avg_sleeptime

FROM `fitbit-capstone-373720.Sleepday.sleepday` 

![2023-01-11 07_27_20-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211818092-2b1351e1-7036-4a00-92d1-973a92c19139.png)


*Calculated average time in bed*

SELECT round(sum(TotalTimeInBed)/sum(TotalSleepRecords),3) as avg_timeinbed

FROM `fitbit-capstone-373720.Sleepday.sleepday` 

![2023-01-11 07_27_48-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211818197-cee9b60b-441e-4f10-8a81-18d72eca64e8.png)


## Takeaways
- Total sleeprecords added up to 462 and the Average sleep time was 374 minutes
- Average time in bed was 409
- People spent about 35 minutes awake  before sleeping


## Multiple table summaries

*found the total calories vs the total active distance per user*

SELECT dailyintensities.Id, sum(calories) as totalcalories,round(sum(VeryActiveDistance),2) as totalveryactivedistance

FROM `fitbit-capstone-373720.dailyintensities.dailyintensities` as dailyintensities

left join fitbit-capstone-373720.dailycalories.dailycalories as dailycalories

on dailyintensities.id = dailycalories.id

and dailyintensities.activityday = dailycalories.activityday

group by dailyintensities.Id

order by totalveryactivedistance desc

![2023-01-11 07_28_16-BigQuery – Fitbit-capstone – Google Cloud console - Opera](https://user-images.githubusercontent.com/58373408/211818286-92345b7c-3e7f-4bc4-a10a-749619a08509.png)


**Findings**
The trend for calories vs distance is upwards but its not as definitive as I expected


# Recommendations
-People use the app to track steps, calories and sleep. Some information is missing for these so we can plan to set reminders in the app to have people enter information
-The relation between total steps vs calories as well as active minutes vs calories, shows direct proportionality, we can market around this fact
-81.3% of people are not very active. We can use this information to set goals and awards for people who reach higher levels of activity
-The marketing team can use the information to target people that are not very active and give incentives to use the device more. We can suggest different types of activities and a more accurate way of getting user information like weight.


**Results of visualizations are below and will be in the Powerpoint presentation**



# Visualizations

![most active days](https://user-images.githubusercontent.com/58373408/211822765-f21e9308-edd7-4737-bdc0-b8f3ca7afaf9.png)

![Percentage of Activity in Distance](https://user-images.githubusercontent.com/58373408/211822777-744dc7b7-dc73-47af-928a-4aaf0cf1f747.png)

![sedentary minutes vs total steps](https://user-images.githubusercontent.com/58373408/211822850-6cc05e87-f72f-414b-b8b9-ab6f061e7dba.png)

![calories vs steps](https://user-images.githubusercontent.com/58373408/211822904-fdad4a69-4da4-4c5a-adac-e7f8f8acba07.png)

![calories vs sedentary minutes](https://user-images.githubusercontent.com/58373408/211822924-e1e23260-a48f-4a87-b9e0-f78317ca3635.png)

![Calories vs active distance](https://user-images.githubusercontent.com/58373408/211822944-106ea70d-c835-4ea1-9031-743e875ec8a6.png)
