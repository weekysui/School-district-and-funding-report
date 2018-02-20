## PySchool Analysis:
- The school size and spending per student seems correlate. The larger the school size the lower passing rates and mean scores.
- Charter school has better performing than the district school. The top 5 perfroming schools are charter schools, and the bottom 5 are the district schools. 
- Accoring to the math scores by grade and the reading scores by grade, you can see their mean scores stay consistent across grades. 

## District summary
```python
# import files, create dataframe
import pandas as pd
csv_read = pd.read_csv("raw_data/schools_complete.csv")
school_df=pd.DataFrame(csv_read)
csvRead = pd.read_csv("raw_data/students_complete.csv")
student_df=pd.DataFrame(csvRead)
#rename school_df name to school:
school_df = school_df.rename(columns={"name":"school"})
school_df.head

```
    <bound method NDFrame.head of     School ID                 school      type  size   budget
    0           0      Huang High School  District  2917  1910635
    1           1   Figueroa High School  District  2949  1884411
    2           2    Shelton High School   Charter  1761  1056600
    3           3  Hernandez High School  District  4635  3022020
    4           4    Griffin High School   Charter  1468   917500
    5           5     Wilson High School   Charter  2283  1319574
    6           6    Cabrera High School   Charter  1858  1081356
    7           7     Bailey High School  District  4976  3124928
    8           8     Holden High School   Charter   427   248087
    9           9       Pena High School   Charter   962   585858
    10         10     Wright High School   Charter  1800  1049400
    11         11  Rodriguez High School  District  3999  2547363
    12         12    Johnson High School  District  4761  3094650
    13         13       Ford High School  District  2739  1763916
    14         14     Thomas High School   Charter  1635  1043130>
```python
# District Summary
total_school = school_df["school"].count()
#total students: merge 2 dataframe:
merge_df = pd.merge(school_df,student_df,on = "school")
total_students = merge_df["name"].count()
#total budget:
total_budget = school_df["budget"].sum()
#average math score, average reading score:
avg_math=merge_df["math_score"].mean()
avg_reading = merge_df["reading_score"].mean()
#%pass math, %pass reading
student_math= merge_df.loc[merge_df["math_score"]>=70]
pass_math = len(student_math)/total_students *100
student_reading = merge_df.loc[merge_df["reading_score"]>=70]
pass_reading = len(student_reading)/total_students *100
#overall passing rate
student_overall = merge_df.loc[(merge_df["math_score"]>=70)&(merge_df["reading_score"]>=70)]
overall= len(student_overall)/total_students *100

district_df = pd.DataFrame({"Total Schools":[total_school],
                           "Total Students":[total_students],
                           "Total Budget":[total_budget],
                           "Average Math Score":[avg_math],
                           "Average Reading Score":[avg_reading],
                           "% Passing Math":[pass_math],
                           "% Passing Reading":[pass_reading],
                           "% Overall Passing Rate":[overall]})
district_df = district_df[["Total Schools",
                           "Total Students",
                           "Total Budget",
                           "Average Math Score",
                           "Average Reading Score",
                           "% Passing Math",
                           "% Passing Reading",
                           "% Overall Passing Rate"]]
#format
district_df["Total Students"]=district_df["Total Students"].map("{:,}".format)
district_df["Total Budget"]=district_df["Total Budget"].map("${:,}".format)
district_df["Average Math Score"]=district_df["Average Math Score"].map("{:.2f}".format)
district_df["Average Reading Score"]=district_df["Average Reading Score"].map("{:.2f}".format)
district_df["% Passing Math"]=district_df["% Passing Math"].map("{:.2f}%".format)
district_df["% Passing Reading"]=district_df["% Passing Reading"].map("{:.2f}%".format)
district_df["% Overall Passing Rate"]=district_df["% Overall Passing Rate"].map("{:.2f}%".format)

district_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39,170</td>
      <td>$24,649,428</td>
      <td>78.99</td>
      <td>81.88</td>
      <td>74.98%</td>
      <td>85.81%</td>
      <td>65.17%</td>
    </tr>
  </tbody>
</table>
</div>



## School Summary
```python
# set school as index in school_df, get the total budget, and type of schools
school_index = school_df.set_index("school")
sch_budget = school_index["budget"]
sch_type = school_index["type"]
# budget per student
student_budget = sch_budget / school_index["size"]

