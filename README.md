# Crowdfunding_ETL


## Overview
Crowdfunding dataset contains information on backers who made pledges to live projects. Perform the ETL (extract, transform and load) process on the dataset.

## Resources

* PgAdmin 4
* Visual Studio Code
* Jupyter Notebook
* QuickDBD

## ERD
![](crowdfunding_db_relationships.png)

## ETL
1.  Extract: Import crowdfunding.xlsx into Jupyter NB dataframe
2.  Read the various sheets within the excel file.
3.  Get a brief summary
4.  Inspect unique values
5.  Split one column into two columns
6.  Transform category and subcategory
7.  Use comprehension lists to create category ids
8.  Convert datatypes and date formats
9.  Merge columns
10.  Drop columns
11.  Reorder columns
12.  Export campaign data as a csv
13.  Create the contacts dataframe -


### **Import the necessary libraries**

```python
import pandas as pd
# Import the Numpy dependency
import numpy as np
from datetime import datetime as dt
import json
```

### **Read the dataset**


```python
# Read the data into a Pandas DataFrame
crowdfunding_data = pd.ExcelFile("crowdfunding.xlsx")
# Get the sheet names.
crowdfunding_data.sheet_names
```


    ['crowdfunding_info', 'contact_info']


```python
# Get the crowdfunding_info from the crowdfunding_info worksheet. 
crowdfunding_info_df = pd.read_excel(crowdfunding_data, sheet_name='crowdfunding_info')
crowdfunding_info_df.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>blurb</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launched_at</th>
      <th>deadline</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100</td>
      <td>0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>1581573600</td>
      <td>1614578400</td>
      <td>False</td>
      <td>False</td>
      <td>food/food trucks</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400</td>
      <td>14560</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>1611554400</td>
      <td>1621918800</td>
      <td>False</td>
      <td>True</td>
      <td>music/rock</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400</td>
      <td>142523</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>1608184800</td>
      <td>1640844000</td>
      <td>False</td>
      <td>False</td>
      <td>technology/web</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200</td>
      <td>2477</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>1634792400</td>
      <td>1642399200</td>
      <td>False</td>
      <td>False</td>
      <td>music/rock</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600</td>
      <td>5265</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>1608530400</td>
      <td>1629694800</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
    </tr>
  </tbody>
</table>
</div>




```python
crowdfunding_info_df.tail()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>blurb</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launched_at</th>
      <th>deadline</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>995</th>
      <td>2986</td>
      <td>Manning-Hamilton</td>
      <td>Vision-oriented scalable definition</td>
      <td>97300</td>
      <td>153216</td>
      <td>successful</td>
      <td>2043</td>
      <td>US</td>
      <td>USD</td>
      <td>1609221600</td>
      <td>1622350800</td>
      <td>False</td>
      <td>True</td>
      <td>food/food trucks</td>
    </tr>
    <tr>
      <th>996</th>
      <td>2031</td>
      <td>Butler LLC</td>
      <td>Future-proofed upward-trending migration</td>
      <td>6600</td>
      <td>4814</td>
      <td>failed</td>
      <td>112</td>
      <td>US</td>
      <td>USD</td>
      <td>1634274000</td>
      <td>1638252000</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
    </tr>
    <tr>
      <th>997</th>
      <td>1627</td>
      <td>Ball LLC</td>
      <td>Right-sized full-range throughput</td>
      <td>7600</td>
      <td>4603</td>
      <td>canceled</td>
      <td>139</td>
      <td>IT</td>
      <td>EUR</td>
      <td>1636174800</td>
      <td>1639116000</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
    </tr>
    <tr>
      <th>998</th>
      <td>2175</td>
      <td>Taylor, Santiago and Flores</td>
      <td>Polarized composite customer loyalty</td>
      <td>66600</td>
      <td>37823</td>
      <td>failed</td>
      <td>374</td>
      <td>US</td>
      <td>USD</td>
      <td>1602133200</td>
      <td>1618117200</td>
      <td>False</td>
      <td>True</td>
      <td>music/indie rock</td>
    </tr>
    <tr>
      <th>999</th>
      <td>1788</td>
      <td>Hernandez, Norton and Kelley</td>
      <td>Expanded eco-centric policy</td>
      <td>111100</td>
      <td>62819</td>
      <td>canceled</td>
      <td>1122</td>
      <td>US</td>
      <td>USD</td>
      <td>1609308000</td>
      <td>1629262800</td>
      <td>False</td>
      <td>False</td>
      <td>food/food trucks</td>
    </tr>
  </tbody>
</table>
</div>




```python
crowdfunding_info_df.sample(n=5)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>blurb</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launched_at</th>
      <th>deadline</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>525</th>
      <td>400</td>
      <td>Greene, Lloyd and Sims</td>
      <td>Balanced leadingedge data-warehouse</td>
      <td>2100</td>
      <td>1768</td>
      <td>failed</td>
      <td>63</td>
      <td>US</td>
      <td>USD</td>
      <td>1619845200</td>
      <td>1630558800</td>
      <td>False</td>
      <td>False</td>
      <td>technology/wearables</td>
    </tr>
    <tr>
      <th>230</th>
      <td>1632</td>
      <td>Miranda, Hall and Mcgrath</td>
      <td>Progressive value-added ability</td>
      <td>2400</td>
      <td>10084</td>
      <td>successful</td>
      <td>101</td>
      <td>US</td>
      <td>USD</td>
      <td>1611554400</td>
      <td>1623733200</td>
      <td>False</td>
      <td>False</td>
      <td>games/video games</td>
    </tr>
    <tr>
      <th>506</th>
      <td>277</td>
      <td>Robles, Bell and Gonzalez</td>
      <td>Customizable background monitoring</td>
      <td>18000</td>
      <td>166874</td>
      <td>successful</td>
      <td>2528</td>
      <td>US</td>
      <td>USD</td>
      <td>1628571600</td>
      <td>1629090000</td>
      <td>False</td>
      <td>True</td>
      <td>theater/plays</td>
    </tr>
    <tr>
      <th>837</th>
      <td>2575</td>
      <td>Cook-Ortiz</td>
      <td>Right-sized dedicated standardization</td>
      <td>17700</td>
      <td>150960</td>
      <td>successful</td>
      <td>1797</td>
      <td>US</td>
      <td>USD</td>
      <td>1623387600</td>
      <td>1629608400</td>
      <td>False</td>
      <td>False</td>
      <td>music/jazz</td>
    </tr>
    <tr>
      <th>548</th>
      <td>1944</td>
      <td>York-Pitts</td>
      <td>Monitored discrete toolset</td>
      <td>66100</td>
      <td>179074</td>
      <td>successful</td>
      <td>2985</td>
      <td>US</td>
      <td>USD</td>
      <td>1634619600</td>
      <td>1645596000</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get the contact_info from the contact_info worksheet.
# Increase the width of the column.
pd.set_option('max_colwidth', 400)
contact_info_df = pd.read_excel(crowdfunding_data, sheet_name='contact_info', header=3)
contact_info_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_info</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{"contact_id": 4661, "name": "Cecilia Velasco", "email": "cecilia.velasco@rodrigues.fr"}</td>
    </tr>
    <tr>
      <th>1</th>
      <td>{"contact_id": 3765, "name": "Mariana Ellis", "email": "mariana.ellis@rossi.org"}</td>
    </tr>
    <tr>
      <th>2</th>
      <td>{"contact_id": 4187, "name": "Sofie Woods", "email": "sofie.woods@riviere.com"}</td>
    </tr>
    <tr>
      <th>3</th>
      <td>{"contact_id": 4941, "name": "Jeanette Iannotti", "email": "jeanette.iannotti@yahoo.com"}</td>
    </tr>
    <tr>
      <th>4</th>
      <td>{"contact_id": 2199, "name": "Samuel Sorgatz", "email": "samuel.sorgatz@gmail.com"}</td>
    </tr>
  </tbody>
</table>
</div>




```python
contact_info_df.tail()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_info</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>995</th>
      <td>{"contact_id": 3684, "name": "Whitney Noack", "email": "whitney.noack@laboratorios.org"}</td>
    </tr>
    <tr>
      <th>996</th>
      <td>{"contact_id": 5784, "name": "Gelsomina Migliaccio", "email": "gelsomina.migliaccio@junk.com"}</td>
    </tr>
    <tr>
      <th>997</th>
      <td>{"contact_id": 1498, "name": "Evangelista Pereira", "email": "evangelista.pereira@thompson-peterson.biz"}</td>
    </tr>
    <tr>
      <th>998</th>
      <td>{"contact_id": 6073, "name": "Gareth Comolli", "email": "gareth.comolli@tiscali.fr"}</td>
    </tr>
    <tr>
      <th>999</th>
      <td>{"contact_id": 4939, "name": "Michelangelo Hess", "email": "michelangelo.hess@bouygtel.fr"}</td>
    </tr>
  </tbody>
</table>
</div>




```python
contact_info_df.sample(n=5)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_info</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>802</th>
      <td>{"contact_id": 5306, "name": "Nicolas Palacios", "email": "nicolas.palacios@meraz.com"}</td>
    </tr>
    <tr>
      <th>31</th>
      <td>{"contact_id": 5560, "name": "Valeria Rich", "email": "valeria.rich@turchetta-mondadori.it"}</td>
    </tr>
    <tr>
      <th>419</th>
      <td>{"contact_id": 1613, "name": "Amelia Olvera", "email": "amelia.olvera@hotmail.com"}</td>
    </tr>
    <tr>
      <th>517</th>
      <td>{"contact_id": 3274, "name": "Rolando Bibi", "email": "rolando.bibi@tin.it"}</td>
    </tr>
    <tr>
      <th>530</th>
      <td>{"contact_id": 2354, "name": "Edelgard Kreusel", "email": "edelgard.kreusel@hotmail.com"}</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get a brief summary of the crowdfunding_info DataFrame. 
crowdfunding_info_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1000 entries, 0 to 999
    Data columns (total 14 columns):
     #   Column                   Non-Null Count  Dtype 
    ---  ------                   --------------  ----- 
     0   cf_id                    1000 non-null   int64 
     1   company_name             1000 non-null   object
     2   blurb                    1000 non-null   object
     3   goal                     1000 non-null   int64 
     4   pledged                  1000 non-null   int64 
     5   outcome                  1000 non-null   object
     6   backers_count            1000 non-null   int64 
     7   country                  1000 non-null   object
     8   currency                 1000 non-null   object
     9   launched_at              1000 non-null   int64 
     10  deadline                 1000 non-null   int64 
     11  staff_pick               1000 non-null   bool  
     12  spotlight                1000 non-null   bool  
     13  category & sub-category  1000 non-null   object
    dtypes: bool(2), int64(6), object(6)
    memory usage: 95.8+ KB



```python
# Get a brief summary of the contact_info DataFrame. 
contact_info_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1000 entries, 0 to 999
    Data columns (total 1 columns):
     #   Column        Non-Null Count  Dtype 
    ---  ------        --------------  ----- 
     0   contact_info  1000 non-null   object
    dtypes: object(1)
    memory usage: 7.9+ KB


### **Inspect the Data**


```python
# Get the unique values in the category & sub-category column. 
crowdfunding_info_df["category & sub-category"].unique()
```




    array(['food/food trucks', 'music/rock', 'technology/web',
           'theater/plays', 'film & video/documentary',
           'music/electric music', 'film & video/drama', 'music/indie rock',
           'technology/wearables', 'publishing/nonfiction',
           'film & video/animation', 'games/video games',
           'film & video/shorts', 'publishing/fiction',
           'photography/photography books', 'publishing/radio & podcasts',
           'music/metal', 'music/jazz', 'publishing/translations',
           'film & video/television', 'games/mobile games',
           'music/world music', 'film & video/science fiction',
           'journalism/audio'], dtype=object)



**Observations:**
- The `category` & `sub-category` column has 24 unique values

### **Spilt into Two Columns**


```python
# Assign the category and subcategory values to category and subcategory columns.
crowdfunding_info_df[["category","subcategory"]] = crowdfunding_info_df["category & sub-category"].str.split('/', n=1, expand=True)
crowdfunding_info_df.head(10)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>blurb</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launched_at</th>
      <th>deadline</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
      <th>category</th>
      <th>subcategory</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100</td>
      <td>0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>1581573600</td>
      <td>1614578400</td>
      <td>False</td>
      <td>False</td>
      <td>food/food trucks</td>
      <td>food</td>
      <td>food trucks</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400</td>
      <td>14560</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>1611554400</td>
      <td>1621918800</td>
      <td>False</td>
      <td>True</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400</td>
      <td>142523</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>1608184800</td>
      <td>1640844000</td>
      <td>False</td>
      <td>False</td>
      <td>technology/web</td>
      <td>technology</td>
      <td>web</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200</td>
      <td>2477</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>1634792400</td>
      <td>1642399200</td>
      <td>False</td>
      <td>False</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600</td>
      <td>5265</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>1608530400</td>
      <td>1629694800</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2057</td>
      <td>Harris Group</td>
      <td>Open-source optimizing database</td>
      <td>7600</td>
      <td>13195</td>
      <td>successful</td>
      <td>174</td>
      <td>DK</td>
      <td>DKK</td>
      <td>1607666400</td>
      <td>1630213200</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1894</td>
      <td>Ortiz, Coleman and Mitchell</td>
      <td>Operative upward-trending algorithm</td>
      <td>5200</td>
      <td>1090</td>
      <td>failed</td>
      <td>18</td>
      <td>GB</td>
      <td>GBP</td>
      <td>1596171600</td>
      <td>1620709200</td>
      <td>False</td>
      <td>False</td>
      <td>film &amp; video/documentary</td>
      <td>film &amp; video</td>
      <td>documentary</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2669</td>
      <td>Carter-Guzman</td>
      <td>Centralized cohesive challenge</td>
      <td>4500</td>
      <td>14741</td>
      <td>successful</td>
      <td>227</td>
      <td>DK</td>
      <td>DKK</td>
      <td>1608616800</td>
      <td>1632200400</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1114</td>
      <td>Nunez-Richards</td>
      <td>Exclusive attitude-oriented intranet</td>
      <td>110100</td>
      <td>21946</td>
      <td>live</td>
      <td>708</td>
      <td>DK</td>
      <td>DKK</td>
      <td>1586322000</td>
      <td>1615356000</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
    </tr>
    <tr>
      <th>9</th>
      <td>970</td>
      <td>Rangel, Holt and Jones</td>
      <td>Open-source fresh-thinking model</td>
      <td>6200</td>
      <td>3208</td>
      <td>failed</td>
      <td>44</td>
      <td>US</td>
      <td>USD</td>
      <td>1628830800</td>
      <td>1630386000</td>
      <td>False</td>
      <td>False</td>
      <td>music/electric music</td>
      <td>music</td>
      <td>electric music</td>
    </tr>
  </tbody>
</table>
</div>



### **Transform the Category and Subcategory Data**


```python
# Get the distinct values in the category and subcategory columns.
print(crowdfunding_info_df["category"].nunique())
print(crowdfunding_info_df["subcategory"].nunique())
```

    9
    24



```python
# Get the unique categories and subcategories in separate lists.
categories = crowdfunding_info_df["category"].unique()
subcategories = crowdfunding_info_df["subcategory"].unique()
print(categories)
print()
print(subcategories)
```

    ['food' 'music' 'technology' 'theater' 'film & video' 'publishing' 'games'
     'photography' 'journalism']
    
    ['food trucks' 'rock' 'web' 'plays' 'documentary' 'electric music' 'drama'
     'indie rock' 'wearables' 'nonfiction' 'animation' 'video games' 'shorts'
     'fiction' 'photography books' 'radio & podcasts' 'metal' 'jazz'
     'translations' 'television' 'mobile games' 'world music'
     'science fiction' 'audio']



```python
# Create numpy arrays that have 1-10 for the category_ids and 1-25 for the subcategory_ids.
category_ids = np.arange(1, 10)
subcategory_ids = np.arange(1, 25)
```


```python
# Use a list comprehension to add "cat0" to each category_id. 
cat_ids = ["cat0" + str(cat_id) for cat_id in category_ids]

# Use a list comprehension to add "scat0" to each subcategory_id. 
scat_ids = ["scat0" + str(scat_id) for scat_id in subcategory_ids ]

print(cat_ids)
print(scat_ids)
```

    ['cat01', 'cat02', 'cat03', 'cat04', 'cat05', 'cat06', 'cat07', 'cat08', 'cat09']
    ['scat01', 'scat02', 'scat03', 'scat04', 'scat05', 'scat06', 'scat07', 'scat08', 'scat09', 'scat010', 'scat011', 'scat012', 'scat013', 'scat014', 'scat015', 'scat016', 'scat017', 'scat018', 'scat019', 'scat020', 'scat021', 'scat022', 'scat023', 'scat024']



```python
# Create a category DataFrame with the cat_ids array as the category_id and categories list as the category name.
category_df = pd.DataFrame({
    "category_id": cat_ids,
    "category" : categories
})

# Create a subcategory DataFrame with the scat_ids array as the subcategory_id and subcategories list as the subcategory name.
subcategory_df = pd.DataFrame({
    "subcategory_id": scat_ids,
    "subcategory" : subcategories
})

category_df.head(10)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category_id</th>
      <th>category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>cat01</td>
      <td>food</td>
    </tr>
    <tr>
      <th>1</th>
      <td>cat02</td>
      <td>music</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cat03</td>
      <td>technology</td>
    </tr>
    <tr>
      <th>3</th>
      <td>cat04</td>
      <td>theater</td>
    </tr>
    <tr>
      <th>4</th>
      <td>cat05</td>
      <td>film &amp; video</td>
    </tr>
    <tr>
      <th>5</th>
      <td>cat06</td>
      <td>publishing</td>
    </tr>
    <tr>
      <th>6</th>
      <td>cat07</td>
      <td>games</td>
    </tr>
    <tr>
      <th>7</th>
      <td>cat08</td>
      <td>photography</td>
    </tr>
    <tr>
      <th>8</th>
      <td>cat09</td>
      <td>journalism</td>
    </tr>
  </tbody>
</table>
</div>




```python
subcategory_df.head(10)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>subcategory_id</th>
      <th>subcategory</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>scat01</td>
      <td>food trucks</td>
    </tr>
    <tr>
      <th>1</th>
      <td>scat02</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>2</th>
      <td>scat03</td>
      <td>web</td>
    </tr>
    <tr>
      <th>3</th>
      <td>scat04</td>
      <td>plays</td>
    </tr>
    <tr>
      <th>4</th>
      <td>scat05</td>
      <td>documentary</td>
    </tr>
    <tr>
      <th>5</th>
      <td>scat06</td>
      <td>electric music</td>
    </tr>
    <tr>
      <th>6</th>
      <td>scat07</td>
      <td>drama</td>
    </tr>
    <tr>
      <th>7</th>
      <td>scat08</td>
      <td>indie rock</td>
    </tr>
    <tr>
      <th>8</th>
      <td>scat09</td>
      <td>wearables</td>
    </tr>
    <tr>
      <th>9</th>
      <td>scat010</td>
      <td>nonfiction</td>
    </tr>
  </tbody>
</table>
</div>



#### **Export Dataframes as CSV Files**


```python
category_df.to_csv('category.csv', index=False)
subcategory_df.to_csv('subcategory.csv', index=False)
```

### **Clean the Campaign Data**

To do so, you review the business requirements. For the campaign_df DataFrame, you need the following columns:
- The "cf_id" column from the crowdfunding_info worksheet.
- A column named "contact_id" that contains the unique "contact_id" values from the contact_info worksheet.
- The "company_name" column from the crowdfunding_info worksheet.
- The "blurb" column from the crowdfunding_info worksheet, renamed as the "description" column.
- The "goal" column from the crowdfunding_info worksheet, converted to a floating-point data type.
- The "pledged" column from the crowdfunding_info worksheet, converted to a floating-point data type.
- The "outcome" column from the crowdfunding_info worksheet.
- The "backers_count" column from the crowdfunding_info worksheet.
- The "country" column from the crowdfunding_info worksheet.
- The "currency" column from the crowdfunding_info worksheet.
- The "launched_at" column from the crowdfunding_info worksheet, renamed as the "launch_date" column and converted to a datetime format.
- The "deadline" column from the crowdfunding_info worksheet, renamed as the "end_date" column and converted to a datetime format.
- A "category_id" column that contains numbers matching the unique "category_id" values from the category_df DataFrame.
- A "subcategory_id" column that contains numbers matching the unique "subcategory_id" values from the subcategory_df DataFrame.


```python
# Create a copy of the crowdfunding_info_df DataFrame name campaign_df.
campaign_df = crowdfunding_info_df.copy()
campaign_df.head()
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>blurb</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launched_at</th>
      <th>deadline</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
      <th>category</th>
      <th>subcategory</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100</td>
      <td>0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>1581573600</td>
      <td>1614578400</td>
      <td>False</td>
      <td>False</td>
      <td>food/food trucks</td>
      <td>food</td>
      <td>food trucks</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400</td>
      <td>14560</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>1611554400</td>
      <td>1621918800</td>
      <td>False</td>
      <td>True</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400</td>
      <td>142523</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>1608184800</td>
      <td>1640844000</td>
      <td>False</td>
      <td>False</td>
      <td>technology/web</td>
      <td>technology</td>
      <td>web</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200</td>
      <td>2477</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>1634792400</td>
      <td>1642399200</td>
      <td>False</td>
      <td>False</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600</td>
      <td>5265</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>1608530400</td>
      <td>1629694800</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
    </tr>
  </tbody>
