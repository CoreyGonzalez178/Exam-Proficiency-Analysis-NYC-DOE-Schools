# Exam Proficiency Analysis

### Overview
This project analyzes data from the New York City Department of Education (NYC DOE) elementary and middle schools and districts. It provides insights into the academic performance and demographic composition of students across various schools and districts. Specifically, the project examines:

- The percentage of students in each school and district who achieved proficient scores on their year-end exams.
- The racial demographics of students, highlighting the percentage of students from different racial backgrounds for each school and district.
- The percentage of students with disabilities and English Language Learners (ELLs).

By visualizing and interpreting this data, the project aims to offer a comprehensive understanding of the educational landscape in NYC, helping stakeholders make informed decisions to improve educational outcomes.

### Table of Contents
- [Overview](#Overview)
- [Data Sources](#Data-Sources)
- [Tools](#Tools)
- [Data Cleaning/Preparation](#Data-Cleaning/Preparation)
- [Exploratory Data Analysis](#Exploratory-Data-Analysis)
- [Data Analysis Code Snippet 1](#Data-Analysis-Code-Snippet-1)
- [Data Analysis Code Snippet 2](#Data-Analysis-Code-Snippet-2)
- [Data Analysis Code Snippet 3](#Data-Analysis-Code-Snippet-3)
- [Results/Findings](#Results/Findings)
- [Recommendations](#Recommendations)

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

### Data Analysis Code Snippet 3
```SQL
# Demographics vs exam proficiency correlation
WITH AvgValues AS (
  SELECT 
    AVG(d.percent_asian) AS avg_percent_asian,
	AVG(d.percent_black) AS avg_percent_black,
	AVG(d.percent_hispanic) AS avg_percent_hispanic,
	AVG(d.percent_multi_racial) AS avg_percent_multi_racial,
	AVG(d.percent_native_american) AS avg_percent_native_american,
	AVG(d.percent_white) AS avg_percent_white,
	AVG(d.percent_students_with_disabilities) AS avg_percent_swd,
	AVG(d.percent_english_language_learners) AS avg_percent_ell,
    
    AVG(g.english_proficiency) AS avgenglishproficiency,
    AVG(g.math_proficiency) AS avgmathproficiency
  FROM 
    school_demographics AS d
  JOIN 
    grade_level_proficiency2 AS g ON d.dbn = g.school
)

SELECT 
    ROUND((SUM((d.percent_asian - av.avg_percent_asian) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_asian - av.avg_percent_asian), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS asian_english_correlation,
    ROUND((SUM((d.percent_asian - av.avg_percent_asian) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_asian - av.avg_percent_asian), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS asian_math_correlation,
    ROUND((SUM((d.percent_black - av.avg_percent_black) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_black - av.avg_percent_black), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS black_english_correlation,
    ROUND((SUM((d.percent_black - av.avg_percent_black) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_black - av.avg_percent_black), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS black_math_correlation,    
    ROUND((SUM((d.percent_hispanic - av.avg_percent_hispanic) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_hispanic - av.avg_percent_hispanic), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS hispanic_english_correlation,
    ROUND((SUM((d.percent_hispanic - av.avg_percent_hispanic) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_hispanic - av.avg_percent_hispanic), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS hispanic_math_correlation,
    ROUND((SUM((d.percent_multi_racial - av.avg_percent_multi_racial) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_multi_racial - av.avg_percent_multi_racial), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS multi_racial_english_correlation,
    ROUND((SUM((d.percent_multi_racial - av.avg_percent_multi_racial) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_multi_racial - av.avg_percent_multi_racial), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS multi_racial_math_correlation,  
    ROUND((SUM((d.percent_native_american - av.avg_percent_native_american) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_native_american - av.avg_percent_native_american), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS native_american_english_correlation,
    ROUND((SUM((d.percent_native_american - av.avg_percent_native_american) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_native_american - av.avg_percent_native_american), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS native_american_math_correlation,    
    ROUND((SUM((d.percent_white - av.avg_percent_white) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_white - av.avg_percent_white), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS white_english_correlation,
    ROUND((SUM((d.percent_white - av.avg_percent_white) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_white - av.avg_percent_white), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS white_math_correlation,       
    ROUND((SUM((d.percent_students_with_disabilities - av.avg_percent_swd) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_students_with_disabilities - av.avg_percent_swd), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS swd_english_correlation,
    ROUND((SUM((d.percent_students_with_disabilities - av.avg_percent_swd) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_students_with_disabilities - av.avg_percent_swd), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS swd_math_correlation,    
    ROUND((SUM((d.percent_english_language_learners - av.avg_percent_ell) * (g.english_proficiency - av.avgenglishproficiency)) / 
    (SQRT(SUM(POW((d.percent_english_language_learners - av.avg_percent_ell), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS ell_english_correlation,
    ROUND((SUM((d.percent_english_language_learners - av.avg_percent_ell) * (g.math_proficiency - av.avgmathproficiency)) / 
    (SQRT(SUM(POW((d.percent_english_language_learners - av.avg_percent_ell), 2))) * 
    SQRT(SUM(POW((g.english_proficiency - av.avgenglishproficiency), 2))))), 4) AS ell_math_correlation      
FROM 
    school_demographics AS d
JOIN 
  grade_level_proficiency2 AS g
	ON d.dbn = g.school,
	AvgValues AS av;
```

### Results/Findings 

1. There is a 33% positive correlation between attendance and English exam proficiency
2. There is a 36% positive correlation between attendance and Math exam proficiency
3. Schools and districts will have a high probabilty of raising their percentage of English and Math proficient students by implementing new/stronger actions to get their students to miss less days of school
4. English and Math proficiencies are relatively equal per school and district
5. The Bronx is, by far, the lowest performing borough with only 37% of students proficient in English and 36% proficient in Math.
6. Queens is barely the highest performing borough with a 55% proficiency in English and a 57% proficiency in Math. 
7. All boroughs have relatively equal attendance rates
8. New York City has an average daily rate of approximately 89%
9. Correlation between each demographic measure and exam proficiency are as follows:
- Asian:			45% for English, 60% for Math
- Black:			-36% for English, -49% for Math
- Hispanic: 			-47% for English, -51% for Math
- Multi-Racial: 		51% for English, 53% for Math
- Native American: 		-6% for English, -5% for Math
- White: 			61% for English, 68% for Math
- Students with Disabilities:	-44% for English, -54% for Math
- English Language Learners:	-30% for English, -23% for Math
10. The data review indicates there is a wide gap between each demographics that would need to be addressed to ensure all students regardless of location or demographics are receiving the same quality education.

### Recommendations

Based on the analysis, schools in disadvantaged communities of NYC should find alternative methods to increase and maintain attendance. Below are some recommended actions:

- Increased parent involvement with positive incentives for parent's to attend parent/teacher conferences and fun school events
- Increased parent/teacher communication by scheduling routine parent check-in dates
