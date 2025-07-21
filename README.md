# Glassdoor_Analysis
I go over some interesting facts in the Glassdoor Dataset.

I used the [Data Science Hive](https://www.datasciencehive.com/data-analyst-path) website's [Homework 2.1](https://docs.google.com/document/d/1FWrl4_E5qdsv9dsHOAu5jbgGQ6zjMB2JFZN-yoRUIoo/edit?tab=t.0#heading=h.dhat3tnlvtlz) questions involving the [Glassdoor Dataset](https://www.kaggle.com/datasets/rashikrahmanpritom/data-science-job-posting-on-glassdoor?select=Uncleaned_DS_jobs.csv).

For the same reason as with my previous project, I've decided to use Power Query instead of basic Excel functions.<br/>
Excel functions are easier to use but I wanted to practice using Power Query since it can handle a greater dataset.

## Part 1 & 2: Data Cleaning & Data Wrangling

### Handle missing data
missing values (average/mode), duplicates, standardize text<br/>
Power Query -> top left -> remove duplicates (from 660 rows to 648 rows).

### Finding the Mode
The long way:<br/>
Duplicate Query -> remove nulls (-1 in this dataset) = 628 rows -> Group to count frequencies -> sort to find mode -> keep top row only -> Right-click -> Drill Down -> rename from ModeFinder to MostCommonHQ<br/>
<br/>
The short way:<br/>
View tab -> tick "column profile" -> Right-click on biggest bar (New York, NY) -> copy -> Right-click on null bar (-1) -> replace values<br/>
![Replacing with Mode](https://github.com/user-attachments/assets/25608e26-02b7-4f6a-8d79-54334253c43b)

### Finding the Median in an interval
New Excel sheet -> pivot table to exclude categories and manual dragging sorting -> find median through cumulative count.<br/>
![Calculating the Median](https://github.com/user-attachments/assets/5af937e8-b3c7-40cf-84af-6453e8297cd3)

### Finding the Average
The average in the column profile is skewed because -1 is a number that drags it down.<br/>
Filter out the -1 rows, to find the average without those values, and then retrace and replace -1 with this value.<br/>
![Finding the Average](https://github.com/user-attachments/assets/1c99d8f1-469e-4ae9-8524-e3ad46fec66f)

### Onward
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
This gave 3 "Others" that can be viewed by filtering job title group and checking corresponding job title.

### Reflection
I might not agree with these changes in order to keep a representaive dataset. Since no dates or indices has been associated with our current data, there will be a possibility for duplicate values that maybe shouldn't be removed (because one series of events could have happened twice). Substituting missing number values with an average can be fine but not if we're looking to create a normal distribution visualization for those values. I'm less okay with substituting missing string values with the mode since the company with top posts will simply just be even more outlier:ier. In some cases, the N/A or -1 vastly outweighed the other options and a generalisation might then give a false image. Also, I haven't named all tables and charts and titles appropriately. When I'm working on a real project, I would of course do this.

## Part 3: Data Visualization
![Count of Data Jobs](https://github.com/user-attachments/assets/bc633815-d2fa-4c96-83e6-690988220842)

## Part 4: Advanced Analysis

### Conditional formatting
Conditional formatting the cells with min_salary higher than 100k.<br/>
![Conditional formatting](https://github.com/user-attachments/assets/97010687-7db3-4541-a8b1-08f39f03fa68)

### Top Companies
I was interested in what companies offered the highest salary.<br/>
We can just drag Company Name below avg_salary under rows in the pivot table.<br/>
![Top - average](https://github.com/user-attachments/assets/fdb7c480-ea11-436c-9fa3-2d678c7e37f4)

We could do this for the salary estimate range as well with some easy tweaks:<br/>
filter away by label filters -> begins with -> 2<br/>
![Top - range](https://github.com/user-attachments/assets/3ecb8a5d-c011-40f7-8a8f-c821bbb4d53b)

