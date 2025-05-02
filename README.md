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

### Data Analysis Code Snippet 1

```SQL
# Calculate Mean, Standard Deviation, Variance, and Correlation between attendance vs english exam proficiency
# and attendance vs math exam proficiency
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
### Results/Findings 1

The analysis results are summarized as follows:


### Data Analysis Code Snippet 2
```SQL
# school demographics vs grades correlation
WITH AvgValues AS (
  SELECT 
    AVG(s.percent_female) AS avgpercentfemale,
    AVG(s.percent_male) AS avgpercentmale,
    AVG(s.percent_asian) AS avgpercentasian,
    AVG(s.percent_black) AS avgpercentblack,
    AVG(s.percent_hispanic) AS avgpercenthispanic,
    AVG(s.percent_multi_racial) AS avgpercentmultiracial,
    AVG(s.percent_native_american) AS avgpercentnativeamerican,
    AVG(s.percent_white) AS avgpercentwhite,
    AVG(s.percent_students_with_disabilities) AS avgpercentswd,
    AVG(s.percent_english_language_learners) AS avgpercentell,
    AVG(g.english_proficiency) AS avgep,
    AVG(g.math_proficiency) AS avgmp
  FROM 
    school_demographics AS s
  JOIN 
    grade_level_proficiency2 AS g ON s.dbn = g.school
)

SELECT 
    ROUND((SUM((s.percent_female - av.avgpercentfemale) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_female - av.avgpercentfemale), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS female_english_proficiency_correlation,
    ROUND((SUM((s.percent_female - av.avgpercentfemale) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_female - av.avgpercentfemale), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS female_math_proficiency_correlation,
    ROUND((SUM((s.percent_male - av.avgpercentmale) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_male - av.avgpercentmale), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS male_english_proficiency_correlation,
    ROUND((SUM((s.percent_male - av.avgpercentmale) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_male - av.avgpercentmale), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS male_math_proficiency_correlation,
    ROUND((SUM((s.percent_asian - av.avgpercentasian) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_asian - av.avgpercentasian), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS asian_english_proficiency_correlation,
    ROUND((SUM((s.percent_asian - av.avgpercentasian) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_asian - av.avgpercentasian), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS asian_math_proficiency_correlation,
    ROUND((SUM((s.percent_black - av.avgpercentblack) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_black - av.avgpercentblack), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS black_english_proficiency_correlation,
    ROUND((SUM((s.percent_black - av.avgpercentblack) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_black - av.avgpercentblack), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS black_math_proficiency_correlation,
    ROUND((SUM((s.percent_hispanic - av.avgpercenthispanic) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_hispanic - av.avgpercenthispanic), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS hispanic_english_proficiency_correlation,
    ROUND((SUM((s.percent_hispanic - av.avgpercenthispanic) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_hispanic - av.avgpercenthispanic), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS hispanic_math_proficiency_correlation,
    ROUND((SUM((s.percent_multi_racial - av.avgpercentmultiracial) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_multi_racial - av.avgpercentmultiracial), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS multiracial_english_proficiency_correlation,
    ROUND((SUM((s.percent_multi_racial - av.avgpercentmultiracial) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_multi_racial - av.avgpercentmultiracial), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS multiracial_math_proficiency_correlation,
    ROUND((SUM((s.percent_native_american - av.avgpercentnativeamerican) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_native_american - av.avgpercentnativeamerican), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS nativeamerican_english_proficiency_correlation,
    ROUND((SUM((s.percent_native_american - av.avgpercentnativeamerican) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_native_american - av.avgpercentnativeamerican), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS nativeamerican_math_proficiency_correlation,
    ROUND((SUM((s.percent_white - av.avgpercentwhite) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_white- av.avgpercentwhite), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS white_english_proficiency_correlation,
    ROUND((SUM((s.percent_white - av.avgpercentwhite) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_white - av.avgpercentwhite), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS white_math_proficiency_correlation,
    ROUND((SUM((s.percent_students_with_disabilities - av.avgpercentswd) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_students_with_disabilities- av.avgpercentswd), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS swd_english_proficiency_correlation,
    ROUND((SUM((s.percent_students_with_disabilities - av.avgpercentswd) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_students_with_disabilities - av.avgpercentswd), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS swd_math_proficiency_correlation,
    ROUND((SUM((s.percent_english_language_learners - av.avgpercentell) * (g.english_proficiency - av.avgep)) / 
    (SQRT(SUM(POW((s.percent_english_language_learners- av.avgpercentell), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgep), 2))))), 4) AS ell_english_proficiency_correlation,
    ROUND((SUM((s.percent_english_language_learners - av.avgpercentell) * (g.math_proficiency - av.avgmp)) / 
    (SQRT(SUM(POW((s.percent_english_language_learners - av.avgpercentell), 2))) * 
    SQRT(SUM(POW((g.math_proficiency - av.avgmp), 2))))), 4) AS ell_math_proficiency_correlation
FROM 
  school_demographics AS s
JOIN 
  grade_level_proficiency2 AS g
	ON s.dbn = g.school,
	AvgValues AS av;
```

### Results/Findings 2
The analysis results are summarized as follows:
