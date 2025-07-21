# Glassdoor_Analysis
I go over some interesting facts in the Glassdoor Dataset.

I followed the [Data Science Hive](https://www.datasciencehive.com/data-analyst-path) website's [Homework 2.1](https://docs.google.com/document/d/1FWrl4_E5qdsv9dsHOAu5jbgGQ6zjMB2JFZN-yoRUIoo/edit?tab=t.0#heading=h.dhat3tnlvtlz) questions involving the [Glassdoor Dataset](https://www.kaggle.com/datasets/rashikrahmanpritom/data-science-job-posting-on-glassdoor?select=Uncleaned_DS_jobs.csv).

For the same reason as with my previous project, I've decided to use Power Query instead of basic Excel functions.<br/>
Excel functions are easier to use but I wanted to practice using Power Query since it can handle a greater dataset.

## Part 1 & 2: Data Cleaning & Data Wrangling

### Handle missing data
missing values (average/mode), duplicates, standardize text.<br/>
Power Query -> top left -> remove duplicates (from 660 rows to 648 rows).

### Finding the Mode
The long way: Duplicate Query -> remove nulls (-1 in this dataset) = 628 rows -> Group to count frequencies -> sort to find mode -> keep top row only -> Right-click -> Drill Down -> rename from ModeFinder to MostCommonHQ<br/>
The short way: View tab -> tick "column profile" -> Right-click on biggest bar (New York, NY) -> copy -> Right-click on null bar (-1) -> replace values

### Finding the Median in an interval
New Excel sheet -> pivot table to exclude categories and manual dragging sorting -> find median through cumulative count.

### Finding the Average
The average in the column profile is skewed because -1 is a number that drags it down.<br/>
Filter out the -1 rows, to find the average without those values, and then retrace and replace -1 with this value.

Other text-based columns: copy the mode at column profile -> RC -1 -> paste<br/>
Other interval-based columns: replace unknown values with the median interval<br/>
Single number-based columns: remove -1 to unskew and replace with the true average

For the seniority column, I created a custom column to compare the Job Title with the seniority and all titles with senior or lead or sr or the like were accounted for in this column. So I assumed that the na in this column is jr instead of the mode (senior) bacause of the lack of that word's presence.

There is a job_simp but I wanted to cleaning the job titles myself:
```
let
    title = Text.Lower([Job Title])
in
    if Text.Contains(title, "ml") or Text.Contains(title, "learning") then "Machine Learning Engineer"
    else if Text.Contains(title, "engineer") then "Data Engineer"
    else if Text.Contains(title, "scientist") or Text.Contains(title, "science") then "Data Scientist"
    else if Text.Contains(title, "analyst") or Text.Contains(title, "analytic") then "Data Analyst"
    else "Other"
```
which gave 3 "Others" that can be viewed by filtering job title group and checking corresponding job title
