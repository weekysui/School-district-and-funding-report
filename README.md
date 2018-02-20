

```python
import pandas as pd
csv_read = pd.read_csv("raw_data/schools_complete.csv")
school_df=pd.DataFrame(csv_read)
csvRead = pd.read_csv("raw_data/students_complete.csv")
student_df=pd.DataFrame(csvRead)
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
#District summary: total schools 
District = school_df.loc[school_df["type"]=="District"]
total_schools = len(District)
total_schools
```




    7




```python
#District schools total students
district_school = pd.merge(District,student_df,on="school")
total_students=district_school["school"].count()
total_students
```




    26976




```python
#Disctric schools total budget
total_budget = District["budget"].sum()
total_budget
```




    17347923




```python
avg_math = district_school["math_score"].mean()
avg_math
```




    76.98702550415184




```python
avg_reading = district_school["reading_score"].mean()
avg_reading
```




    80.96248517200475




```python
#percent of passing math
pass_math = len(district_school.loc[district_school["math_score"]>=60])
percent_pass_math = pass_math/total_students*100
print(percent_pass_math)
#percent of passing reading
pass_reading = len(district_school.loc[district_school["reading_score"]>=60])
percent_pass_reading = pass_reading/total_students*100
print(percent_pass_reading)
#overall passing rate (Average of the above two)
overall = (percent_pass_reading+percent_pass_math)/2
print(overall)
```

    89.0309905101
    100.0
    94.515495255



```python
district_df = pd.DataFrame({"Total Schools":[total_schools],
                           "Total Students":[total_students],
                           "Total Budget":[total_budget],
                           "Average Math Score":[avg_math],
                           "Average Reading Score":[avg_reading],
                           "% Passing Math":[percent_pass_math],
                           "% Passing Reading":[percent_pass_reading],
                           "% Overall Passing Rate":[overall]})
district_df=district_df[["Total Schools",
                         "Total Students",
                         "Total Budget",
                         "Average Math Score",
                         "Average Reading Score",
                         "% Passing Math",
                         "% Passing Reading",
                         "% Overall Passing Rate"]]
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
      <td>7</td>
      <td>26976</td>
      <td>17347923</td>
      <td>76.987026</td>
      <td>80.962485</td>
      <td>89.030991</td>
      <td>100.0</td>
      <td>94.515495</td>
    </tr>
  </tbody>
</table>
</div>




```python
#school budet
schoolBudget = school_df["budget"]
#total students for each school
totalStudents= school_df["size"]
#per student budget
perBudget = schoolBudget / totalStudents
#add per student budget to school_df
school_df["Per Student Budget"]=perBudget
#rename type, size, budget
sch_df = school_df.rename(columns = {"type":"School Type",
                             "size":"Total Students",
                             "budget":"Total School Budget"})
#set school as index
sch_df = sch_df.set_index("school")
#pass reading, pass math
passReading = pd.DataFrame(student_df.loc[student_df["reading_score"]>=70])
each_school_pass_reading = passReading["school"].value_counts()
percentReading = each_school_pass_reading / sch_df["Total Students"]*100

passMath = pd.DataFrame(student_df.loc[student_df["math_score"]>=70])
each_school_pass_math = passMath["school"].value_counts()
percentMath = each_school_pass_math / sch_df["Total Students"]*100
overallPassing = (percentReading+percentMath)/2

#student_df groupby ["school"]
grouped_student_df = student_df.groupby(["school"])
#average reading_score
avgReading = grouped_student_df["reading_score"].mean()
#average math_score
avgMath = grouped_student_df["math_score"].mean()

sch_df["Average Math Score"]=avgMath
sch_df["Average Reading Score"]=avgReading
sch_df["% Passing Math"] = percentMath
sch_df["% Passing Reading"] = percentReading
sch_df["% Overall Passing Rate"] = overallPassing