</table>
</div>




```python
# rename the "blurb", "launched_at", and "deadline" columns
campaign_df = campaign_df.rename(columns={'launched_at': 'launch_date', 'blurb':'description', 'deadline': 'end_date'})
```


```python
# Convert the goal and pledged columns to a `float` data type.
campaign_df[["goal","pledged"]] = campaign_df[["goal","pledged"]].astype(float)
campaign_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>description</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launch_date</th>
      <th>end_date</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
      <th>category</th>
      <th>subcategory</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>1581573600</td>
      <td>1614578400</td>
      <td>False</td>
      <td>False</td>
      <td>food/food trucks</td>
      <td>food</td>
      <td>food trucks</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400.0</td>
      <td>14560.0</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>1611554400</td>
      <td>1621918800</td>
      <td>False</td>
      <td>True</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400.0</td>
      <td>142523.0</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>1608184800</td>
      <td>1640844000</td>
      <td>False</td>
      <td>False</td>
      <td>technology/web</td>
      <td>technology</td>
      <td>web</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200.0</td>
      <td>2477.0</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>1634792400</td>
      <td>1642399200</td>
      <td>False</td>
      <td>False</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600.0</td>
      <td>5265.0</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>1608530400</td>
      <td>1629694800</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Format the launched_date and end_date columns to datetime format.

campaign_df["launch_date"] = pd.to_datetime(campaign_df["launch_date"], unit='s').dt.strftime('%Y-%m-%d') 
campaign_df["end_date"] = pd.to_datetime(campaign_df["end_date"], unit='s').dt.strftime('%Y-%m-%d')
campaign_df.info()


```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1000 entries, 0 to 999
    Data columns (total 16 columns):
     #   Column                   Non-Null Count  Dtype  
    ---  ------                   --------------  -----  
     0   cf_id                    1000 non-null   int64  
     1   company_name             1000 non-null   object 
     2   description              1000 non-null   object 
     3   goal                     1000 non-null   float64
     4   pledged                  1000 non-null   float64
     5   outcome                  1000 non-null   object 
     6   backers_count            1000 non-null   int64  
     7   country                  1000 non-null   object 
     8   currency                 1000 non-null   object 
     9   launch_date              1000 non-null   object 
     10  end_date                 1000 non-null   object 
     11  staff_pick               1000 non-null   bool   
     12  spotlight                1000 non-null   bool   
     13  category & sub-category  1000 non-null   object 
     14  category                 1000 non-null   object 
     15  subcategory              1000 non-null   object 
    dtypes: bool(2), float64(2), int64(2), object(10)
    memory usage: 111.5+ KB



```python
campaign_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1000 entries, 0 to 999
    Data columns (total 16 columns):
     #   Column                   Non-Null Count  Dtype  
    ---  ------                   --------------  -----  
     0   cf_id                    1000 non-null   int64  
     1   company_name             1000 non-null   object 
     2   description              1000 non-null   object 
     3   goal                     1000 non-null   float64
     4   pledged                  1000 non-null   float64
     5   outcome                  1000 non-null   object 
     6   backers_count            1000 non-null   int64  
     7   country                  1000 non-null   object 
     8   currency                 1000 non-null   object 
     9   launch_date              1000 non-null   object 
     10  end_date                 1000 non-null   object 
     11  staff_pick               1000 non-null   bool   
     12  spotlight                1000 non-null   bool   
     13  category & sub-category  1000 non-null   object 
     14  category                 1000 non-null   object 
     15  subcategory              1000 non-null   object 
    dtypes: bool(2), float64(2), int64(2), object(10)
    memory usage: 111.5+ KB


### **Transform the Campaign Data**


```python
# Merge the campaign_df with the category_df on the "category" column and 
# the subcategory_df on the "subcategory" column.
campaign_merged_df = campaign_df.merge(category_df, on='category', how='left').merge(subcategory_df, on='subcategory', how='left')
campaign_merged_df.tail(10)
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>description</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launch_date</th>
      <th>end_date</th>
      <th>staff_pick</th>
      <th>spotlight</th>
      <th>category &amp; sub-category</th>
      <th>category</th>
      <th>subcategory</th>
      <th>category_id</th>
      <th>subcategory_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>990</th>
      <td>1561</td>
      <td>Ortiz-Roberts</td>
      <td>Devolved foreground customer loyalty</td>
      <td>7800.0</td>
      <td>6839.0</td>
      <td>failed</td>
      <td>64</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-06-09</td>
      <td>2021-06-18</td>
      <td>False</td>
      <td>True</td>
      <td>film &amp; video/drama</td>
      <td>film &amp; video</td>
      <td>drama</td>
      <td>cat05</td>
      <td>scat07</td>
    </tr>
    <tr>
      <th>991</th>
      <td>2632</td>
      <td>Ramirez LLC</td>
      <td>Reduced reciprocal focus group</td>
      <td>9800.0</td>
      <td>11091.0</td>
      <td>successful</td>
      <td>241</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-12-09</td>
      <td>2021-05-26</td>
      <td>False</td>
      <td>True</td>
      <td>music/rock</td>
      <td>music</td>
      <td>rock</td>
      <td>cat02</td>
      <td>scat02</td>
    </tr>
    <tr>
      <th>992</th>
      <td>439</td>
      <td>Morrow Inc</td>
      <td>Networked global migration</td>
      <td>3100.0</td>
      <td>13223.0</td>
      <td>successful</td>
      <td>132</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-06-14</td>
      <td>2021-02-09</td>
      <td>False</td>
      <td>True</td>
      <td>film &amp; video/drama</td>
      <td>film &amp; video</td>
      <td>drama</td>
      <td>cat05</td>
      <td>scat07</td>
    </tr>
    <tr>
      <th>993</th>
      <td>461</td>
      <td>Erickson-Rogers</td>
      <td>De-engineered even-keeled definition</td>
      <td>9800.0</td>
      <td>7608.0</td>
      <td>canceled</td>
      <td>75</td>
      <td>IT</td>
      <td>EUR</td>
      <td>2021-07-03</td>
      <td>2021-07-08</td>
      <td>False</td>
      <td>True</td>
      <td>photography/photography books</td>
      <td>photography</td>
      <td>photography books</td>
      <td>cat08</td>
      <td>scat015</td>
    </tr>
    <tr>
      <th>994</th>
      <td>1419</td>
      <td>Leach, Rich and Price</td>
      <td>Implemented bi-directional flexibility</td>
      <td>141100.0</td>
      <td>74073.0</td>
      <td>failed</td>
      <td>842</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-11-15</td>
      <td>2021-12-07</td>
      <td>False</td>
      <td>True</td>
      <td>publishing/translations</td>
      <td>publishing</td>
      <td>translations</td>
      <td>cat06</td>
      <td>scat019</td>
    </tr>
    <tr>
      <th>995</th>
      <td>2986</td>
      <td>Manning-Hamilton</td>
      <td>Vision-oriented scalable definition</td>
      <td>97300.0</td>
      <td>153216.0</td>
      <td>successful</td>
      <td>2043</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-12-29</td>
      <td>2021-05-30</td>
      <td>False</td>
      <td>True</td>
      <td>food/food trucks</td>
      <td>food</td>
      <td>food trucks</td>
      <td>cat01</td>
      <td>scat01</td>
    </tr>
    <tr>
      <th>996</th>
      <td>2031</td>
      <td>Butler LLC</td>
      <td>Future-proofed upward-trending migration</td>
      <td>6600.0</td>
      <td>4814.0</td>
      <td>failed</td>
      <td>112</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-10-15</td>
      <td>2021-11-30</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
      <td>cat04</td>
      <td>scat04</td>
    </tr>
    <tr>
      <th>997</th>
      <td>1627</td>
      <td>Ball LLC</td>
      <td>Right-sized full-range throughput</td>
      <td>7600.0</td>
      <td>4603.0</td>
      <td>canceled</td>
      <td>139</td>
      <td>IT</td>
      <td>EUR</td>
      <td>2021-11-06</td>
      <td>2021-12-10</td>
      <td>False</td>
      <td>False</td>
      <td>theater/plays</td>
      <td>theater</td>
      <td>plays</td>
      <td>cat04</td>
      <td>scat04</td>
    </tr>
    <tr>
      <th>998</th>
      <td>2175</td>
      <td>Taylor, Santiago and Flores</td>
      <td>Polarized composite customer loyalty</td>
      <td>66600.0</td>
      <td>37823.0</td>
      <td>failed</td>
      <td>374</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-10-08</td>
      <td>2021-04-11</td>
      <td>False</td>
      <td>True</td>
      <td>music/indie rock</td>
      <td>music</td>
      <td>indie rock</td>
      <td>cat02</td>
      <td>scat08</td>
    </tr>
    <tr>
      <th>999</th>
      <td>1788</td>
      <td>Hernandez, Norton and Kelley</td>
      <td>Expanded eco-centric policy</td>
      <td>111100.0</td>
      <td>62819.0</td>
      <td>canceled</td>
      <td>1122</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-12-30</td>
      <td>2021-08-18</td>
      <td>False</td>
      <td>False</td>
      <td>food/food trucks</td>
      <td>food</td>
      <td>food trucks</td>
      <td>cat01</td>
      <td>scat01</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Drop unwanted columns.
campaign_cleaned = campaign_merged_df.drop(['staff_pick', 'spotlight', 'category & sub-category','category', 'subcategory'], axis=1)
campaign_cleaned.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>description</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launch_date</th>
      <th>end_date</th>
      <th>category_id</th>
      <th>subcategory_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>2020-02-13</td>
      <td>2021-03-01</td>
      <td>cat01</td>
      <td>scat01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400.0</td>
      <td>14560.0</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-01-25</td>
      <td>2021-05-25</td>
      <td>cat02</td>
      <td>scat02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400.0</td>
      <td>142523.0</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>2020-12-17</td>
      <td>2021-12-30</td>
      <td>cat03</td>
      <td>scat03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200.0</td>
      <td>2477.0</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-10-21</td>
      <td>2022-01-17</td>
      <td>cat02</td>
      <td>scat02</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600.0</td>
      <td>5265.0</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-12-21</td>
      <td>2021-08-23</td>
      <td>cat04</td>
      <td>scat04</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Show the contact_info_df DataFrame.
