# pandas-challenge
Module 4 challenge - Analyze the district-wide standardized test results for math and reading scores as well as various information on the schools they attend. The task is to aggregate the data to showcase trends in school performance.
# Dependencies and Setup
import pandas as pd
from pathlib import Path
import numpy as np

# File to Load (Remember to Change These)
school_data_to_load = Path("/Users/savirahiman/Desktop/pandas-challenge/pandas-challenge/PyCitySchools/Resources/schools_complete.csv")
student_data_to_load = Path("/Users/savirahiman/Desktop/pandas-challenge/pandas-challenge/PyCitySchools/Resources/students_complete.csv")

# Read School and Student Data File and store into Pandas DataFrames
school_data_df = pd.read_csv(school_data_to_load)
student_data_df = pd.read_csv(student_data_to_load)

# Combine the data into a single dataset.
school_data_complete_df = pd.merge(student_data_df, school_data_df, how="left", on=["school_name", "school_name"])
school_data_complete_df.head()

#District Summary
# Calculate the total number of unique schools
school_count = len(school_data_complete_df["school_name"].unique())
school_count
# Calculate the total number of students
student_count = school_data_complete_df["Student ID"].count()
student_count
# Calculate the total budget
total_budget = school_data_df["budget"].sum()
total_budget
# Calculate the average (mean) math score
average_math_score = school_data_complete_df["math_score"].mean()
average_math_score
# Calculate the average (mean) reading score
average_reading_score = school_data_complete_df["reading_score"].mean()
average_reading_score
# Use the following to calculate the percentage of students who passed math (math scores greather than or equal to 70)
passing_math_count = school_data_complete_df[(school_data_complete_df["math_score"] >= 70)].count()["student_name"]
passing_math_percentage = passing_math_count / float(student_count) * 100
passing_math_percentage
# Calculate the percentage of students who passed reading (hint: look at how the math percentage was calculated)
passing_reading_count = school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)].count()["student_name"]
passing_reading_percentage = passing_reading_count / float(student_count) * 100
passing_reading_percentage
# Use the following to calculate the percentage of students that passed math and reading
passing_math_reading_count = school_data_complete_df[
    (school_data_complete_df["math_score"] >= 70) & (school_data_complete_df["reading_score"] >= 70)
].count()["student_name"]
overall_passing_rate = passing_math_reading_count /  float(student_count) * 100
overall_passing_rate
# Create a high-level snapshot of the district's key metrics in a DataFrame
district_summary_df = pd.DataFrame(
    [{"Total Schools": school_count, 
      "Total Students": student_count, 
      "Total Budget": total_budget, 
      "Average Math Score": average_math_score, 
      "Average Reading Score": average_reading_score,
      "% Passing Math": passing_math_percentage,
      "% Passing Reading": passing_reading_percentage,
      "% Overall Passing": overall_passing_rate}])

# Formatting total students to have the comma seperator
district_summary_df["Total Students"] = district_summary_df["Total Students"].map("{:,}".format)
# Formatting total budget to have currency, comma seperator, and 2 decimal place
district_summary_df["Total Budget"] = district_summary_df["Total Budget"].map("${:,.2f}".format)
# Formatting to show 1 decimal place
district_summary_df["Average Math Score"] = district_summary_df["Average Math Score"].map("{:.1f}".format)
district_summary_df["Average Reading Score"] = district_summary_df["Average Reading Score"].map("{:.1f}".format)
district_summary_df["% Passing Math"] = district_summary_df["% Passing Math"].map("{:.1f}".format)
district_summary_df["% Passing Reading"] = district_summary_df["% Passing Reading"].map("{:.1f}".format)
district_summary_df["% Overall Passing"] = district_summary_df["% Overall Passing"].map("{:.1f}".format)

# Display the DataFrame
district_summary_df

# School Summary
# Use the code provided to select the type per school from school_data
school_types = school_data_df.set_index(["school_name"])["type"]
school_types
school_types_df = pd.DataFrame(school_types)
school_types_df
# Calculate the total student count per school from school_data
per_school_counts = school_data_df.set_index(["school_name"])["size"]
per_school_counts
# Calculate the total school budget from school_data

#per_school_budget = school_data_complete_df.groupby(["school_name"]).agg({"budget":"mean"})
per_school_budget = school_data_df.set_index(["school_name"]) ["budget"]
per_school_budget
# Calculate the per capita spending per school from school_data
per_school_capita = per_school_budget / per_school_counts
per_school_capita
# Calculate the average Math test scores per school from school_data_complete
#per_school_math = school_data_complete_df.groupby(["school_name"]).agg({"math_score":"mean"})
per_school_math = school_data_complete_df.groupby(["school_name"]).mean(numeric_only=True)["math_score"]
per_school_math
# Calculate the average Reading test scores per school from school_data_complete
per_school_reading = school_data_complete_df.groupby(["school_name"]).mean(numeric_only=True)["reading_score"]
per_school_reading
# Calculate the number of students per school with math scores of 70 or higher from school_data_complete
students_passing_math =school_data_complete_df[(school_data_complete_df["math_score"] >= 70)]

