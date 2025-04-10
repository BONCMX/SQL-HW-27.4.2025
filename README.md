# SQL data cleaning
This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

## Data introduction
```sql 
SELECT  * FROM club_member_info LIMIT 10;
```
The result:
|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|
|Wanda del mar       |44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|Human Resources Assistant IV|3/24/2015|
|Joann Kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|Accountant IV|4/17/2013|
|   Joete Cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|Research Nurse|11/16/2014|
|mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|Systems Administrator III|3/12/1921|
| fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|Chemical Engineer|11/5/2014|

## Copy Table
### Create new table for cleaning
```sql
CREATE TABLE club_member_info_cleaned (
	full_name VARCHAR(50),
	age INTEGER,
	martial_status VARCHAR(50),
	email VARCHAR(50),
	phone VARCHAR(50),
	full_address VARCHAR(50),
	job_title VARCHAR(50),
	membership_date VARCHAR(50)
);
```
### Copy all value from original table
```sql
INSERT INTO club_member_info_cleaned 
SELECT  * FROM club_member_info;
```

## Cleaned full_name column
```sql
UPDATE club_member_info_cleaned SET full_name = TRIM(full_name);
UPDATE club_member_info_cleaned SET full_name = UPPER(full_name);
UPDATE club_member_info_clean
	SET full_name = REPLACE(REPLACE(full_name, '!', ''), '?', '')
WHERE full_name LIKE '%!%' OR full_name LIKE '%?%';
```
The result:
|TRIM(full_name)|
|---------------|
|ADDIE LUSH|
|ROCK CRADICK|
|SYDEL SHARVELL|
|CONSTANTIN DE LA CRUZ|
|GAYLOR REDHOLE|


## Cleaned age column
```sql
UPDATE club_member_info_clean
SET age =
	CASE
		WHEN LENGTH(age) > 2
		THEN SUBSTR(age, 1, LENGTH(age) - 1)
		ELSE age 
	END;
```
The result:
|age|age_cleaned|
|---|-----------|
|39|39|
|53|53|
|39|39|
|27|27|
|37|37|
|34|34|
|27|27|
|38|38|
|25|25|
|555|55|


## Cleaned martial_status column
```sql
UPDATE club_member_info_clean
SET martial_status = 
    CASE
        WHEN martial_status IS NULL OR TRIM(martial_status) = '' THEN 'Unknown'
        ELSE REPLACE(martial_status, 'divored', 'divorced')
    END;
```
The result:
|martial_status|martial_status_clean|
|--------------|--------------------|
|married|married|
|married|married|
|divorced|divorced|
||Unknown|
|married|married|
|single|single|
|married|married|
|divorced|divorced|
|single|single|
|married|married|

## Cleaned phone column
```sql
UPDATE club_member_info_clean SET phone = REPLACE(phone, '-', ' ');
```
The result:
|phone|phone_cleaned|
|-----|-------------|
|254-389-8708|254 389 8708|
|910-566-2007|910 566 2007|
|702-187-8715|702 187 8715|
|402-688-7162|402 688 7162|
|917-394-6001|917 394 6001|
|937-467-6942|937 467 6942|
|513-726-9885|513 726 9885|
|616-617-0965|616 617 0965|
|504-918-4753|504 918 4753|
|808-177-0318|808 177 0318|


## Cleaned job_title column
```sql
UPDATE club_member_info_clean SET job_title = CASE
	WHEN TRIM(job_title) = '' OR job_title IS NULL THEN 'Unknown'
	ELSE job_title
END;
```
The result:
|job_title|job_title_cleaned|
|---------|-----------------|
|Professor|Professor|
|Social Worker|Social Worker|
|Quality Engineer|Quality Engineer|
|Community Outreach Specialist|Community Outreach Specialist|
|VP Quality Control|VP Quality Control|
|GIS Technical Architect|GIS Technical Architect|
||Unknown|
|Human Resources Assistant IV|Human Resources Assistant IV|
|Marketing Manager|Marketing Manager|
|Help Desk Operator|Help Desk Operator|

## Cleaned membership_date
```sql
UPDATE club_member_info_clean SET membership_date =
 SUBSTR(membership_date, 1, LENGTH(membership_date) - 4) || '20' || 
    SUBSTR(membership_date, LENGTH(membership_date) - 1, 2)
WHERE membership_date LIKE '%19__';
```
The result:
|membership_date_fixed|
|---------------------|
|3/12/2021|
|10/1/2012|
|2/20/2016|
|5/8/2012|
|10/4/2019|
|3/10/2013|
|1/8/2012|
|9/2/2014|
|5/11/2016|
|1/31/2015|
|5/15/2015|
|3/3/2017|
|4/30/2015|
|5/22/2021|
|10/27/2015|
|7/5/2015|

