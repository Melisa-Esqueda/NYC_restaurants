# Data Cleaning Documentation on the NYC Restaurants Dataset
# Melisa Esqueda

Link to the dataset: [NYC Restaurants Dataset](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/43nn-pn8j#)

Client - NYC Food, Health, and Safety Inspector 

Client wants to learn more about the restaurants in Manhattan. 

#### Description of how each column in the dataset was cleaned, along with its SQL code.
Data cleaning was performed in PostgreSQL. 
#### camis: 
Made sure that each entry had 10 digits by converting the camis column’s datatype to VARCHAR in order to count the number of digits of each entry. It turns out that each entry has 8 digits, not 10.


    SELECT LENGTH(CAST(camis AS VARCHAR))
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';
    
    SELECT DISTINCT LENGTH(CAST(camis AS VARCHAR))
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';

There were no NULL values in this column. Same code, with different values, was used to find the NULL values for each column. 

    SELECT camis
    FROM nyc_restaurants
    WHERE camis IS NULL and boro = 'Manhattan';
 
#### dba
Checked to make sure that there are no NULL values. There are 482 NULL values.

    SELECT dba, street, phone
    FROM nyc_restaurants
    WHERE dba IS NULL and boro = 'Manhattan';

To be able to replace these NULL values with their appropriate names, columns dba, street, and phone columns were selected in order to examine their consistency among other records. To make this process easier, a nested query was created in order to select columns dba, street, and phone where dba is NULL. From there, the NULL values can get replaced with their respective names that match with certain details of other records. Otherwise, NULL values with no respective dba values were deleted. 

    SELECT dba, street, phone
    FROM nyc_restaurants
    WHERE dba IS IN (SELECT dba FROM nyc_restaurants WHERE dba IS NULL) and boro = 'Manhattan';


To make sure that the spelling and grammar of the dba values are consistent, the dba column values were capitalized and were sorted by ascending order. 

    UPDATE
    NYC_restaurants
    SET
    dba = UPPER(dba)
        
    SELECT dba
    FROM nyc_restaurants
    WHERE boro = 'Manhattan'
    ORDER BY dba;

Since some of these values have values from the street column incorporated in them, this code was used to extract these values.

    SELECT dba
    FROM nyc_restaurants
    WHERE boro = 'Manhattan' and dba ~ '[0-9]+';
      
The values that contained street values that weren't consistent with those in the street column and some inconcsistent values were deleted.
 
    DELETE
    FROM NYC_restaurants
    WHERE dba IN ('NECTAR OF 82ND STREET', 'NECTAR OF 82ND STREET', '10TH AVENUE PIZZA & CAFE', '2A', 'LADY M CONFECTIONS (PLAZA HOTEL 770 5TH AVENUE)', 'STONE STREET TAVERN (@ 52 STONE ST)', '12TH ST. ALE HOUSE', '57TH BAGELS & MORE')

For the values that contained the franchise number next to it, their values were changed to be more consistent.

    SELECT *
    FROM nyc_restaurants
    WHERE boro = 'Manhattan' and dba ~ '#[0-9]+';

    SELECT *
    FROM nyc_restaurants
    WHERE boro = 'Manhattan' and dba LIKE '%STARBUCKS%' or dba LIKE '%CHIPOTLE%';

    UPDATE nyc_restaurants
    SET dba = 'STARBUCKS'
    WHERE dba LIKE '%STARBUCKS%';

    UPDATE nyc_restaurants
    SET dba = 'CHIPOTLE MEXICAN GRILL'
    WHERE dba LIKE '%CHIPOTLE%';

#### boro
There were no NULL values.

#### building 
NULL values were deleted. Same code, with different values, was used to delete the NULL values for each column.

    DELETE FROM nyc_restaurants
    WHERE boro = 'Manhattan' and building IS NULL;
Zero values were deleted.

    DELETE 
    FROM nyc_restaurants
    WHERE boro = 'Manhattan' and building = '0';
      
#### street
To make sure that the street values were consistent with the dba values, the street column was sorted by ascending order and corrected if necessary.

    SELECT dba, street
    FROM nyc_restaurants
    WHERE boro = 'Manhattan'
    ORDER BY street;
    
NULL values were deleted. 

#### zipcode
There were no NULL values.

Made sure that each entry had 5 digits by converting this column’s datatype to VARCHAR in order to count the number of digits of every entry. 

    SELECT LENGTH(CAST(zipcode AS VARCHAR))
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';
    
    SELECT DISTINCT LENGTH(CAST(zipcode AS VARCHAR))
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';
    
#### phone
NULL values were deleted.

Made sure each entry had 10 digits by converting this column’s datatype to VARCHAR in order to count the number of digits of every entry. 

    SELECT LENGTH(CAST(phone AS VARCHAR))
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';
        
    SELECT LENGTH(CAST(phone AS VARCHAR))
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';
    
Entries that have greater or less than 10 digits were deleted.

    DELETE 
    FROM nyc_restaurants
    WHERE boro = 'Manhattan' and LENGTH(CAST(phone AS VARCHAR)) > 10 or          LENGTH(CAST(phone AS VARCHAR)) < 10;
        
#### cuisine_description
NULL values were deleted.

#### inspection date
There were no NULL values. 

#### action
There are no NULL values.

#### violation_code
Null values were deleted.

#### violation_description
There were no NULL values.

Grouped the table by violation_code and violation_description columns to make sure that these columns were consistent with each other in terms of what their values meant.

    SELECT violation_code, violation_description
    FROM nyc_restaurants
    WHERE boro = 'Manhattan'
    GROUP BY violation_code, violation_description;
        
#### Critical_flag
There were no NULL values.

Selected the distinct values of this column to make sure entries were consistent.
  
    SELECT DISTINCT critical_flag
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';

#### score
Deleted NULL values.

Selected distinct values to make sure entries were consistent.

#### grade
There were no NULL values.

Selected the distinct values of this column to make sure entries were consistent with NYC food grading system. Link to website: [Grading System ](https://data.cityofnewyork.us/Health/DOHMH-New-York-City-Restaurant-Inspection-Results/rs6k-p7g6)

    SELECT DISTINCT grade
    FROM nyc_restaurants
    WHERE boro = 'Manhattan';

#### grade_date
NULL values were deleted.

Deleted dates that indicate that no inspection was made.

    SELECT grade_date
    FROM nyc_restaurants
    WHERE boro = 'Manhattan'
    AND grade_date = '1900-01-01'

#### record_date
There were no NULL values.

#### inspection_type
There were no NULL values.

#### latitude
There were no NULL values.

Deleted latitudes that were equal to zero.

    DELETE
    FROM nyc_restaurants
    WHERE latitude = '0' and boro = 'Manhattan';

#### longitude
There were no NULL values.

Deleted latitudes that were equal to zero.

    DELETE
    FROM nyc_restaurants
    WHERE longitude = '0' and boro = 'Manhattan';

#### bbl 
Inconsistent values were deleted.

    DELETE
    FROM nyc_restaurants
    WHERE boro = 'Manhattan' and bbl = '1';

#### For the rest of the columns that won't be needed...

NULL values were deleted.
            
    SELECT *
    FROM NYC_restaurants
    WHERE NYC_restaurants IS NOT NULL
    and boro = 'Manhattan';

    DELETE FROM NYC_restaurants
    WHERE NOT (NYC_restaurants IS NOT NULL);
        
#### For the duplicates...
Columns that determined the unique records of this dataset were selected, grouped, and counted.

    SELECT dba, inspection_date, violation_code, count(*)
    FROM nyc_restaurants
    WHERE boro = 'Manhattan'
    GROUP BY dba, inspection_date, violation_code
    HAVING count(*) > 1;

Duplicates were deleted. Unique records and columns that are relevant for the data visualization project were selected,
        
    DELETE FROM nyc_restaurants
    WHERE camis IN (SELECT camis FROM (SELECT camis, ROW_NUMBER() OVER (PARTITION BY dba, inspection_date, violation_code) AS row_num FROM nyc_restaurants) AS sub WHERE row_num > 1);


    SELECT camis, COUNT(*)
    FROM nyc_restaurants
    WHERE boro = 'Manhattan'
    GROUP BY camis
    HAVING COUNT(*) > 1;

    with cte as
    (select *, ROW_NUMBER() over (partition by camis order by camis) AS num_rep FROM nyc_restaurants)
    SELECT camis, dba, boro, street, zipcode, cuisine_description, action, violation_code, violation_description, critical_flag, score, grade, grade_date, record_date, inspection_type, latitude, longitude, community_board, council_district
    FROM CTE
    WHERE num_rep = 1;