school_students_passing_math = students_passing_math.groupby(["school_name"]).count()["student_name"]
school_students_passing_math
# Calculate the number of students per school with reading scores of 70 or higher from school_data_complete
students_passing_reading =school_data_complete_df[(school_data_complete_df["reading_score"] >= 70)]
school_students_passing_reading =students_passing_reading.groupby(["school_name"]).count()["student_name"]
school_students_passing_reading
# Use the provided code to calculate the number of students per school that passed both math and reading with scores of 70 or higher
students_passing_math_and_reading_df = school_data_complete_df[
    (school_data_complete_df["reading_score"] >= 70) & (school_data_complete_df["math_score"] >= 70)
]
school_students_passing_math_and_reading_df = students_passing_math_and_reading_df.groupby(["school_name"]).size()
school_students_passing_math_and_reading_df
# Use the provided code to calculate the passing rates-Math
per_school_passing_math = school_students_passing_math / per_school_counts * 100
per_school_passing_math
# Use the provided code to calculate the passing rates-Reading
per_school_passing_reading = school_students_passing_reading / per_school_counts * 100
per_school_passing_reading
# Use the provided code to calculate the passing rates-Overall
overall_passing_rate = school_students_passing_math_and_reading_df / per_school_counts * 100
overall_passing_rate
# Create a DataFrame called `per_school_summary` with columns for the calculations above.
per_school_summary_df = pd.DataFrame({"School Type": school_types,
    "Total Students": per_school_counts,
    "Total School Budget" : per_school_budget,
    "Per Student Budget" : per_school_capita,
    "Average Math Score" : per_school_math,
    "Average Reading Score" :per_school_reading,
    "% Passing Math" :per_school_passing_math,
    "% Passing Reading" : per_school_passing_reading,
    "% Overall Passing" : overall_passing_rate
                                      })





# Formatting
per_school_summary_df["Total School Budget"] = per_school_summary_df["Total School Budget"].map("${:,.2f}".format)
per_school_summary_df["Per Student Budget"] = per_school_summary_df["Per Student Budget"].map("${:,.2f}".format)
per_school_summary_df["Average Math Score"] = per_school_summary_df["Average Math Score"].map("{:,.2f}".format)
per_school_summary_df["Average Reading Score"] = per_school_summary_df["Average Reading Score"].map("{:,.2f}".format)
per_school_summary_df["% Passing Math"] = per_school_summary_df["% Passing Math"].map("{:,.2f}".format)
per_school_summary_df["% Passing Reading"] = per_school_summary_df["% Passing Reading"].map("{:,.2f}".format)
per_school_summary_df["% Overall Passing"] = per_school_summary_df["% Overall Passing"].map("{:,.2f}".format)
# Display the DataFrame
per_school_summary_df
# Highest-Performing Schools (by % Overall Passing)
# Sort the schools by `% Overall Passing` in descending order and display the top 5 rows.
top_schools_df =per_school_summary_df.sort_values(["% Overall Passing"], ascending=False)
top_schools_df.head(5)
# Bottom Performing Schools (By % Overall Passing)
# Sort the schools by `% Overall Passing` in ascending order and display the top 5 rows.
bottom_schools =per_school_summary_df.sort_values(["% Overall Passing"], ascending=True)
bottom_schools.head(5)
# Math Scores by Grade
# Use the code provided to separate the data by grade
ninth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "9th")]
tenth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "10th")]
eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"] == "11th")]
twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "12th")]

# Group by `school_name` and take the mean of the `math_score` column for each.
ninth_grade_math_scores =ninth_graders.groupby(["school_name"]).mean(numeric_only=True)["math_score"]
tenth_grader_math_scores =tenth_graders.groupby(["school_name"]).mean(numeric_only=True)["math_score"]
eleventh_grader_math_scores =eleventh_graders.groupby(["school_name"]).mean(numeric_only=True)["math_score"]
twelfth_grader_math_scores =twelfth_graders.groupby(["school_name"]).mean(numeric_only=True)["math_score"]

# Combine each of the scores above into single DataFrame called `math_scores_by_grade`
math_scores_by_grade =pd.DataFrame({
    "9th": ninth_grade_math_scores,
    "10th": tenth_grader_math_scores,
    "11th": eleventh_grader_math_scores,
    "12th": twelfth_grader_math_scores})

math_scores_by_grade.head()

# Minor data wrangling
math_scores_by_grade.index.name = None

# Display the DataFrame
math_scores_by_grade
# Reading Score by Grade
# Use the code provided to separate the data by grade
ninth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "9th")]
tenth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "10th")]
eleventh_graders = school_data_complete_df[(school_data_complete_df["grade"] == "11th")]
twelfth_graders = school_data_complete_df[(school_data_complete_df["grade"] == "12th")]