# set school as index, and then group the school for merge_df
index = merge_df.set_index('school')
grouped = index.groupby(['school'])
# avg math score, avg reading score by school
mean_math = grouped["math_score"].mean()
mean_reading = grouped["reading_score"].mean()
total_stu = grouped["name"].count()
#pass math
grouped_math = student_math.groupby("school")
pass_math_percent = grouped_math["name"].count() / total_stu *100
#pass reading
grouped_reading = student_reading.groupby("school")
pass_reading_percent = grouped_reading["name"].count() / total_stu *100

#overall passing
grouped_overall = student_overall.groupby("school")
overall_passing = grouped_overall["name"].count() / total_stu *100

sch_summary=pd.DataFrame({"School Type":sch_type,
                         "Total Students":total_stu,
                         "Total School Budget":sch_budget,
                         "Per Student Budget":student_budget,
                         "Average Math Score":mean_math,
                         "Average Reading Score":mean_reading,
                         "% Passing Math":pass_math_percent,
                         "% Passing Reading":pass_reading_percent,
                         "% Overall Passing Rate":overall_passing})
sch_summary = sch_summary[["School Type",
                           "Total Students",
                           "Total School Budget",
                           "Per Student Budget",
                           "Average Math Score",
                           "Average Reading Score",
                           "% Passing Math",
                           "% Passing Reading",
                           "% Overall Passing Rate"]]

sch_summary.head()




```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>66.680064</td>
      <td>81.933280</td>
      <td>54.642283</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>91.334769</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>53.204476</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>68.309602</td>
      <td>79.299014</td>
      <td>54.289887</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>90.599455</td>
    </tr>
  </tbody>
</table>
</div>



## Top Performing Schools (By Passing Rate)
```python
#Top Performing Schools (By Passing Rate)
top_performing = sch_summary.sort_values(["% Overall Passing Rate"],ascending = False)
top_performing.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Cabrera High School</th>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>94.133477</td>
      <td>97.039828</td>
      <td>91.334769</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>93.272171</td>
      <td>97.308869</td>
      <td>90.948012</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>93.392371</td>
      <td>97.138965</td>
      <td>90.599455</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>93.867718</td>
      <td>96.539641</td>
      <td>90.582567</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>94.594595</td>
      <td>95.945946</td>
      <td>90.540541</td>
    </tr>
  </tbody>
</table>
</div>


## Bottom Performing Schools (By Passing Rate)

```python
#Bottom Performing Schools (By passing Rate)
bottom_performing = sch_summary.sort_values(["% Overall Passing Rate"])
bottom_performing.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School Budget</th>
      <th>Per Student Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rodriguez High School</th>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>66.366592</td>
      <td>80.220055</td>
      <td>52.988247</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>65.988471</td>
      <td>80.739234</td>
      <td>53.204476</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>65.683922</td>
      <td>81.316421</td>
      <td>53.513884</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>66.752967</td>
      <td>80.862999</td>
      <td>53.527508</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>66.057551</td>
      <td>81.222432</td>
      <td>53.539172</td>
    </tr>
  </tbody>
</table>
</div>



## Math scores by grade
```python
#Math Scores by Grade
ninth = student_df.loc[student_df["grade"]=='9th'].groupby("school")
ninth_math = ninth["math_score"].mean()
tenth = student_df.loc[student_df["grade"]=='10th'].groupby("school")
tenth_math = tenth["math_score"].mean()
eleventh=student_df.loc[student_df["grade"]=='11th'].groupby("school")
eleventh_math = eleventh["math_score"].mean()
twelfth = student_df.loc[student_df["grade"]=='12th'].groupby("school")
twelfth_math = twelfth["math_score"].mean()

math_grade = pd.DataFrame({"9th":ninth_math,
                          "10th":tenth_math,
                          "11th":eleventh_math,
                          "12th":twelfth_math})
math_grade=math_grade[["9th","10th","11th","12th"]]

math_grade.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.083676</td>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.094697</td>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.403037</td>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.361345</td>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.044010</td>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
    </tr>
  </tbody>
</table>
</div>



## Reading Scores by Grade
```python
#Reading Score by Grade
nine = student_df.loc[student_df["grade"]=='9th'].groupby("school")
nine_reading = nine["reading_score"].mean()
ten  = student_df.loc[student_df["grade"]=='10th'].groupby("school")
ten_reading = ten["reading_score"].mean()
eleven=student_df.loc[student_df["grade"]=='11th'].groupby("school")
eleven_reading = eleven["reading_score"].mean()
twelve = student_df.loc[student_df["grade"]=='12th'].groupby("school")
twelve_reading = twelve["reading_score"].mean()

reading_grade = pd.DataFrame({"9th":nine_reading,
                          "10th":ten_reading,
                          "11th":eleven_reading,
                          "12th":twelve_reading})
reading_grade=reading_grade[["9th","10th","11th","12th"]]

reading_grade.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.303155</td>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.676136</td>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.198598</td>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.632653</td>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.369193</td>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Spending
```python
#Scores by School Spending
score_summary = pd.DataFrame(sch_summary)
bins = [0,585,615,645,675]
group_names = ["< $585","$585-615","$615-645","$645-675"]
score_summary["Per Student Budget"]=pd.cut(score_summary["Per Student Budget"],bins,labels = group_names)