contact_info_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_info</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{"contact_id": 4661, "name": "Cecilia Velasco", "email": "cecilia.velasco@rodrigues.fr"}</td>
    </tr>
    <tr>
      <th>1</th>
      <td>{"contact_id": 3765, "name": "Mariana Ellis", "email": "mariana.ellis@rossi.org"}</td>
    </tr>
    <tr>
      <th>2</th>
      <td>{"contact_id": 4187, "name": "Sofie Woods", "email": "sofie.woods@riviere.com"}</td>
    </tr>
    <tr>
      <th>3</th>
      <td>{"contact_id": 4941, "name": "Jeanette Iannotti", "email": "jeanette.iannotti@yahoo.com"}</td>
    </tr>
    <tr>
      <th>4</th>
      <td>{"contact_id": 2199, "name": "Samuel Sorgatz", "email": "samuel.sorgatz@gmail.com"}</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Retrieve the data from the "contact_info" column.
contact_info_list = contact_info_df.contact_info.to_list()
contact_info_list
```

    ['{"contact_id": 4661, "name": "Cecilia Velasco", "email": "cecilia.velasco@rodrigues.fr"}',
     '{"contact_id": 3765, "name": "Mariana Ellis", "email": "mariana.ellis@rossi.org"}',
     '{"contact_id": 4187, "name": "Sofie Woods", "email": "sofie.woods@riviere.com"}',
     ,
     '{"contact_id": 2384, "name": "Irene Anichini", "email": "irene.anichini@yahoo.com"}',
     '{"contact_id": 3074, "name": "Alicia Cainero", "email": "alicia.cainero@yahoo.com"}',
     '{"contact_id": 2031, "name": "Tracey Laboy", "email": "tracey.laboy@bell.biz"}',
     '{"contact_id": 5873, "name": "Donald Reynaud", "email": "donald.reynaud@green.edu.au"}',
     '{"contact_id": 5501, "name": "Venancio Stadelmann", "email": "venancio.stadelmann@gmx.de"}',
     '{"contact_id": 3489, "name": "Seth Olivier", "email": "seth.olivier@yahoo.com"}',
     '{"contact_id": 4210, "name": "Dennis Boaga", "email": "dennis.boaga@gmx.de"}',
     '{"contact_id": 6151, "name": "Pedro Wilson", "email": "pedro.wilson@outlook.com"}',
     '{"contact_id": 6047, "name": "Roland Weihmann", "email": "roland.weihmann@gmail.com"}',
     '{"contact_id": 5445, "name": "Hector Morrison", "email": "hector.morrison@web.de"}',
     '{"contact_id": 5493, "name": "Evangelos Peters", "email": "evangelos.peters@sanchez.net"}',
     '{"contact_id": 6036, "name": "Gerardo Hamilton", "email": "gerardo.hamilton@libero.it"}',
     '{"contact_id": 2368, "name": "Meta Cuzzocrea", "email": "meta.cuzzocrea@howard-jensen.org"}',
     '{"contact_id": 1501, "name": "Alma Raymond", "email": "alma.raymond@tin.it"}',
     '{"contact_id": 4351, "name": "Kathleen Solimena", "email": "kathleen.solimena@wallace.biz"}',
     '{"contact_id": 3096, "name": "Zacharie Cordier", "email": "zacharie.cordier@gmail.com"}',
     '{"contact_id": 6162, "name": "Riza Techer", "email": "riza.techer@yahoo.com"}',
     '{"contact_id": 1433, "name": "Enrique Guillen", "email": "enrique.guillen@hotmail.co.uk"}',
     '{"contact_id": 2720, "name": "Tristan Weeks", "email": "tristan.weeks@pena.com"}',
     '{"contact_id": 5251, "name": "Natalie Lerma", "email": "natalie.lerma@boucher.com"}',
     '{"contact_id": 1797, "name": "Rodney Hamon", "email": "rodney.hamon@hotmail.com"}',
     '{"contact_id": 1656, "name": "Adalberto Lombardo", "email": "adalberto.lombardo@sfr.fr"}',
     '{"contact_id": 1346, "name": "Florencia Querini", "email": "florencia.querini@gmail.com"}',
     '{"contact_id": 2989, "name": "Liam Rolland", "email": "liam.rolland@yahoo.fr"}',
     '{"contact_id": 5629, "name": "Ian Kelly", "email": "ian.kelly@reeves.com.au"}',
     '{"contact_id": 3456, "name": "Mehdi Todd", "email": "mehdi.todd@web.de"}',
     '{"contact_id": 3229, "name": "Rembrandt Liguori", "email": "rembrandt.liguori@davis.org"}',
     '{"contact_id": 2277, "name": "Irmi Schweitzer", "email": "irmi.schweitzer@ozuna.com"}',
     '{"contact_id": 1276, "name": "Gioffre Stein", "email": "gioffre.stein@hotmail.com.au"}',
     '{"contact_id": 3694, "name": "Frida Villarreal", "email": "frida.villarreal@hotmail.de"}',
     '{"contact_id": 2260, "name": "Steffen Mangold", "email": "steffen.mangold@paul.net"}',
     '{"contact_id": 5374, "name": "Elias Marin", "email": "elias.marin@herve.net"}',
     '{"contact_id": 4420, "name": "Roman Gonzalez", "email": "roman.gonzalez@anderson.net"}',
     '{"contact_id": 3849, "name": "Reina Middleton", "email": "reina.middleton@hotmail.com"}',
     '{"contact_id": 1638, "name": "Sandro Moran", "email": "sandro.moran@hotmail.de"}',
     '{"contact_id": 5230, "name": "Gebhard Thanel", "email": "gebhard.thanel@gmail.com"}',
     '{"contact_id": 1763, "name": "Antonino Bolander", "email": "antonino.bolander@gmail.com"}',
     '{"contact_id": 4323, "name": "Karl Trevisani", "email": "karl.trevisani@anguissola.it"}',
     '{"contact_id": 5256, "name": "Silvester Brookes", "email": "silvester.brookes@franco.org"}',
     '{"contact_id": 4836, "name": "Octavio Pratt", "email": "octavio.pratt@web.de"}',
     '{"contact_id": 5981, "name": "Rebecca Greer", "email": "rebecca.greer@hotmail.com"}',
     '{"contact_id": 1463, "name": "Pierina Gaggini", "email": "pierina.gaggini@free.fr"}',
     '{"contact_id": 4577, "name": "Robin Schleich", "email": "robin.schleich@rogers-barrera.biz"}',
     '{"contact_id": 4951, "name": "Sergio Abbott", "email": "sergio.abbott@moore.net.au"}',
     '{"contact_id": 3567, "name": "Gilberto Evans", "email": "gilberto.evans@salcedo-archuleta.net"}',
     '{"contact_id": 5475, "name": "James Elliott", "email": "james.elliott@gmail.com"}',
     '{"contact_id": 2114, "name": "Timoteo Bolnbach", "email": "timoteo.bolnbach@laboratorios.com"}',
     '{"contact_id": 5216, "name": "Tonino Stanley", "email": "tonino.stanley@hotmail.com"}',
     '{"contact_id": 1665, "name": "Margaretha Murialdo", "email": "margaretha.murialdo@meunier.com"}',
     '{"contact_id": 6051, "name": "Bianca Drubin", "email": "bianca.drubin@libero.it"}',
     '{"contact_id": 3983, "name": "Kaylee Hoffmann", "email": "kaylee.hoffmann@poulain.com"}',
     '{"contact_id": 2966, "name": "Teresa Vecellio", "email": "teresa.vecellio@yahoo.com"}',
     '{"contact_id": 4478, "name": "Emine Tacchini", "email": "emine.tacchini@dbmail.com"}',
     '{"contact_id": 4238, "name": "Dawn Cortez", "email": "dawn.cortez@gmail.com"}',
     '{"contact_id": 4404, "name": "Emily Cundari", "email": "emily.cundari@clark.com.au"}',
     '{"contact_id": 5666, "name": "Eleanor Hall", "email": "eleanor.hall@hotmail.com"}',
     '{"contact_id": 3691, "name": "Monique Delgadillo", "email": "monique.delgadillo@hotmail.co.uk"}',
     '{"contact_id": 2703, "name": "Arnaude Warner", "email": "arnaude.warner@gmail.com"}',
     '{"contact_id": 4253, "name": "Jason Howell", "email": "jason.howell@gmx.de"}',
     '{"contact_id": 5652, "name": "Nicole Mccullough", "email": "nicole.mccullough@hall.com"}',
     '{"contact_id": 3279, "name": "Rosmarie Esquibel", "email": "rosmarie.esquibel@bouygtel.fr"}',
     '{"contact_id": 3233, "name": "Leonor Ferreira", "email": "leonor.ferreira@gmail.com"}',
     '{"contact_id": 3017, "name": "Brett Jones", "email": "brett.jones@alice.it"}',
     '{"contact_id": 3482, "name": "Nino Bien", "email": "nino.bien@gmail.com"}',
     '{"contact_id": 2166, "name": "Jemma Tate", "email": "jemma.tate@hotmail.de"}',
     '{"contact_id": 4146, "name": "Denis Rogers", "email": "denis.rogers@poste.it"}',
     '{"contact_id": 2651, "name": "Vanesa Khan", "email": "vanesa.khan@voila.fr"}',
     '{"contact_id": 1409, "name": "Victoria Ojeda", "email": "victoria.ojeda@doehn.com"}',
     '{"contact_id": 3717, "name": "Eugenio Martin", "email": "eugenio.martin@yahoo.de"}',
     '{"contact_id": 3963, "name": "Nancy Hayes", "email": "nancy.hayes@hotmail.com"}',
     '{"contact_id": 2152, "name": "Lara Jacob", "email": "lara.jacob@buckley.org"}',
     '{"contact_id": 2974, "name": "Gino Hernandez", "email": "gino.hernandez@covarrubias.com"}',
     '{"contact_id": 1268, "name": "Vicente Tijerina", "email": "vicente.tijerina@montanariello.com"}',
     '{"contact_id": 1822, "name": "Michele Brambilla", "email": "michele.brambilla@yahoo.com"}',
     '{"contact_id": 5066, "name": "Dana Ritacca", "email": "dana.ritacca@gmail.com"}',
     '{"contact_id": 5075, "name": "Mitchell Bachmann", "email": "mitchell.bachmann@suarez-cruz.com"}',
     '{"contact_id": 3855, "name": "Vincenza Key", "email": "vincenza.key@adams.com"}',
     '{"contact_id": 5923, "name": "Salvador Gaillard", "email": "salvador.gaillard@yahoo.de"}',
     '{"contact_id": 1867, "name": "Diane Rogner", "email": "diane.rogner@kelly.co.uk"}',
     '{"contact_id": 4376, "name": "Josette Laine", "email": "josette.laine@saracino-parisi.net"}',
     '{"contact_id": 2785, "name": "Courtney Fiebig", "email": "courtney.fiebig@doerschner.com"}',
     '{"contact_id": 5884, "name": "Hugues Oliver", "email": "hugues.oliver@stiffel.com"}',
     '{"contact_id": 2441, "name": "Espartaco Willis", "email": "espartaco.willis@hotmail.com"}',
     '{"contact_id": 1243, "name": "Flavia Yoder", "email": "flavia.yoder@hotmail.de"}',
     '{"contact_id": 3411, "name": "Casey Flores", "email": "casey.flores@baggio.org"}',
     '{"contact_id": 2467, "name": "Calogero Cross", "email": "calogero.cross@mercantini.it"}',
     '{"contact_id": 1850, "name": "Charles Begum", "email": "charles.begum@laboratorios.info"}',
     '{"contact_id": 2604, "name": "Adina Pollard", "email": "adina.pollard@outlook.com"}',
     '{"contact_id": 1693, "name": "Natasha Lara", "email": "natasha.lara@outlook.com"}',
     '{"contact_id": 2969, "name": "Benita Pottier", "email": "benita.pottier@sfr.fr"}',
     '{"contact_id": 1544, "name": "Elizabeth Valdivia", "email": "elizabeth.valdivia@gmail.com"}',
     '{"contact_id": 4149, "name": "Mattia Huet", "email": "mattia.huet@aol.de"}',
     '{"contact_id": 3463, "name": "Geza Howard", "email": "geza.howard@voila.fr"}',
     '{"contact_id": 2939, "name": "Lauren Guillaume", "email": "lauren.guillaume@sorgatz.org"}',
     '{"contact_id": 2202, "name": "Joanna Baxter", "email": "joanna.baxter@bruce-wright.com"}',
     '{"contact_id": 1728, "name": "Tracy Metz", "email": "tracy.metz@bryant.info"}',
     '{"contact_id": 5362, "name": "Birgit Pena", "email": "birgit.pena@armellini.it"}',
     '{"contact_id": 4840, "name": "Elvira Papafava", "email": "elvira.papafava@ifrance.com"}',
     '{"contact_id": 2955, "name": "Ernest Delorme", "email": "ernest.delorme@dowerg.de"}',
     '{"contact_id": 5354, "name": "Christy Grossi", "email": "christy.grossi@alarcon-tafoya.com"}',
     '{"contact_id": 3847, "name": "Hermelinda Olmos", "email": "hermelinda.olmos@portillo.com"}',
     '{"contact_id": 3025, "name": "Herbert Fraser", "email": "herbert.fraser@holloway.org.au"}',
     '{"contact_id": 1615, "name": "Giancarlo Heydrich", "email": "giancarlo.heydrich@hotmail.com"}',
     '{"contact_id": 5154, "name": "Constance Maldonado", "email": "constance.maldonado@spiess.net"}',
     '{"contact_id": 5005, "name": "Giampiero Carlier", "email": "giampiero.carlier@hotmail.com"}',
     '{"contact_id": 1349, "name": "Carlo Jacquot", "email": "carlo.jacquot@hotmail.com"}',
     '{"contact_id": 3670, "name": "Gabriel Robles", "email": "gabriel.robles@yahoo.com"}',
     '{"contact_id": 2912, "name": "Gerd Gunpf", "email": "gerd.gunpf@gmail.com"}',
     '{"contact_id": 4336, "name": "Ashley Hellwig", "email": "ashley.hellwig@libero.it"}',
     '{"contact_id": 5576, "name": "Patrick Rosas", "email": "patrick.rosas@yahoo.com"}',
     '{"contact_id": 2736, "name": "Lilla Leonard", "email": "lilla.leonard@jungfer.com"}',
     '{"contact_id": 3321, "name": "Tony Nicolas", "email": "tony.nicolas@morrocco-seddio.net"}',
     '{"contact_id": 3353, "name": "Allegra Benedetti", "email": "allegra.benedetti@miller.com"}',
     '{"contact_id": 2336, "name": "Camille Tafuri", "email": "camille.tafuri@aol.de"}',
     '{"contact_id": 4369, "name": "Juan Wheeler", "email": "juan.wheeler@gilmore.net"}',
     '{"contact_id": 4464, "name": "Leopoldo Johnson", "email": "leopoldo.johnson@hotmail.com.au"}',
     '{"contact_id": 5626, "name": "Dietmar Grenier", "email": "dietmar.grenier@tele2.it"}',
     '{"contact_id": 1669, "name": "Leonid Scholl", "email": "leonid.scholl@collin.com"}',
     '{"contact_id": 5544, "name": "Olivie Contarini", "email": "olivie.contarini@marshall-wright.biz"}',
     '{"contact_id": 6078, "name": "Jeffery Lacroix", "email": "jeffery.lacroix@medina.org"}',
     '{"contact_id": 2580, "name": "Edeltraud Chavez", "email": "edeltraud.chavez@hotmail.com.au"}',
     '{"contact_id": 3592, "name": "Mesut Morvan", "email": "mesut.morvan@gmail.com"}',
     '{"contact_id": 4914, "name": "Gregory Jackson", "email": "gregory.jackson@ortiz.com"}',
     '{"contact_id": 3848, "name": "Nicola Rossi", "email": "nicola.rossi@berry.org"}',
     '{"contact_id": 2619, "name": "Eddie Collazo", "email": "eddie.collazo@gmail.com"}',
     '{"contact_id": 4605, "name": "Philippe Gardner", "email": "philippe.gardner@howells-jones.net"}',
     '{"contact_id": 6147, "name": "Roy Fox", "email": "roy.fox@tejeda.com"}',
     '{"contact_id": 1383, "name": "Heiderose Garcia", "email": "heiderose.garcia@noos.fr"}',
     '{"contact_id": 1757, "name": "Claudine Rowley", "email": "claudine.rowley@industrias.com"}',
     '{"contact_id": 4390, "name": "Astrid Roht", "email": "astrid.roht@fastwebnet.it"}',
     '{"contact_id": 4165, "name": "Auguste Burnett", "email": "auguste.burnett@yahoo.fr"}',
     '{"contact_id": 4529, "name": "Pellegrino Allen", "email": "pellegrino.allen@gmail.com"}',
     '{"contact_id": 6084, "name": "Angela Bowers", "email": "angela.bowers@laboratorios.com"}',
     '{"contact_id": 2916, "name": "Michael Descamps", "email": "michael.descamps@gmail.com"}',
     '{"contact_id": 2005, "name": "Heidelore Kennedy", "email": "heidelore.kennedy@guinizzelli.org"}',
     '{"contact_id": 2261, "name": "Lucrezia Koch", "email": "lucrezia.koch@petit.com"}',
     '{"contact_id": 5342, "name": "Ivan Accardo", "email": "ivan.accardo@web.de"}',
     '{"contact_id": 4087, "name": "Karen Avogadro", "email": "karen.avogadro@yahoo.com"}',
     '{"contact_id": 2761, "name": "Laetitia Gallet", "email": "laetitia.gallet@aubry.org"}',
     '{"contact_id": 2908, "name": "Amico Gosselin", "email": "amico.gosselin@shaw.info"}',
     '{"contact_id": 2155, "name": "Ilaria Bowen", "email": "ilaria.bowen@hotmail.it"}',
     '{"contact_id": 5642, "name": "Marlen Coardi", "email": "marlen.coardi@colletti.eu"}',
     '{"contact_id": 6217, "name": "Rafael Alexander", "email": "rafael.alexander@jenkins.com"}',
     '{"contact_id": 4222, "name": "Herma Gabbana", "email": "herma.gabbana@hotmail.com"}',
     '{"contact_id": 4798, "name": "Zachary Vespucci", "email": "zachary.vespucci@industrias.com"}',
     '{"contact_id": 5660, "name": "Marisol Richard", "email": "marisol.richard@gmail.com"}',
     '{"contact_id": 5000, "name": "Joseph Glover", "email": "joseph.glover@laposte.net"}',
     '{"contact_id": 2809, "name": "Lauretta Neal", "email": "lauretta.neal@hotmail.de"}',
     '{"contact_id": 3349, "name": "Simone Sauvage", "email": "simone.sauvage@yahoo.com"}',
     '{"contact_id": 3647, "name": "Ramona Brooks", "email": "ramona.brooks@kensy.de"}',
     '{"contact_id": 5745, "name": "Gioele Schwital", "email": "gioele.schwital@moody.com"}',
     '{"contact_id": 2583, "name": "Elena Jimenez", "email": "elena.jimenez@yahoo.com.au"}',
     '{"contact_id": 5441, "name": "Pam Vargas", "email": "pam.vargas@guillot.net"}',
     '{"contact_id": 2090, "name": "Alison Morales", "email": "alison.morales@lewis.com"}',
     '{"contact_id": 3386, "name": "Marc Aumann", "email": "marc.aumann@holland.com"}',
     '{"contact_id": 1816, "name": "Carla Butler", "email": "carla.butler@yahoo.com"}',
     '{"contact_id": 4718, "name": "Dionigi Bruce", "email": "dionigi.bruce@industrias.com"}',
     '{"contact_id": 1677, "name": "Frank Henry", "email": "frank.henry@yahoo.com"}',
     '{"contact_id": 4218, "name": "Brent Pons", "email": "brent.pons@proyectos.net"}',
     '{"contact_id": 4548, "name": "Patricia Liebelt", "email": "patricia.liebelt@baca.org"}',
    
     '{"contact_id": 1791, "name": "Ludovica Arellano", "email": "ludovica.arellano@morandi-argento.com"}',
     '{"contact_id": 3314, "name": "Catherine Gray", "email": "catherine.gray@hotmail.com"}',
     '{"contact_id": 5852, "name": "Franco Fioravanti", "email": "franco.fioravanti@barry-gill.com.au"}',
     '{"contact_id": 4618, "name": "Heather Zimmer", "email": "heather.zimmer@bluemel.de"}',
     '{"contact_id": 2618, "name": "Hortense Taylor", "email": "hortense.taylor@chambers.biz"}',
     '{"contact_id": 4967, "name": "Livia Ernst", "email": "livia.ernst@colas.fr"}',
     '{"contact_id": 4594, "name": "Toby Etzler", "email": "toby.etzler@industrias.com"}',
     '{"contact_id": 2080, "name": "Ennio Leoncavallo", "email": "ennio.leoncavallo@sanchez.org"}',
     '{"contact_id": 2793, "name": "Philippine Vaca", "email": "philippine.vaca@hotmail.com"}',
     '{"contact_id": 4890, "name": "Rhonda Phillips", "email": "rhonda.phillips@yahoo.com"}',
     '{"contact_id": 3528, "name": "Alphonse Nitto", "email": "alphonse.nitto@voila.fr"}',
     '{"contact_id": 5810, "name": "Karina Arreola", "email": "karina.arreola@yahoo.com.au"}',
     '{"contact_id": 5720, "name": "Denise Wood", "email": "denise.wood@mantegna.com"}',
     '{"contact_id": 4593, "name": "Burghard Rosenow", "email": "burghard.rosenow@hotmail.com"}',
     '{"contact_id": 3186, "name": "Friedl Powell", "email": "friedl.powell@laposte.net"}',
     '{"contact_id": 5136, "name": "Benjamin Schultz", "email": "benjamin.schultz@rivero-davila.com"}',
     '{"contact_id": 2646, "name": "Rachel Ulibarri", "email": "rachel.ulibarri@schueler.com"}',
     '{"contact_id": 3460, "name": "Silvano Blanchet", "email": "silvano.blanchet@yahoo.com"}',
     '{"contact_id": 4345, "name": "Kurt Sauer", "email": "kurt.sauer@almanza.biz"}',
     '{"contact_id": 2690, "name": "Nayeli Gehringer", "email": "nayeli.gehringer@hotmail.com"}',
     '{"contact_id": 4025, "name": "Imelda Guerin", "email": "imelda.guerin@stumpf.net"}',
     '{"contact_id": 4524, "name": "Zbigniew Holmes", "email": "zbigniew.holmes@stiffel.net"}',
     '{"contact_id": 3978, "name": "Adela Alfonsi", "email": "adela.alfonsi@despacho.com"}',
     '{"contact_id": 3910, "name": "Hilary Clark", "email": "hilary.clark@libero.it"}',
     '{"contact_id": 5825, "name": "Kornelius Molina", "email": "kornelius.molina@heuser.com"}',
     '{"contact_id": 3564, "name": "Christian Anderson", "email": "christian.anderson@tim.it"}',
     '{"contact_id": 2396, "name": "Francisco Dorsey", "email": "francisco.dorsey@tiscali.fr"}',
     '{"contact_id": 2140, "name": "Walli Albert", "email": "walli.albert@dussen.com"}',
     '{"contact_id": 3079, "name": "Siegrun Mota", "email": "siegrun.mota@matthews-montoya.org.au"}',
     '{"contact_id": 2017, "name": "Alberico Herve", "email": "alberico.herve@beer.net"}',
     '{"contact_id": 5444, "name": "Serafina Bonnin", "email": "serafina.bonnin@mendes.com"}',
     '{"contact_id": 2216, "name": "Marcelle Bender", "email": "marcelle.bender@edwards-robinson.info"}',
     '{"contact_id": 1241, "name": "Hartmuth Robertson", "email": "hartmuth.robertson@thomas.edu.au"}',
     '{"contact_id": 1484, "name": "Herlinde George", "email": "herlinde.george@free.fr"}',
     '{"contact_id": 3475, "name": "Pamela Payne", "email": "pamela.payne@yahoo.com"}',
     '{"contact_id": 4333, "name": "Gastone Beyer", "email": "gastone.beyer@stewart-sanchez.edu"}',
    
     '{"contact_id": 1890, "name": "Brigitte Stiebitz", "email": "brigitte.stiebitz@gmail.com"}',
     '{"contact_id": 1304, "name": "June Sinisi", "email": "june.sinisi@manzoni-giannini.it"}',
     '{"contact_id": 3799, "name": "Carl Macias", "email": "carl.macias@yahoo.com"}',
     '{"contact_id": 2749, "name": "Veronica Trapp", "email": "veronica.trapp@tiscali.it"}',
     '{"contact_id": 2028, "name": "Frances Garnier", "email": "frances.garnier@aporti-guidotti.org"}',
     '{"contact_id": 2383, "name": "Terri Iannelli", "email": "terri.iannelli@hotmail.com"}',
     '{"contact_id": 5721, "name": "Odette Moore", "email": "odette.moore@hall.edu"}',
     '{"contact_id": 1394, "name": "Mercedes Trujillo", "email": "mercedes.trujillo@aol.de"}',
     '{"contact_id": 3451, "name": "Babette Dumas", "email": "babette.dumas@brady.net"}',
     '{"contact_id": 5512, "name": "Jerry Santiago", "email": "jerry.santiago@soprano-ferragni.it"}',
     '{"contact_id": 3790, "name": "Eloy Renzi", "email": "eloy.renzi@gmail.com"}',
     '{"contact_id": 5784, "name": "Gelsomina Migliaccio", "email": "gelsomina.migliaccio@junk.com"}',
     '{"contact_id": 1498, "name": "Evangelista Pereira", "email": "evangelista.pereira@thompson-peterson.biz"}',
     '{"contact_id": 6073, "name": "Gareth Comolli", "email": "gareth.comolli@tiscali.fr"}',
     '{"contact_id": 4939, "name": "Michelangelo Hess", "email": "michelangelo.hess@bouygtel.fr"}']

```python
# Pop out the unique identification number using list slicing. 
print(contact_info_list[0][:15])
print(contact_info_list[0][15:19])
```
    {"contact_id": 
    4661

```python
# Pop out the unique identification number using list comprehension and slicing. 
print([x[15:19] for x in contact_info_list])
```

    ['4661', '3765', '4187', '4941', '2199', '5650', '5889', '4842', '3280', '5468', '3064', '4904', '1299', '5602', '5753', '4495', '4269', '2226', '1558', '2307', '2900', '5695', '5708', '1663', '3605', '4678', '2251', '6202', '3715', '4242', '4326', '5560', '4002', '3813', '5336', '4994', '1471', '4482', '3241', '3477', '2265', '5911', '2288', '4064', '1294', '5008', '3604', '3263', '5631', '2851', '3714', '1664', '5027', '3070', '4248', '2034', '4085', '3569', '3889', '3136', '2103', '2329', '3325', '3131', '4995', '3631', '5373', '3126', '2194', '2906', '2611', '2374', '3254', '3571', '2812', '3961', '3872', '4736', '5119', '5725', '4037', '2109', '3283', '6181', '3251', '3443', '2988', '1673', '2085', '1672', '4426', '3211', '3190', '2081', '3185', '5044', '1883', '2067', '4604', '3203', '5758', '5755', '5150', '4181', '3006', '4865', '2862', '6070', '5300', '3486', '5989', '2849', '1612', '3307', '5288', '6026', '2212', '4591', '2771', '5682', '5368', '3706', '4034', '3209', '2384', '3074', '2031', '5873', '5501', '3489', '4210', '6151', '6047', '5445', '5493', '6036', '2368', '1501', '4351', '3096', '6162', '1433', '2720', '5251', '1797', '1656', '1346', '2989', '5629', '3456', '3229', '2277', '1276', '3694', '2260', '5374', '4420', '3849', '1638', '5230', '1763', '4323', '5256', '4836', '5981', '1463', '4577', '4951', '3567', '5475', '2114', '5216', '1665', '6051', '3983', '2966', '4478', '4238', '4404', '5666', '3691', '2703', '4253', '5652', '3279', '3233', '3017', '3482', '2166', '4146', '2651', '1409', '3717', '3963', '2152', '2974', '1268', '1822', '5066', '5075', '3855', '5923', '1867', '4376', '2785', '5884', '2441', '1243', '3411', '2467', '1850', '2604', '1693', '2969', '1544', '4149', '3463', '2939', '2202', '1728', '5362', '4840', '2955', '5354', '3847', '3025', '1615', '5154', '5005', '1349', '3670', '2912', '4336', '5576', '2736', '3321', '3353', '2336', '4369', '4464', '5626', '1669', '5544', '6078', '2580', '3592', '4914', '3848', '2619', '4605', '6147', '1383', '1757', '4390', '4165', '4529', '6084', '2916', '2005', '2261', '5342', '4087', '2761', '2908', '2155', '5642', '6217', '4222', '4798', '5660', '5000', '2809', '3349', '3647', '5745', '2583', '5441', '2090', '3386', '1816', '4718', '1677', '4218', '4548', '1431', '2830', '4400', '5773', '4161', '2062', '3458', '3174', '5565', '1906', '5479', '4320', '4213', '3373', '4184', '5472', '5559', '5419', '4171', '1302', '3520', '6010', '4617', '1417', '4770', '4328', '5171', '4394', '1794', '3510', '2487', '2215', '4136', '3723', '5842', '3289', '1249', '5409', '2951', '3432', '2297', '6161', '4603', '5818', '4270', '6007', '4084', '1340', '5675', '2010', '6201', '1791', '3314', '5852', '4618', '2618', '4967', '4594', '2080', '2793', '4890', '3528', '5810', '5720', '4593', '3186', '5136', '2646', '3460', '4345', '2690', '4025', '4524', '3978', '3910', '5825', '3564', '2396', '2140', '3079', '2017', '5444', '2216', '1241', '1484', '3475', '4333', '5657', '2527', '5967', '5698', '3331', '3841', '6105', '3895', '1891', '1281', '4186', '2511', '4272', '3533', '3618', '2177', '4102', '6089', '4701', '5645', '4499', '3365', '2290', '1890', '1304', '3799', '2749', '2028', '2383', '5721', '1394', '3451', '5512', '2453', '6055', '5704', '3707', '2205', '5976', '3290', '4257', '4588', '4785', '3033', '2779', '2131', '6229', '3429', '1613', '4734', '4439', '1441', '5799', '1419', '3059', '3224', '3181', '6061', '4466', '6081', '2159', '3324', '3176', '5874', '5110', '5490', '1922', '4398', '4754', '3309', '1244', '2892', '2777', '4952', '5144', '5487', '1932', '3364', '5165', '3858', '4666', '2896', '4520', '5726', '5661', '4765', '3710', '4925', '1533', '5264', '4606', '2043', '3871', '6205', '3193', '1810', '4797', '1487', '3474', '5771', '6074', '1568', '4263', '5850', '5619', '1904', '5388', '2150', '2653', '2687', '3779', '1494', '4129', '3035', '2069', '5469', '3903', '4083', '4003', '3565', '1640', '4769', '3398', '1865', '5690', '3814', '2942', '5095', '2878', '3697', '4744', '2104', '4883', '4807', '1372', '3666', '2066', '1834', '5853', '2219', '4148', '4477', '1786', '3455', '5707', '5470', '3274', '4613', '4465', '1652', '2100', '4321', '4168', '3733', '2594', '3909', '5179', '2228', '6222', '2354', '1725', '2960', '2077', '5662', '3481', '2565', '4436', '5072', '5200', '4502', '4945', '3012', '6094', '3815', '4838', '3469', '4874', '1792', '4356', '6196', '3578', '4491', '3054', '1968', '5869', '5858', '5203', '2577', '3216', '2037', '3867', '5109', '1376', '5260', '2350', '2269', '3650', '3934', '4048', '4266', '3863', '4922', '4517', '3259', '3123', '3870', '2996', '2993', '4556', '2326', '4109', '5739', '5887', '1651', '1628', '2642', '6020', '4747', '5534', '4790', '4632', '1952', '4388', '5529', '1316', '1670', '4909', '3401', '4619', '4722', '2188', '5497', '5711', '4076', '1847', '2816', '5019', '4869', '3613', '3069', '2601', '2133', '3164', '3897', '5663', '3879', '5900', '2272', '5436', '3676', '4676', '1563', '4349', '1658', '2552', '4847', '1553', '2632', '1908', '5801', '1935', '2609', '2740', '1907', '1831', '5177', '6179', '2917', '1486', '3735', '3838', '2430', '2480', '3431', '1742', '4628', '2385', '2213', '5188', '3418', '5637', '3530', '3731', '3219', '2392', '5103', '3992', '2442', '2913', '1631', '4073', '4568', '1830', '2623', '4327', '3713', '2389', '5141', '3522', '1334', '4721', '3768', '1639', '5149', '2153', '2767', '5124', '2097', '4796', '5849', '1495', '2462', '2280', '1877', '6108', '4427', '2881', '2339', '5156', '4750', '4852', '5734', '3801', '4474', '1992', '5796', '1373', '1879', '3072', '4561', '5201', '5871', '3041', '5864', '3999', '4849', '5461', '5946', '4508', '5096', '5443', '2165', '2125', '6141', '5245', '2190', '2243', '3336', '3305', '6191', '1326', '6159', '2003', '5881', '1918', '2157', '3149', '2444', '6207', '5580', '5942', '1592', '2344', '5995', '5022', '5991', '4020', '6028', '2790', '4033', '1261', '1979', '2122', '4506', '3016', '3272', '1375', '4014', '3199', '2184', '2170', '1478', '6163', '1889', '5912', '2537', '2118', '3234', '3842', '3032', '3238', '2059', '5238', '2578', '4789', '2357', '1235', '3558', '1707', '2842', '5318', '1620', '4998', '3111', '1424', '3244', '4643', '6132', '3790', '1312', '5686', '2111', '5685', '1905', '3271', '4178', '3275', '5713', '3339', '5585', '4407', '1941', '4690', '3607', '2670', '5261', '4121', '4620', '4630', '3440', '3572', '5306', '5122', '1368', '5370', '5636', '6033', '3599', '5566', '1873', '3904', '4453', '5481', '2987', '2431', '4158', '3753', '4391', '2026', '4547', '2798', '4732', '5126', '5151', '2134', '5786', '5515', '4530', '4088', '3667', '3286', '4804', '5768', '6098', '2508', '5779', '1717', '3368', '4265', '4455', '2940', '6144', '3657', '1537', '2116', '5102', '5815', '4250', '4533', '3329', '3606', '4749', '2222', '1939', '2456', '2794', '2648', '2545', '3273', '6155', '6064', '1729', '4969', '3402', '2810', '2217', '5140', '4497', '5100', '3390', '1926', '3264', '2573', '3628', '3826', '5983', '5678', '2093', '5333', '5139', '3173', '3333', '2262', '1531', '4881', '3928', '1803', '2321', '6234', '1422', '2660', '5361', '3420', '4337', '2338', '4140', '2373', '1344', '2141', '3415', '5696', '1798', '1657', '3651', '3574', '6063', '2404', '2705', '5059', '5466', '5821', '5385', '3506', '5427', '6142', '2873', '3916', '1687', '1542', '3087', '3857', '5921', '3144', '2676', '3646', '1321', '1399', '3169', '2058', '1300', '2075', '2984', '4768', '3682', '5901', '3540', '2225', '1485', '5343', '5594', '2482', '2706', '4861', '2961', '3043', '6103', '4708', '4395', '4912', '2030', '5235', '5001', '4093', '2576', '6122', '2813', '3737', '2803', '5508', '3850', '4220', '4532', '6013', '3396', '4401', '2981', '4467', '1377', '5036', '5761', '3699', '4519', '5395', '3057', '2741', '1675', '2712', '4527', '1722', '4330', '5219', '4997', '2239', '4587', '4113', '4762', '5242', '5765', '2185', '5500', '3494', '3924', '3521', '5262', '3684', '5784', '1498', '6073', '4939']



```python
# Extract the four-digit contact ID number and add it to a new column in the `campaign_cleaned` DataFrame.
campaign_cleaned["contact_id"] = [x[15:19] for x in contact_info_df["contact_info"].values]
campaign_cleaned.head()
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>company_name</th>
      <th>description</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launch_date</th>
      <th>end_date</th>
      <th>category_id</th>
      <th>subcategory_id</th>
      <th>contact_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>2020-02-13</td>
      <td>2021-03-01</td>
      <td>cat01</td>
      <td>scat01</td>
      <td>4661</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400.0</td>
      <td>14560.0</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-01-25</td>
      <td>2021-05-25</td>
      <td>cat02</td>
      <td>scat02</td>
      <td>3765</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400.0</td>
      <td>142523.0</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>2020-12-17</td>
      <td>2021-12-30</td>
      <td>cat03</td>
      <td>scat03</td>
      <td>4187</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200.0</td>
      <td>2477.0</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-10-21</td>
      <td>2022-01-17</td>
      <td>cat02</td>
      <td>scat02</td>
      <td>4941</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600.0</td>
      <td>5265.0</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-12-21</td>
      <td>2021-08-23</td>
      <td>cat04</td>
      <td>scat04</td>
      <td>2199</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Convert the "contact_id" column to an int64 data type.
