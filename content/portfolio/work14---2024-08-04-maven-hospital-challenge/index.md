---
title: Maven Hospital Challenge
author: Ntobeko Sosibo [ Afrikaniz3D ]
date: '2024-08-04'
slug: maven-hospital-challenge
categories:
  - project
tags:
  - analytics
  - cte
  - databricks
  - dataset
  - data science
  - data analysis
  - data exploration
  - eda
  - big query
  - sql
  - maven analytics
  - maven challenge
  - mgh 
  - massachusetts general hospital
  - healthcare analytics
  - healthcare
draft: no
image: 'img/portfolio/W14/w14000.webp'
showonlyimage: yes
---
  
## 1. Maven Hospital Challenge Introduction  
  
####  1.1 Challenge Objective  
For the [Maven Hospital Challenge](https://mavenanalytics.io/challenges/maven-hospital-challenge/facee4d2-8369-4c87-a55e-e6c7ed2a42d8?utm_source=linkedin&utm_campaign=mavenhospitalchallenge_li_maven), you’ll play the role of an Analytics Consultant for **Massachusetts General Hospital (MGH)**.  

You’ve been asked to **build a high-level KPI** report for the executive team, based on a subset of patient records. The purpose of the report is to **give stakeholders visibility into the hospital’s recent performance**, and answer the following questions:  

  - How many patients have been admitted or readmitted over time?
  - How long are patients staying in the hospital, on average?
  - How much is the average cost per visit?
  - How many procedures are covered by insurance?  

The dashboard should scale to accommodate new data over time, but the CEO has asked you to summarize any insights you can derive from the sample provided.  
  
####  1.2 Tools Used  
The data exploration is conducted using [Databricks SQL](https://www.databricks.com/). The data processing, exploration, and results/visualizations are generated and presented in-house. Any stylised images were made using [GIMP](https://www.gimp.org/)  
  
  _  
    
##  2.  Mounting Tables Into Notebook  
  

![][1]  
  
![][2]  
  
![][3]  
  
![][4]  
  
![][5]  
  
![][6]  
  
  _  
    
##  3.  Data Profiling and Cleaning  
  
####  3.1 Profiling the patients table  
  
#####  3.1.1 Composition  
The *patients* table is comprised of 20 columns/variables. One (1) variable, *ZIP* is an **INTERGER**. Two (2) variables (*LAT* and *LON*) are **DOUBLE PRECISION** for location data. Another two (2), *BIRTHDATE* and *DEATHDATE*, are **DATETIME**, and the remaining fifteen (15) (*Id*, *PREFIX*, *FIRST*, *LAST*, *SUFFIX*, *MAIDEN*, *MARITAL*, *RACE*, *ETHNICITY*, *GENDER*, *BIRTHPLACE*, *ADDRESS*, *CITY*, *STATE*, and *COUNTY*) are **STRING**.  
  
#####  3.1.2 Categories within the STRING data  
-   **PREFIX**: Mr., Ms., Mrs  
-   **FIRST**: various first names with an additional random three (3) numbers attached to accommodate instances of people having the same first name (Example: Timothy786) 
-   **LAST**: various last names with an additional random three (3) numbers attached to accommodate instances of people having the same last name (Example: Jones034)  
-   **SUFFIX**: JD, PhD, MD  
-   **MAIDEN**: Only used cases where the female patient is married ('Mrs.')  
-   **MARITAL**: S (single), M (married)  
-   **RACE**:  asian, other, white, black, native, hawaiinan
-   **ETHNICITY**: hispanic, nonhispanic  
-   **GENDER**: F, M  
-   **BIRTHPLACE**: various geographic locations (297 in total)  
-   **CITY**: various cities (29 in total)  
-   **COUNTY**: various counties (5 in total)  

Things that stood out when looking at this data were namely the *ETHNICITY* column's hispanic/nonhispanic binary - which I'll keep in-mind for the remainder of the exploration - as well as some of the patients from other countries.  
The solution for addressing repeated/same names (attaching a random 3-digit number) is worth noting for future projects.  
  
####  3.2 Profiling the payers table  
  
##### 3.2.1 Composition  
The *payers* table is comprised of 7 columns/variables. One (1) variable, *ZIP* is the only **INTERGER**. The remaining 6 variables (*Id*, *NAME*, *ADDRESS**, *CITY*, *STATE_HEADQUARTERED*, and *PHONE*) are **STRING**.  
  
##### 3.2.2 Categories within the STRING data  
- **ZIP**:  6002, 46204, 40018, 6156, 55436, 60007, 21244
- **NAME**: Cigna Health, NO_INSURANCE, Humana, Aetna, Blue Cross Blue Shield, Dual Eligible, Medicaid, 
Medicare, UnitedHealthcare, Anthem
- **CITY**: Louisville, Indianapolis, null, Chicago, Minnetonka, Bloomfield, Hartford, Baltimore  
- **STATE_HEADQUARTERED**:  MN, null, KY, CT, MD, IL, IN  
   
Something that stood out which I might explore further are any possible relationships around patients/procedures with **Dual Eligible** as their insurer.  
  
####  3.3 Profiling the encounters table  
  
##### 3.3.1 Composition  
The *encounters* table is comprised of 14 columns/variables. One (1) variable, *REASONCODE* is the only **BIGINT**. Three (3) variables (*BASE_ENCOUNTER_COST*, *TOTAL_CLAIM_COST*, and *PAYER_COVERAGE*) are *DOUBLE PRECISION*. There is another single variable (**CODE**) that is an **INTERGER**, and two (2) variables (*START* and *STOP*) that are **TIMESTAMP**. The remaining seven (7) variables (*Id*, *PATIENT*, *ORGANIZATION*, *PAYER*, *ENCOUNTERCLASS*, *DESCRIPTION*, and *REASONDESCRIPTION*) are all **STRING**.  
  
##### 3.3.2 Categories within the STRING data  
-   **ENCOUNTERCLASS**: wellness, outpatient, emergency, inpatient. urgentcare, ambulatory  
-   **CODE**:   Forty-five (45) numerical strings that are between 7 and 9 digits in length   
-   **DESCRIPTION**:    Fifty-three (53) descriptions for the types of encounters. Examples include "*Postnatal visit*" and "*Cardiac Arrest*"  
-   **REASONCODE**: Seventy-four (74) numerical strings of varying length (drastic in some cases)  
-   **REASONDESCRIPTION**: Full-text description, corresponding with *REASONCODE*
  
Some of the descriptions are vague, like "*Encounter for problem*", and rely on the *REASONDESCRIPTION* to give more clarity. The distribution of the types of procedures will be explored later on to see what MGH's "popular" procedures are.  
  
####  3.4 Profiling the procedures table  
  
##### 3.4.1 Composition  
The *encounters* table is comprised of 9 columns/variables. The first twp (2) variables (*START* and *STOP*) are **TIMESTAMP**. Two (2) variables (*CODE* and *REASONCODE*) are **BIGINT**. *BASE_COST* is the only **INTERGER**, and the remaining four (4) variables (*PATIENT*, *ENCOUNTER*, *DESCRIPTION*, and *REASONDESCRIPTION*) are all **STRING**.  
  
##### 3.4.2 Categories within the STRING data  
- **CODE**: 157 numerical string data in ranging digit length that represent procedures  
- **DESCRIPTION**:  163 text string data describing the procedures performed  
- **REASONCODE**: Forty-seven (47) numerical strings of various length  
- **REASONDESCRIPTION**:  Forty-seven (47) text string data describing the reasons for the prerformed procedures  
  
The thing that stood out for me here was the inclusion of a *null* value in the **REASONDESCRIPTION** column I'll look further into to see if the cause can be identified, and whether anything could be done to address it.  
  
  _  
  
##  4.  Answering the Questions  
  
####  4.1 How many patients have been admitted or readmitted over time?  
  
##### 4.1.1 How many patients have been admitted?  
There were **1880** patients admitted. To answer this I needed to first look at an understand what constituted being admitted to the hospital - rather than a visit. some online queries revealed that you could be admitted for as little a time as two (2) hours, and some procedures, regardless of how long, could be done without needing to be admitted.  
  
For this reason, I chose to only look for patients that had the variations of the word "admitted/admission" used in the *DESCRIPTION* column in the *encounters* table, or those that had an *ENCOUNTER_CLASS* of "*inpatient*" as these two conditions either explicitly state there is admission or admission is the condition for the class designation.  
  
![][7]  
  
##### 4.1.2 How many patients have been readmitted?  
There are **247** patients that have been readmitted. To answer this question took the query from **4.1.1** and applied an additional conditioned that filtered for *COUNT()* of the *patient* column with a value greater than one (1). To account for the initial admission I included a *"- 1"* to the *COUNT()* for admissions - so that it's **only** counting readmissions.  
  
![][8]  
  
![][9]  
  
####  4.2 How long are patients staying in the hospital, on average?  
Patients are staying **22hrs 52min** on average.  
  
![][10]  
  
####  4.3 How much is the average cost per visit?  
The average cost of a visit was **$116.18** (base), and **$3 639.68** (with a claim). The *encounters* table was used to answer this question where I looked at both the **base** cost as well as the **total claim cost** as the question was a little open-ended in that regard.  
  
![][11]  
  
####  4.4 How many procedures are covered by insurance?  
Of the **47 701** recorded procedures from *2011-01-02* to *2022-01-29*, **8228** (**17.24%**) procedures were covered by insurance.  
  
##### 4.4.1  Querying the number of procedures covered by insurance  
  
![][12]  
  
##### 4.4.2 Percentage of the total procedures performed that were insured  
  
![][13]  
  
##### 4.4.3 15-minute childbirths?  
While looking through the **procedures** table I noticed that, particularly among childbirths, there were patients that had multiple 15 minute childbirths and they were happening at seemingly scheduled time slots (like 15:00 or 14:45). A Chat-GPT inquiry revealed that there were a number of possible reasons for this, the most like being [Data Anonymization or Privacy Measures](https://www.k2view.com/what-is-data-anonymization/#:~:text=Data%20anonymization%20is%20the%20process%20of%20obscuring%20or%20removing%20personally,people%20associated%20with%20that%20data.). Learning this made me realise that any deep look into the pregnancy and childbirth data would be skewed by this.  
  
I looked at the other procedure descriptions and found that the same measures weren't being used. Curious which kinds of procedures the hospital felt needed that level of protection. Of the 163 different types of procedures MGH performs, **93** seem to have varying degrees of anonymization applied.  
  
![][14]  
  
After reading many of the descriptions in the results, it makes sense why anonymization was used. The new curiosity was that there were subsets which I could possibly explain as the hospital perhaps having a coding system that classes the procedure by the time length within that category.  
  
![][15]  
  
  _
  
## 5. Final Thoughts  
This has been a great exercise in getting a lot of practice with **CTE**. Some queries more than three (3) CTEs with *JOIN*s included. I also had previously never encountered cases where I had a *FROM* statement that referenced two tables, so I'm looking forward to getting more practice there.  
It was also interesting working with hospital data, getting to learn about people's live throught their relationships with the hospital. Some of their patients have been through so much..  



[1]: /img/portfolio/W14/w14001.png
[2]: /img/portfolio/W14/w14002.png
[3]: /img/portfolio/W14/w14003.png
[4]: /img/portfolio/W14/w14004.png
[5]: /img/portfolio/W14/w14005.png
[6]: /img/portfolio/W14/w14006.png
[7]: /img/portfolio/W14/w14007.png
[8]: /img/portfolio/W14/w14008.png
[9]: /img/portfolio/W14/w14009.png
[10]: /img/portfolio/W14/w14010.png
[11]: /img/portfolio/W14/w14011.png
[12]: /img/portfolio/W14/w14012.png
[13]: /img/portfolio/W14/w14013.png
[14]: /img/portfolio/W14/w14014.png
[15]: /img/portfolio/W14/w14015.png