#rename per student budget to "Spending Ranges (Per Student)"
score = score_summary.rename(columns={"Per Student Budget":"Spending Ranges (Per Student)"})

#group the "Spending Ranges (Per Student)"
score = score.groupby(["Spending Ranges (Per Student)"])
score_spending = pd.DataFrame(score["Average Math Score"].mean())
score_spending["Average Reading Score"]=score["Average Reading Score"].mean()
score_spending["% Passing Math"]=score["% Passing Math"].mean()
score_spending["% Passing Reading"]=score["% Passing Reading"].mean()
score_spending["% Overall Passing Rate"]=score["% Overall Passing Rate"].mean()

score_spending


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>Spending Ranges (Per Student)</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; $585</th>
      <td>83.455399</td>
      <td>83.933814</td>
      <td>93.460096</td>
      <td>96.610877</td>
      <td>90.369459</td>
    </tr>
    <tr>
      <th>$585-615</th>
      <td>83.599686</td>
      <td>83.885211</td>
      <td>94.230858</td>
      <td>95.900287</td>
      <td>90.216324</td>
    </tr>
    <tr>
      <th>$615-645</th>
      <td>79.079225</td>
      <td>81.891436</td>
      <td>75.668212</td>
      <td>86.106569</td>
      <td>66.112060</td>
    </tr>
    <tr>
      <th>$645-675</th>
      <td>76.997210</td>
      <td>81.027843</td>
      <td>66.164813</td>
      <td>81.133951</td>
      <td>53.526855</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Size
```python
sch_size=pd.DataFrame(sch_summary)
#rename total students to "School Size"
sch_size = sch_size.rename(columns={"Total Students":"School Size"})
#bin number to groups_size
bins_size = [0,1000,2000,5000]
groups_size = ["Small (<1000)","Medium (1000-2000)","Large (2000-5000)"]
sch_size["School Size"]=pd.cut(sch_size["School Size"],bins_size,labels = groups_size)
#groupby school size
size_group = sch_size.groupby(["School Size"])
size_score = pd.DataFrame(size_group["Average Math Score"].mean())
size_score["Average Reading Score"]=size_group["Average Reading Score"].mean()
size_score["% Passing Math"]=size_group["% Passing Math"].mean()
size_score["% Passing Reading"]=size_group["% Passing Reading"].mean()
size_score["% Overall Passing Rate"]=size_group["% Overall Passing Rate"].mean()

size_score
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Size</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Small (&lt;1000)</th>
      <td>83.821598</td>
      <td>83.929843</td>
      <td>93.550225</td>
      <td>96.099437</td>
      <td>89.883853</td>
    </tr>
    <tr>
      <th>Medium (1000-2000)</th>
      <td>83.374684</td>
      <td>83.864438</td>
      <td>93.599695</td>
      <td>96.790680</td>
      <td>90.621535</td>
    </tr>
    <tr>
      <th>Large (2000-5000)</th>
      <td>77.746417</td>
      <td>81.344493</td>
      <td>69.963361</td>
      <td>82.766634</td>
      <td>58.286003</td>
    </tr>
  </tbody>
</table>
</div>



## Scores by School Type
```python
#groupby school type
group_type = sch_summary.groupby(["School Type"])
type_score = pd.DataFrame(group_type["Average Math Score"].mean())
type_score["Average Reading Score"]=group_type["Average Reading Score"].mean()
type_score["% Passing Math"]=group_type["% Passing Math"].mean()
type_score["% Passing Reading"]=group_type["% Passing Reading"].mean()
type_score["% Overall Passing Rate"]=group_type["% Overall Passing Rate"].mean()
type_score
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Overall Passing Rate</th>
    </tr>
    <tr>
      <th>School Type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83.473852</td>
      <td>83.896421</td>
      <td>93.620830</td>
      <td>96.586489</td>
      <td>90.432244</td>
    </tr>
    <tr>
      <th>District</th>
      <td>76.956733</td>
      <td>80.966636</td>
      <td>66.548453</td>
      <td>80.799062</td>
      <td>53.672208</td>
    </tr>
  </tbody>
</table>
</div>