campaign_cleaned['contact_id'] = pd.to_numeric(campaign_cleaned['contact_id'])
campaign_cleaned.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1000 entries, 0 to 999
    Data columns (total 14 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   cf_id           1000 non-null   int64  
     1   company_name    1000 non-null   object 
     2   description     1000 non-null   object 
     3   goal            1000 non-null   float64
     4   pledged         1000 non-null   float64
     5   outcome         1000 non-null   object 
     6   backers_count   1000 non-null   int64  
     7   country         1000 non-null   object 
     8   currency        1000 non-null   object 
     9   launch_date     1000 non-null   object 
     10  end_date        1000 non-null   object 
     11  category_id     1000 non-null   object 
     12  subcategory_id  1000 non-null   object 
     13  contact_id      1000 non-null   int64  
    dtypes: float64(2), int64(3), object(9)
    memory usage: 117.2+ KB



```python
# Reorder columns
campaign_cleaned = campaign_cleaned.reindex(columns=["cf_id", "contact_id", "company_name", "description", "goal", "pledged", "outcome", "backers_count", "country", "currency", "launch_date", "end_date", "category_id","subcategory_id"])
campaign_cleaned.head()
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cf_id</th>
      <th>contact_id</th>
      <th>company_name</th>
      <th>description</th>
      <th>goal</th>
      <th>pledged</th>
      <th>outcome</th>
      <th>backers_count</th>
      <th>country</th>
      <th>currency</th>
      <th>launch_date</th>
      <th>end_date</th>
      <th>category_id</th>
      <th>subcategory_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>147</td>
      <td>4661</td>
      <td>Baldwin, Riley and Jackson</td>
      <td>Pre-emptive tertiary standardization</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>failed</td>
      <td>0</td>
      <td>CA</td>
      <td>CAD</td>
      <td>2020-02-13</td>
      <td>2021-03-01</td>
      <td>cat01</td>
      <td>scat01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1621</td>
      <td>3765</td>
      <td>Odom Inc</td>
      <td>Managed bottom-line architecture</td>
      <td>1400.0</td>
      <td>14560.0</td>
      <td>successful</td>
      <td>158</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-01-25</td>
      <td>2021-05-25</td>
      <td>cat02</td>
      <td>scat02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1812</td>
      <td>4187</td>
      <td>Melton, Robinson and Fritz</td>
      <td>Function-based leadingedge pricing structure</td>
      <td>108400.0</td>
      <td>142523.0</td>
      <td>successful</td>
      <td>1425</td>
      <td>AU</td>
      <td>AUD</td>
      <td>2020-12-17</td>
      <td>2021-12-30</td>
      <td>cat03</td>
      <td>scat03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2156</td>
      <td>4941</td>
      <td>Mcdonald, Gonzalez and Ross</td>
      <td>Vision-oriented fresh-thinking conglomeration</td>
      <td>4200.0</td>
      <td>2477.0</td>
      <td>failed</td>
      <td>24</td>
      <td>US</td>
      <td>USD</td>
      <td>2021-10-21</td>
      <td>2022-01-17</td>
      <td>cat02</td>
      <td>scat02</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1365</td>
      <td>2199</td>
      <td>Larson-Little</td>
      <td>Proactive foreground core</td>
      <td>7600.0</td>
      <td>5265.0</td>
      <td>failed</td>
      <td>53</td>
      <td>US</td>
      <td>USD</td>
      <td>2020-12-21</td>
      <td>2021-08-23</td>
      <td>cat04</td>
      <td>scat04</td>
    </tr>
  </tbody>
</table>
</div>




```python
campaign_cleaned.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 1000 entries, 0 to 999
    Data columns (total 14 columns):
     #   Column          Non-Null Count  Dtype  
    ---  ------          --------------  -----  
     0   cf_id           1000 non-null   int64  
     1   contact_id      1000 non-null   int64  
     2   company_name    1000 non-null   object 
     3   description     1000 non-null   object 
     4   goal            1000 non-null   float64
     5   pledged         1000 non-null   float64
     6   outcome         1000 non-null   object 
     7   backers_count   1000 non-null   int64  
     8   country         1000 non-null   object 
     9   currency        1000 non-null   object 
     10  launch_date     1000 non-null   object 
     11  end_date        1000 non-null   object 
     12  category_id     1000 non-null   object 
     13  subcategory_id  1000 non-null   object 
    dtypes: float64(2), int64(3), object(9)
    memory usage: 117.2+ KB


#### **Export to CSV**


```python
campaign_cleaned.to_csv('campaign_cleaned.csv', index=False, encoding='utf8')
```

### **Create the Contacts DF**

The contacts_df DataFrame will contain the following columns, which all contain data from the contact_info worksheet:
- A column named "contact_id" that contains the unique contact ID of each contact person.
- A column named "first_name" that contains the first name of each contact person.
- A column named "last_name" that contains the first name of each contact person.
- A column named "email" that contains the email address of each contact person.


```python
# Extract the four-digit contact ID number and add it to a new column in the contact_info DataFrame.
contact_info_df["contact_id"] = [x[15:19] for x in contact_info_df["contact_info"].values]
contact_info_df.head()
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_info</th>
      <th>contact_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>{"contact_id": 4661, "name": "Cecilia Velasco", "email": "cecilia.velasco@rodrigues.fr"}</td>
      <td>4661</td>
    </tr>
    <tr>
      <th>1</th>
      <td>{"contact_id": 3765, "name": "Mariana Ellis", "email": "mariana.ellis@rossi.org"}</td>
      <td>3765</td>
    </tr>
    <tr>
      <th>2</th>
      <td>{"contact_id": 4187, "name": "Sofie Woods", "email": "sofie.woods@riviere.com"}</td>
      <td>4187</td>
    </tr>
    <tr>
      <th>3</th>
      <td>{"contact_id": 4941, "name": "Jeanette Iannotti", "email": "jeanette.iannotti@yahoo.com"}</td>
      <td>4941</td>
    </tr>
    <tr>
      <th>4</th>
      <td>{"contact_id": 2199, "name": "Samuel Sorgatz", "email": "samuel.sorgatz@gmail.com"}</td>
      <td>2199</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Get the data from the first row and convert it to a dictionary.
first_row = contact_info_df.iloc[0]['contact_info']
converted_data = json.loads(first_row)
for k, v in converted_data.items():
    print(k,v)
```

    contact_id 4661
    name Cecilia Velasco
    email cecilia.velasco@rodrigues.fr



```python
# Iterate through the contact_info_df and convert each row to a dictionary.
dict_values = []
for i, row in contact_info_df.iterrows():
    # Get the data first item in each row.
    data = row['contact_info']
    converted_data = json.loads(data)
    # Iterate through each dictionary (row) and get the values for each row using list comprehension.
    row_values = [v for k, v in converted_data.items()]
    # Append the list of values for each row to a new list. 
    dict_values.append(row_values)

# Print out the list of values for each row.
print(dict_values)
```
    [[4661, 'Cecilia Velasco', 'cecilia.velasco@rodrigues.fr'], [3765, 'Mariana Ellis', 'mariana.ellis@rossi.org'], [4187, 'Sofie Woods', 'sofie.woods@riviere.com'], [4941, 'Jeanette Iannotti', 'jeanette.iannotti@yahoo.com'], [2199, 'Samuel Sorgatz', 'samuel.sorgatz@gmail.com'], [5650, 'Socorro Luna', 'socorro.luna@hotmail.com'], [5889, 'Carolina Murray', 'carolina.murray@knight.com'], [4842, 'Kayla Moon', 'kayla.moon@yahoo.de'], [3280, 'Ariadna Geisel', 'ariadna.geisel@rangel.com'], [5468, 'Danielle Ladeck', 'danielle.ladeck@scalfaro.net'], [3064, 'Tatiana Thompson', 'tatiana.thompson@hunt.net'], [4904, 'Caleb Benavides', 'caleb.benavides@rubio.com'], [1299, 'Sandra Hardy', 'sandra.hardy@web.de'], [5602, 'Lotti Morris', 'lotti.morris@yahoo.co.uk'], [5753, 'Reinhilde White', 'reinhilde.white@voila.fr'], [4495, 'Kerry Patel', 'kerry.patel@hutchinson.com'], [4269, 'Sophie Antoine', 'sophie.antoine@andersen.com'], [2226, 'Martha Girard', 'martha.girard@web.de'], [1558, 'Stephanie King', 'stephanie.king@cervantes.com'], [2307, 'Amanda Palmer', 'amanda.palmer@didier.fr'], [2900, 'Lina Alcala', 'lina.alcala@vespa.net'], [5695, 'Itzel Murphy', 'itzel.murphy@muelichen.de'], [5708, 'Filippo Parry', 'filippo.parry@live.com'], [1663, 'Katelyn Cole', 'katelyn.cole@fiebig.com'], [3605, 'Brian Novak', 'brian.novak@ford.net'], [4678, 'Cilly Gay', 'cilly.gay@callegaro.it'], [2251, 'Yolanda Snyder', 'yolanda.snyder@gmx.de'], [6202, 'Evelin Odonnell', 'evelin.odonnell@ibarra.net'], [3715, 'Ingeborg Alba', 'ingeborg.alba@hotmail.com'], [4242, 'Marina Madrid', 'marina.madrid@galarza-alba.com'], [4326, 'Sheila Goodwin', 'sheila.goodwin@yahoo.com'], [5560, 'Valeria Rich', 'valeria.rich@turchetta-mondadori.it'], [4002, 'Dustin Camacho', 'dustin.camacho@rhodes.org.au'], [5755, 'Sally Raya', 'sally.raya@tarchetti.it'], [5150, 'Carolyn Charpentier', 'carolyn.charpentier@hotmail.de'], [4181, 'Nathalie Alvarez', 'nathalie.alvarez@live.com'], [3006, 'Abril Lowery', 'abril.lowery@novak.net'], [4865, 'Ottone Sullivan', 'ottone.sullivan@hussain-kaur.com'],[3401, 'Marta Huynh', 'marta.huynh@hernandez.info'], [4619, 'Amador Riley', 'amador.riley@hotmail.com'], [4722, 'Julie Coles', 'julie.coles@hotmail.fr'], [2188, 'Beverley Telesio', 'beverley.telesio@regnier.fr'],  [5734, 'Anna Freeman', 'anna.freeman@snyder.net'], [3801, 'Emilia Vivaldi', 'emilia.vivaldi@garnier.fr'], [4474, 'Gerda Cook', 'gerda.cook@tin.it'], [1992, 'Andrey Faure', 'andrey.faure@frederick-mitchell.info'], [5796, 'Charlotte Duke', 'charlotte.duke@ochoa.org'], [1373, 'Cindy Castillo', 'cindy.castillo@virgilio.it'], [1879, 'Hiltrud Thibault', 'hiltrud.thibault@barbe.com'], [3072, 'Kasimir Jaime', 'kasimir.jaime@gmail.com'], [4561, 'Sabine Lachmann', 'sabine.lachmann@phillips.org'], [5201, 'Andrea Arroyo', 'andrea.arroyo@yahoo.com.au'], [5871, 'Matthew Velasquez', 'matthew.velasquez@gmail.com'], [3041, 'Severino Linares', 'severino.linares@angeli.com'], [5864, 'Swantje Lomeli', 'swantje.lomeli@gmx.de'], [3999, 'Marcus Espino', 'marcus.espino@comolli.eu'], [4849, 'Allen Gates', 'allen.gates@gmail.com'], [5461, 'Mauricio Saunders', 'mauricio.saunders@hotmail.co.uk'], [5946, 'Gilbert Cornejo', 'gilbert.cornejo@hotmail.com'], [4508, 'Letizia Gulotta', 'letizia.gulotta@howard.biz'], [5096, 'Etta Gutknecht', 'etta.gutknecht@leconte.net'], [5443, 'Shelley Perry', 'shelley.perry@gierschner.de'], [1498, 'Evangelista Pereira', 'evangelista.pereira@thompson-peterson.biz'], [6073, 'Gareth Comolli', 'gareth.comolli@tiscali.fr'], [4939, 'Michelangelo Hess', 'michelangelo.hess@bouygtel.fr']]


```python
# Create a contact_info DataFrame and add each list of values, i.e., each row to the 'contact_id', 'name', 'email' columns.
contacts_df = pd.DataFrame(dict_values, columns=['contact_id', 'name', 'email'])
contacts_df.head()
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_id</th>
      <th>name</th>
      <th>email</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4661</td>
      <td>Cecilia Velasco</td>
      <td>cecilia.velasco@rodrigues.fr</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3765</td>
      <td>Mariana Ellis</td>
      <td>mariana.ellis@rossi.org</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4187</td>
      <td>Sofie Woods</td>
      <td>sofie.woods@riviere.com</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4941</td>
      <td>Jeanette Iannotti</td>
      <td>jeanette.iannotti@yahoo.com</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2199</td>
      <td>Samuel Sorgatz</td>
      <td>samuel.sorgatz@gmail.com</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Assign the name colunmn to first_name and last_name columns.
contacts_df[["first_name","last_name"]] = contacts_df["name"].str.split(' ', n=1, expand=True)
contacts_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_id</th>
      <th>name</th>
      <th>email</th>
      <th>first_name</th>
      <th>last_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4661</td>
      <td>Cecilia Velasco</td>
      <td>cecilia.velasco@rodrigues.fr</td>
      <td>Cecilia</td>
      <td>Velasco</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3765</td>
      <td>Mariana Ellis</td>
      <td>mariana.ellis@rossi.org</td>
      <td>Mariana</td>
      <td>Ellis</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4187</td>
      <td>Sofie Woods</td>
      <td>sofie.woods@riviere.com</td>
      <td>Sofie</td>
      <td>Woods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4941</td>
      <td>Jeanette Iannotti</td>
      <td>jeanette.iannotti@yahoo.com</td>
      <td>Jeanette</td>
      <td>Iannotti</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2199</td>
      <td>Samuel Sorgatz</td>
      <td>samuel.sorgatz@gmail.com</td>
      <td>Samuel</td>
      <td>Sorgatz</td>
    </tr>
  </tbody>
</table>
</div>




```python
#drop the name column
contacts_cleaned_df = contacts_df.drop(["name"], axis = 1)
contacts_cleaned_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_id</th>
      <th>email</th>
      <th>first_name</th>
      <th>last_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4661</td>
      <td>cecilia.velasco@rodrigues.fr</td>
      <td>Cecilia</td>
      <td>Velasco</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3765</td>
      <td>mariana.ellis@rossi.org</td>
      <td>Mariana</td>
      <td>Ellis</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4187</td>
      <td>sofie.woods@riviere.com</td>
      <td>Sofie</td>
      <td>Woods</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4941</td>
      <td>jeanette.iannotti@yahoo.com</td>
      <td>Jeanette</td>
      <td>Iannotti</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2199</td>
      <td>samuel.sorgatz@gmail.com</td>
      <td>Samuel</td>
      <td>Sorgatz</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Reorder columns
contacts_cleaned_df = contacts_cleaned_df.reindex(columns=["contact_id", "first_name", "last_name", "email"])
contacts_cleaned_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>contact_id</th>
      <th>first_name</th>
      <th>last_name</th>
      <th>email</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4661</td>
      <td>Cecilia</td>
      <td>Velasco</td>
      <td>cecilia.velasco@rodrigues.fr</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3765</td>
      <td>Mariana</td>
      <td>Ellis</td>
      <td>mariana.ellis@rossi.org</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4187</td>
      <td>Sofie</td>
      <td>Woods</td>
      <td>sofie.woods@riviere.com</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4941</td>
      <td>Jeanette</td>
      <td>Iannotti</td>
      <td>jeanette.iannotti@yahoo.com</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2199</td>
      <td>Samuel</td>
      <td>Sorgatz</td>
      <td>samuel.sorgatz@gmail.com</td>
    </tr>
  </tbody>
</table>
</div>


```python
contacts_cleaned_df.to_csv('contacts_cleaned.csv', index=False, encoding='utf8')
```
