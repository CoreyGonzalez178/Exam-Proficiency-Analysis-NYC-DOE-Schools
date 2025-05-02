# Exam Proficiency Analysis

### Overview
This project analyzes data from the New York City Department of Education (NYC DOE) schools and districts. It provides insights into the academic performance and demographic composition of students across various schools and districts. Specifically, the project examines:

- The percentage of students in each school and district who achieved proficient scores on their year-end exams.
- The racial demographics of students, highlighting the percentage of students from different racial backgrounds for each school and district.
- The percentage of students with disabilities and English Language Learners (ELLs).

By visualizing and interpreting this data, the project aims to offer a comprehensive understanding of the educational landscape in NYC, helping stakeholders make informed decisions to improve educational outcomes.

### Table of Contents

### Installation

### Usage

### Data Sources
New York City Public Schools site: 
https://www.nycenet.edu/

### Tools
- Excel - Data Cleaning ([Download Excel here](https://www.microsoft.com/en-us/microsoft-365/excel))
- MySQL - Data Analysis ([Download MySQL here](https://www.mysql.com))
- PowerBI - Creating Reports ([Download PowerBI here](https://www.microsoft.com/en-us/power-platform/products/power-bi/downloads)

### Data Cleaning/Preparation
In the initial data preparation phase, I performed the following tasks:
1. Exporting data from nyc.net each day for daily attendance across all schools
2. Export of demographic data for all schools and districts
3. Data loading and inspection.
4. Data cleaning and formatting.

### Exploratory Data Analysis
Key questions I was looking to learn about:

- Average attendance across the city?
- Average attendance by day?
- Average attendance per borough?
- Average attendance per district?
- Average English proficiency for the city?
- Average Math proficiency for the city?
- Average English proficiency for each borough?
- Average Math proficiency for each borough?
- Average English proficiency for each district?
- Average Math proficiency for each district?
- Correlation between average attendance and average English and Math proficiency?	
- Correlation between attendance and exam score proficiency per school? 
- Correlation between each race and school attendance per school? (and per district?)
- Correlation between each race and grade level proficiency per school? (and per district?)

### Data Analysis

```SQL
WITH AvgValues AS (
  SELECT 
    AVG(a.attd) AS avgattd, 
    AVG(g.english_proficiency) AS avgenglishproficiency,
    AVG(g.math_proficiency) AS avgmathproficiency
  FROM 
    attendance3 AS a
  JOIN 
    grade_level_proficiency2 AS g ON a.school = g.school
)

SELECT
  ROUND(AVG(attd), 2) AS attd_mean,
  ROUND(STDDEV(attd), 2) AS attendance_std,
  ROUND(VARIANCE(attd), 2) AS attendance_var,
  ROUND(AVG(english_proficiency), 2) AS english_mean,
  ROUND(STDDEV(english_proficiency), 2) AS english_std,
  ROUND(VARIANCE(english_proficiency), 2) AS english_var,
  ROUND(AVG(math_proficiency), 2) AS math_mean,
  ROUND(STDDEV(math_proficiency), 2) AS math_std,
  ROUND(VARIANCE(math_proficiency), 2) AS math_var,
  ROUND((SUM((a.attd - av.avgattd) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((a.attd - av.avgattd), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS attd_english_correlation,
  ROUND((SUM((a.attd - av.avgattd) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((a.attd - av.avgattd), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmathproficiency), 2))))), 4) AS attd_math_correlation
FROM 
  attendance3 AS a
JOIN 
  grade_level_proficiency2 AS g
	ON a.school = g.school,
	AvgValues AS av;
```