# Group by `school_name` and take the mean of the the `reading_score` column for each.
ninth_grade_reading_scores =ninth_graders.groupby(["school_name"]).mean(numeric_only=True)["reading_score"]
tenth_grader_reading_scores =tenth_graders.groupby(["school_name"]).mean(numeric_only=True)["reading_score"]
eleventh_grader_reading_scores =eleventh_graders.groupby(["school_name"]).mean(numeric_only=True)["reading_score"]
twelfth_grader_reading_scores =twelfth_graders.groupby(["school_name"]).mean(numeric_only=True)["reading_score"]

# Combine each of the scores above into single DataFrame called `reading_scores_by_grade`
reading_scores_by_grade =pd.DataFrame({
    "9th": ninth_grade_reading_scores,
    "10th": tenth_grader_reading_scores,
    "11th": eleventh_grader_reading_scores,
    "12th": twelfth_grader_reading_scores})


# Minor data wrangling
reading_scores_by_grade = reading_scores_by_grade[["9th", "10th", "11th", "12th"]]
reading_scores_by_grade.index.name = None

# Display the DataFrame
reading_scores_by_grade
# Scores by School Spending
# Create a copy of the school summary since it has the "Per Student Budget"
school_spending = per_school_summary_df.copy()
# Establish the bins
spending_bins = [0, 585, 630, 645, 680]
labels = ["<$585", "$585-629", "$630-644", "$645-680"]
# Use `pd.cut` to categorize spending based on the bins.
per_school_summary_df["Spending Ranges (Per Student)"] = pd.cut(per_school_capita, spending_bins, labels=labels)
per_school_summary_df
#  Calculate averages for the desired columns.

spending_math_scores = per_school_summary_df.groupby("Spending Ranges (Per Student)")[numeric_cols].agg([np.mean])["Average Math Score"]

spending_reading_scores = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["Average Reading Score"]

spending_passing_math = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Math"]

spending_passing_reading = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean()["% Passing Reading"]
overall_passing_spending = per_school_summary_df.groupby(["Spending Ranges (Per Student)"]).mean(numeric_only=True)["% Overall Passing"]
# Assemble into DataFrame
#spending_summary =

# Display results
#spending_summary
# Scores by School Size
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-1999)", "Large (2000-5000)"]

# Categorize the spending based on the bins
# Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame.

per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=labels)

per_school_summary_df
# Calculate averages for the desired columns.
size_math_scores = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["Average Math Score"]
size_reading_scores = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["Average Reading Score"]
size_passing_math = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["% Passing Math"]
size_passing_reading = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["% Passing Reading"]
size_overall_passing = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["% Overall Passing"]
# Assemble into DataFrame
spending_summary =

# Display results
spending_summary

# Scores by School Size
# Establish the bins.
size_bins = [0, 1000, 2000, 5000]
labels = ["Small (<1000)", "Medium (1000-1999)", "Large (2000-5000)"]

# Categorize the spending based on the bins
# Use `pd.cut` on the "Total Students" column of the `per_school_summary` DataFrame.

per_school_summary_df["School Size"] = pd.cut(per_school_summary_df["Total Students"], size_bins, labels=labels)

per_school_summary_df

# Calculate averages for the desired columns.
size_math_scores = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["Average Math Score"]
size_reading_scores = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["Average Reading Score"]
size_passing_math = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["% Passing Math"]
size_passing_reading = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["% Passing Reading"]
size_overall_passing = per_school_summary_df.groupby(["School Size"]).mean(numeric_only=True)["% Overall Passing"]
# Create a DataFrame called `size_summary` that breaks down school performance based on school size (small, medium, or large).
# Use the scores above to create a new DataFrame called `size_summary`
size_summary =pd.DataFrame({
          "Average Math Score" : size_math_scores,
          "Average Reading Score": size_reading_scores,
          "% Passing Math": size_passing_math,
          "% Passing Reading": size_passing_reading,
          "% Overall Passing": size_overall_passing})
# Display results
size_summary
# Scores by School Type
# Group the per_school_summary DataFrame by "School Type" and average the results.
average_math_score_by_type = per_school_summary_df.groupby(["School Type"])["Average Math Score"].mean(numeric_only=True)
average_reading_score_by_type = per_school_summary_df.groupby(["School Type"])["Average Reading Score"].mean(numeric_only=True)
average_percent_passing_math_by_type = per_school_summary_df.groupby(["School Type"])["% Passing Math"].mean(numeric_only=True)
average_percent_passing_reading_by_type = per_school_summary_df.groupby(["School Type"])["% Passing Reading"].mean(numeric_only=True)
average_percent_overall_passing_by_type = per_school_summary_df.groupby(["School Type"])["% Overall Passing"].mean(numeric_only=True)
# Assemble the new data by type into a DataFrame called `type_summary`
type_summary =pd.DataFrame({
          "Average Math Score" : type_math_scores,
          "Average Reading Score": type_reading_scores,
          "% Passing Math": type_passing_math,
          "% Passing Reading": type_passing_reading,
          "% Overall Passing": type_overall_passing})

# Display results
type_summary