del sch_df['School ID']
sch_df

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
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
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
      <td>73.500171</td>
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
      <td>73.363852</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>93.867121</td>
      <td>95.854628</td>
      <td>94.860875</td>
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
      <td>73.807983</td>
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
      <td>95.265668</td>
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
      <td>95.203679</td>
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
      <td>95.586652</td>
    </tr>
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
      <td>74.306672</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>92.505855</td>
      <td>96.252927</td>
      <td>94.379391</td>
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
      <td>95.270270</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>93.333333</td>
      <td>96.611111</td>
      <td>94.972222</td>
    </tr>
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
      <td>73.293323</td>
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
      <td>73.639992</td>
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
      <td>73.804308</td>
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
      <td>95.290520</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Top Performing Schools (By Passing Rate)
top_performing = school_df.sort_values(["% Overall Passing Rate"],ascending = False)
top_performing.head()
```


    ---------------------------------------------------------------------------

    KeyError                                  Traceback (most recent call last)

    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/indexes/base.py in get_loc(self, key, method, tolerance)
       2441             try:
    -> 2442                 return self._engine.get_loc(key)
       2443             except KeyError:


    pandas/_libs/index.pyx in pandas._libs.index.IndexEngine.get_loc()


    pandas/_libs/index.pyx in pandas._libs.index.IndexEngine.get_loc()


    pandas/_libs/hashtable_class_helper.pxi in pandas._libs.hashtable.PyObjectHashTable.get_item()


    pandas/_libs/hashtable_class_helper.pxi in pandas._libs.hashtable.PyObjectHashTable.get_item()


    KeyError: '% Overall Passing Rate'

    
    During handling of the above exception, another exception occurred:


    KeyError                                  Traceback (most recent call last)

    <ipython-input-96-6585592bf883> in <module>()
          1 #Top Performing Schools (By Passing Rate)
    ----> 2 top_performing = school_df.sort_values(["% Overall Passing Rate"],ascending = False)
          3 top_performing.head()


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/frame.py in sort_values(self, by, axis, ascending, inplace, kind, na_position)
       3184 
       3185             by = by[0]
    -> 3186             k = self.xs(by, axis=other_axis).values
       3187             if k.ndim == 2:
       3188 


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/generic.py in xs(self, key, axis, level, drop_level)
       2021 
       2022         if axis == 1:
    -> 2023             return self[key]
       2024 
       2025         self._consolidate_inplace()


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/frame.py in __getitem__(self, key)
       1962             return self._getitem_multilevel(key)
       1963         else:
    -> 1964             return self._getitem_column(key)
       1965 
       1966     def _getitem_column(self, key):


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/frame.py in _getitem_column(self, key)
       1969         # get column
       1970         if self.columns.is_unique:
    -> 1971             return self._get_item_cache(key)
       1972 
       1973         # duplicate columns & possible reduce dimensionality


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/generic.py in _get_item_cache(self, item)
       1643         res = cache.get(item)
       1644         if res is None:
    -> 1645             values = self._data.get(item)
       1646             res = self._box_item_values(item, values)
       1647             cache[item] = res


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/internals.py in get(self, item, fastpath)
       3588 
       3589             if not isnull(item):
    -> 3590                 loc = self.items.get_loc(item)
       3591             else:
       3592                 indexer = np.arange(len(self.items))[isnull(self.items)]


    /Applications/anaconda3/envs/PythonData/lib/python3.6/site-packages/pandas/core/indexes/base.py in get_loc(self, key, method, tolerance)
       2442                 return self._engine.get_loc(key)
       2443             except KeyError:
    -> 2444                 return self._engine.get_loc(self._maybe_cast_indexer(key))
       2445 
       2446         indexer = self.get_indexer([key], method=method, tolerance=tolerance)


    pandas/_libs/index.pyx in pandas._libs.index.IndexEngine.get_loc()


    pandas/_libs/index.pyx in pandas._libs.index.IndexEngine.get_loc()


    pandas/_libs/hashtable_class_helper.pxi in pandas._libs.hashtable.PyObjectHashTable.get_item()


    pandas/_libs/hashtable_class_helper.pxi in pandas._libs.hashtable.PyObjectHashTable.get_item()


    KeyError: '% Overall Passing Rate'



```python
#Bottom Performing Schools (By passing Rate)
bottom_performing = school_df.sort_values(["% Overall Passing Rate"])
bottom_performing.head()

```


```python
grade = student_df[["school","grade","math_score","reading_score"]]





```


```python



```
