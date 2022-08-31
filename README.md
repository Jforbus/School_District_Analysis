# School_District_Analysis

## Project Overview

The School Board of a local School District has requested the following tasks be completed in order to conduct an analysis of School District performance based on relevant school and student data.

1. A high-level snapshot of the district's key metrics, presented in a table format
2. An overview of the key metrics for each school, presented in a table format
3. Tables presenting each of the following metrics:
    - Top 5 and bottom 5 performing schools, based on the overall passing rate
    - The average math score received by students in each grade level at each school
    - The average reading score received by students in each grade level at each school
    - School performance based on the budget per student
    - School performance based on the school size 
    - School performance based on the type of school

At the time of the analysis' completion, it was brought to the attention of the School District that some portion of the data was corrupted. The scores for Thomas High School Ninth Graders would need to be omitted entirely to preserve the validity of the analysis. Once the scores for the affected portion of the student population were redacted, the original analysis was repeated to obtain the necessary results for the School Disrict.

### Resources

- Data Source: schools_complete.csv, students_complete.csv
- Software: Python 3.6.1, Visual Studio Code 1.70.2, Anaconda 4.14.0, Jupyter Notebook 6.4.8, Pandas 1.4.3

## Results

How is the district summary affected?

With all the Thomas High School Ninth Graders' scores replaced in the Dataset as NaN (not a number), the district summary remains unchanged. All the same data is reflected with no major differences. At this level of analysis, the changes resulting from the omission of a single grade at one school's scores is not noticeable. 

![district_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/district_summary.png)



How is the school summary affected?
The school summary also remains unchanged with the ninth graders' scores removed. Once the total number of students used to calculate the percentages is corrected, the scores for Thomas High School are unchanged by removing the ninth graders' scores.

![school_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/school_summary.png)


How does replacing the ninth graders’ math and reading scores affect Thomas High School’s performance relative to the other schools?
Prior to replacing the ninth graders' scores, Thomas High School was the second highest performing school in the District. After the scores were replaced they remained in second place, showing no relative change.

![performance_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/performance-summary.png)

How does replacing the ninth-grade scores affect the following:
    Math and reading scores by ghttps://github.com/Jforbus/School_District_Analysis/blob/main/Resources/scores_grade.pngrade
        In the scores by grade we can cleary see the results of our omission. Where before Thomas High School showed an 83.7 and 83.6 in 
        reading and math, both now show as `nan`.
        ![score_grade](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/scores_grade.png)
        
   Scores by school spending
        We see no change in the output for the school spending summary. 
        ![school_spending](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/spending_summary.png)
        
   Scores by school size
       There is also no change in the summarization based on school size.
       ![size_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/size_summary.png)
       
   Scores by school type
       Lastly, the summary based on school type remained unaffected by the removal of the 9th grade scores for Thomas High School.
       ![type_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/type_summary.png)

## Summary
It can be seen that the errors in the Thomas High School data had no significant affects on the reports. However untrustworthy the 9th graders data, this analysis shows it was in line with scores from the other grades at Thomas High School, creating no noticeable changes at this level of analysis after removal. 
Within the analysis several changes had to be made to account for the need to omit the 9th graders scores.
To begin a statement was written using the `.loc` method to select the scores of the ninth grade students at Thomas High School and set them all to `nan`.
```
student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th"), "reading_score"] = np.nan

student_data_df.loc[(student_data_df["school_name"] == "Thomas High School") & (student_data_df["grade"] == "9th"), "math_score"] = np.nan
```

When the student data is produced we see that all the scores for 9th graders at Thomas High School now show as nan.
![student_data](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/student_data.png)

The change is visible be in the scores by grade as the 9th grade scores for Thomas High School show as nan.
![score_grade_nan](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/score_grade_nan.png)

An additional change to the analysis was the need to correct the total number of students for several of the calculations. With their scores omitted the 9th graders would need their numbers removed from the student counts to correct the averages and percentages in the district summary.
First the total number of ninth graders being removed is counted, and then subtract that from the unfiltered student total.
```
# Step 1. Get the number of students that are in ninth grade at Thomas High School.
# These students have no grades. 
thomas_ninth_count = school_data_complete_df.loc[(school_data_complete_df["school_name"] == "Thomas High School") & (school_data_complete_df["grade"] == "9th")]["Student ID"].count()

# Get the total student count 
student_count = school_data_complete_df["Student ID"].count()


# Step 2. Subtract the number of students that are in ninth grade at 
# Thomas High School from the total student count to get the new total student count.
new_student_count = student_count - thomas_ninth_count
```
This change had to be repeated when the school summaries were created. Totaling Thomas High School students was no longer sufficient to calculate their passing percentages with the scores of the ninth graders removed. After the school summary is created we adjust the scores of Thomas High School. Before the scores are corrected the affects of calculating the percentages without the 9th graders scores and without correcting the total student numbers can be seen in the school summary. This would have resulted in significant error in the anlysis with Thomas High School's scores appearing much lower than before the removal of the 9th graders, or after the correction is made.
![err_school_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/err_school_summary.png)

The adjustment to the student total is accomplished this time by totaling the grades other than 9th at Thomas High School.Then recalculating the scores using this new student total.
```
# Step 5.  Get the number of 10th-12th graders from Thomas High School (THS).
thomas_tenth_twelfth_graders = school_data_complete_df.loc[(school_data_complete_df["school_name"] == "Thomas High School") & (school_data_complete_df["grade"] != "9th")]["Student ID"].count()

# Step 6. Get all the students passing math from THS
thomas_passing_math = school_data_complete_df.loc[(school_data_complete_df["school_name"] == "Thomas High School") & (school_data_complete_df["grade"] != "9th") & (school_data_complete_df["math_score"] >= 70)]

# Step 9. Calculate the percentage of 10th-12th grade students passing math from Thomas High School. 
thomas_passing_math_percentage = thomas_passing_math["Student ID"].count() / thomas_tenth_twelfth_graders * 100

# Step 12. Replace the passing math percent for Thomas High School in the per_school_summary_df.
per_school_summary_df.loc["Thomas High School", "% Passing Math"] = thomas_passing_math_percentage

```
![school_summary](https://github.com/Jforbus/School_District_Analysis/blob/main/Resources/school_summary.png)

With the grades replaced and the totals corrected the results of the change can be seen in each of the summaries that previously included scores for the 9th graders at Thomas High School. The rest of the analysis was largely unaffected by the change. 
