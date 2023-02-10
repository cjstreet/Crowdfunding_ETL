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
1.  Extract: Import backer_info.csv into Jupyter NB dataframe
2.  Converteach each row into a dictionary after turning the rows into dictionary values, we can organize the data by adding columns and seperating the information.
```
