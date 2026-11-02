# CS665-Project
Project for CS665: Introduction to Database Systems JK

Jacob Krehbiel Y266G455


# Check in 1 entries:


Problem Definition and Mobile Scope: 

There are companies that specialize in matching companies that need temporary work to workers (For example, a moving company needs people for a single afternoon shift). The goal of this project is to create an app that sort of replicates what those companies perform as an app. Both workers and companies can post/upload requirements, and then the system will match up appropriate combinations so that workers are matched with companies.
In order to limit the scope, I plan more on focusing on the database/domain aspect rather than getting into stuff such as accounts, internal company moderation and such. Features should be focused on finding the best match for worker and company (focused on schedule, skills, need) , notifying them, and allowing that to work out, and then potentially keeping track of the results of that match.

Initial Database Design and Mechanics:

The main tables that seem immediately necessary are workers, companies, job type, schedules, shifts, etc. The next section will have a section for tables I plan on having (at least, for my first idea), and detailing some basic details for each table, and the primary/foreign keys.

I will note that in my previous class that covered databases, they advised us to use primary keys as numeric incrementing values that have no inherent meaning, but since this class has not been using that same view for primary keys, I shall try to avoid that.  

Workers: Each row is a worker, who has some basics available about them such as their name, age, etc. More advanced info would be things such as their skills.
Primary key:  Worker ID
Foreign key: None, but they are referred to by other tables.

Worker Schedule: Each worker will have potentially multiple schedules. The point of this table is to allow for workers to put their availibility for shifts. I am uncertain of the exact format I would like so is two proposed methods. 1) Each row is for a specific date for a specific worker, listing the time they are available on the day. 2) Each row contains days of the week (Monday, Tuesday, etc) for a specific employee, and the time for each day. Method 1 allows for more specificity of schedules, but 2 requires the user to only input 7 days worth of information instead of 1, which can require any amount of days worth of information. One thing that would be shared between versions is that it would be listed if that scheduling date is 'handled'/already matched (ie: If worker A has schedule available for Monday at 2 PM, if they end up matching for a shift at that time, the 2 PM Monday listing will stay in schedules, but be listed as already taken)

Primary key: Schedule ID or a composite key of Worker+Date
Foreign key: WorkerID to refer to workers table

Companies: Each row is a company that wnats to put out temproary postings. Each company would have company name, perhaps stuff about comapny history (like number of sucessful matches on the app)
Primary Key: Company Name (or company ID if we believe concept of multiple companies with same name is realistic)
Foreign Key: None, but referred to by other tables

Job Type: Each company can have many job types (the row of this table) that will detail: the skills necessary, the payrate for this sort of job, potential legal requirements (like workers being over 18 in age), the company that created this job type

Primary Key: Job ID (or composite key of company name/ID + job title)
Foreign Key: Company Name/ID


Shifts: Each row is an instance of a job type, with this containing a specific shift, ie: the job type is a painter, and the shift represents a specific shift for working as a painter at 6 PM to 8 PM on February 18th, 2018. This will contain length of shift, date of shift, number of people needed for the shift, how many have been recruited so far, etc
Primary Key: Shift ID (or composite key of job type primary key (whatever form it ends up as ) + date)
Foreign Key: Job Type Primary Key

Shift/Worker match: This table is for a combo of shift/worker, where each row refers to a shift/worker combo, potentially containing details such as: did the worker arrive on time, how many hours of the shift did they stay, a rating of their performance perhaps. 

Primary Key: Composite Key of Shift ID + Worker ID
Foreign Key: Shift ID and Worker ID




The above makes up 6 tables, and some may end up combined, or more may end up being created, but they seem to handle most of the logic necessary to have workers/companies and to match them up for posted shifts. 

We didn't cover how to define composite keys in class yet, so I just used basic primary keys for all of the below, but note above notes for how composite keys may be implemented for some cases
SQL for database proposal:

Create Table Worker(
    Worker_ID VarChar(10) Primary Key,
    First_Name VarChar(100),
    Last_Name VarChar(100),
    Middle_Name VarChar(100)
)


Create Table WorkerSchedule(
    Schedule_ID VarChar(30) Primary Key,
    Worker_ID VarChar(10), #Foreign key to worker
    Date DateTime,
    Start_Time Time,
    End_Time Time
)

Create Table Company(
    Company_Name VarChar(500) Primary Key,
    Company_Founded DateTime,
    Company_Matches Int,
    Company_Revenue Decimal(32, 2),
)

Create Table JobType(
    Job_ID VarChar(10) Primary Key,  #can look at ntoes above to see how it may defined as composite key, but just listing like this for now
    Company_ID VarChar(500), #Foreign Key to company
    ...#here will be where skills are listed, a column for each skill type where it is boolean true or false, along with other requirements like "Over 18 required, boolean")
    Pay_Rate Decimal(32, 2)
)

Create Table Shift( 
    Shift_ID VarChar(100) Primary Key, #can look at notes, to see how it may be defined 
    Job_ID VarChar(10), #foreign key to jobtype
    Start_Time DateTime,
    End_Time DateTime,
    People_Required Int,
    People_Available Int
)

Create Table ShiftWorker(
    Shift_Worker_ID VarChar(110) Primary Key,
    Shift_ID VarChar(100), #Foreign key to shifts
    Worker_ID VarChar(10), #Foreign Key to workers
    Worker_Arrived Boolean,
    Hours_Worked Float,
    Performance_Rating Int   
)



Relational Algebra Example Queries (could also do in SQL, but the check in specifically requests in relational algebra):

σ, π, ∩, ∪, ⋈


1)

Names of all workers who have worked for Company A

π_Worker.First_Name and Worker.Last_Name(((((σ_Company=="Company A"(Company)) ⋈ (JobType)) ⋈ (Shift)) ⋈  ShiftWorker) ⋈ Worker)


2)

All shifts that employee with name Jacob has shown up for

(((σ_Worker.First_Name == "Jacob"(Worker)) ⋈ (π_Worker_Arrived==True(ShiftWorker)) ⋈ Shift)


3) Companies that have jobs available for people under 18


π_Company_Name(σ_Over_18 == False(JobType) ⋈ Company)

4) All dates that employees with last name Richard are able to work after 2 PM 


 π_Date((σ_Last_Name == "Richard"(Worker)) ⋈ (σ_End_Time >= 2:00 PM (σ_Start_Time <= 2:00 PM(WorkerSchedule))))


5) Shift IDs that paid over 10 dollars an hour and had the full amount of people requested


 π_Shift_ID((σ_Pay_Rate > 10.00(Worker)) ⋈ (σ_People_Available>=People_Required(Shift)))





AI Utilization Plan: 

I don't tend to use AI very much in school at all (typically because the stuff being done doesn't feel dificult enough where outside help feels useful/necssary), so typically the scope for me is slightly limited. However, since this project is solo, and the scope of the project is decently large (full deployable app for mobile), AI may be useful for some of the aspects that I have not worked with before that are not the subject of the class (ie: mobile integration)

In general, I tend to use AI mainly for one main purpose:

1) Identifying what to use: That is a fairly general description, and there are two subcategories that I think will define it better

a) Identifying something that I know exists, but don't recall
b) Identifying something that I don't know what exists, but what I want


To give examples for these two categories, here are some below. I will note, that often the AI use is because I google these sorts of questions and google gives Gemini responses by default, so I am somewhat forced into seeing the AI response before other websites.

a) What is the function in python for string regex matching, What is the keyword in SQL for summing?

For category A, you can see the questions are quite simple are a result of not recalling the specific syntax/function name for something. Usually the website results work just as well, but since the google result shows AI first, I will often see that before anything else.
Notably, this category uses the model of Gemini that exists in google queries. 

b) What packages handle html parsing in python? What packages handle Android mobile app integration for python?

These sorts of question differ from category A as they handle things I have not used myself before. They are used to identify the starting/jumping off points for use of new things. Typically after I identify a package that seems promising, I will look at their own internal documentation or other guides online instead of using AI any further.
Sometimes I would use the gemini google results for this, but if I don't find that helpful, I may use something like the free version of Claude. 

For the sake of this project, there may be another category implemented, that being debugging purposes for mobile app integration. Due to my inexperience, I expect some difficulties with this aspect of the project, and if any bugs are particularly difficult to identify the cause of, I may output the error into Claude and use it as a guide for discovring how to debug it further.


# Check in 2 entries:


In order to check the verification of the table, I will check if every table is 1NF, then 2NF, then 3NF and so on, and making necessary changes when any mistakes are seen at each stage.
Refer to the design of tables designated in check-in 1 to refer to schema.

1NF:
Reminder, check that each table has 1) No features that contain a vector for each record 2) Primary Key Identified

Worker:

Has primary key identified, none of the features are vectors

WorkerSchedule:

Has primary key identified, none of the features are vectors

Company:

Primary Key identified, none of the features are vectors.

JobType:

Primary key identified, none of the features are vectors

Shift:

Primary key identified, none of the features are vectored

ShiftWorker:

Primary key identified, none of the features are vectors.


So, the schema is normalized up to 1NF.

Before 2NF, I will update the table's designs to make composite key's explicit because in check-in 1 I only proposed how each one may be defined. I will define them now so that the 2NF check can be meaningful. 

Updated Schema:

No Changes except making plural

```
Create Table Workers(

    Worker_ID VarChar(10) Primary Key,

    First_Name VarChar(100),

    Last_Name VarChar(100),

    Middle_Name VarChar(100)
)
```


I wish to do a composite key as primary, but it seems that Worker + Date would not be sufficent 

That would make it so there would not be possible for a worker to have multiple time regions with gaps avaialble on same day (ie: 2:00 PM to 5:00 PM and then 6:00 PM to 8:00 PM). Additionally, adding any more attributes being added to composite keys seems excessive. 

So I just updated the foreign key to be more explicity
```
Create Table WorkerSchedules(
    Schedule_ID VarChar(30) Primary Key,

    Worker_ID VarChar(10), #Foreign key to worker

    Date DateTime,

    Start_Time Time,

    End_Time Time,

    Foreign Key (Worker_ID) References Workers(Worker_ID)
)
```

No need to make composite key, company name should be a unique identified already
```
Create Table Companies(
    Company_Name VarChar(500) Primary Key,

    Company_Founded DateTime,

    Company_Matches Int,

    Company_Revenue Decimal(32, 2),
)
```

Added job title

Made foreign key explicit

Made composite key of company ID and job title, and removed old primary key

Skills still left blank, as it is really is the sort of thing that ought to be added as I think of relevant skills for the companies I include as example cases

Don't want to add painting as skill, if it is never relevant for instance

```
Create Table JobTypes(
    Company_ID VarChar(500) Non-Null, #Foreign Key to company

    Job_Title VarChar(100) Non-Null, 
    ...#here will be where skills are listed, a column for each skill type where it is boolean true or false, along with other requirements like "Over 18 required, boolean")

    Pay_Rate Decimal(32, 2),

    Constraint Job_ID Primary Key (Company_ID, Job_Title),

    Foreign Key (Company_ID) References Companies(Company_ID)
)
```

Added location as new required aspect that will end up being part of composite

Make composite key (fairly complicated, but it does seem the minimum to uniquely identify for this section)
 
I will discuss the decision more in 2NF, but it does seem difficult to use much less to define a shift uniquely, and I do believe nearly all of these attributes are consquences of all four, but that will be examined later (and potentially updated)


```
Create Table Shifts( 
    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    End_Time DateTime Non-Null,

    People_Required Int,

    People_Available Int,

    Constraints Shift_ID Primary Key (Company_ID, Job_Title,  Location, Start_Time),

    Foreign Key (Company_ID, Job_Title) References JobTypes(Job_ID)
)
```



Removed Shift_Worker_ID to make it a composite key

For 2NF normalization purposes, will consider the 4 compoments to be the aspect of a "Shift", and will therefore only consider if each attribute depends on shift, and if each attribute depends on worker

```
Create Table ShiftWorkers(

    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    Worker_ID VarChar(10) Non-Null, #Foreign Key to workers

    Worker_Arrived Boolean,

    Hours_Worked Float,

    Performance_Rating Int,

    Constraint Shift_ID Foreign Key (Company_ID, Job_Title, Location, Start_Time) References Shifts(Shift_ID)

    Constraint Shift_Worker_ID Primary Key (Shift_ID, Worker_ID) 
)
```
I orignally was not using the constraints to refer to composite keys, but then I realized that it increased the complexity of how statements looked, so I started using it.

I am uncertain as if to I should do it from the start for each table (ie: In ShiftWorkers, remove the four variabels that comprise Shift_ID and only leave Shift_ID)

I don't intend the table to be focused around those features, but they are necessary to identify the shift specifically, which is necessary. 

I could theoretically just replace them with Shift_ID and have the varchar length be the composite of the other variables, but that like one is just trying to hide what is happening underneath. The same amount of storage space will be used, no matter whether I call it 4 variables or just 1 that is the pasting of them. 


Another thing I am slightly uncertain on is whether to include the foreign key relations that are 'irrelevant' for a table. For instance, ShiftWorkers only includes Company_ID as it is necessary to form the Shift_ID, but I am uncertain to whether I should include the foreign key relation of Company_ID to Companies, or just include the composite key foreign relation to Shifts. 

2NF Normalization (Checking/Making changes if necessary):

For 2NF normalization, I need to only inspect the tables that have composite keys, because the 2NF condition is that each feature/attribute relies on each piece of the composite key.

Effectively, this will be examined by seeing if components of the composite key are enough to uniquely identify each entry. 

```
Create Table JobTypes(
    Company_ID VarChar(500) Non-Null, #Foreign Key to company

    Job_Title VarChar(100) Non-Null, 
    ...#here will be where skills are listed, a column for each skill type where it is boolean true or false, along with other requirements like "Over 18 required, boolean")

    Pay_Rate Decimal(32, 2),

    Constraint Job_ID Primary Key (Company_ID, Job_Title),

    Foreign Key (Company_ID) References Companies(Company_ID)
)
```

Above is JobTypes, the pieces of composite key are company_id and job_title.

Let us observe if each of the features requires both.

Any skill based features -> requires both: Company is not enough as different jobs at same company have different skill requirements. Job Title is not sufficent as the same job title may have different skill requirements depending on the company. 

Pay rate -> requires both: Company is not enough, different jobs can have different pay rates for same company. Job title is not enough, as the same job title can have different pay rates for different companies.

That is all covered, so JobTypes is 2NF.


```
Create Table Shifts( 
    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    End_Time DateTime Non-Null,

    People_Required Int,

    People_Available Int,

    Constraints Shift_ID Primary Key (Company_ID, Job_Title,  Location, Start_Time),

    Foreign Key (Company_ID, Job_Title) References JobTypes(Job_ID)
)
```


Shifts is above, the pieces of the composite key can be identified as the company id, job title, location, and start time. Notably, the company id and job title are somewhat already grouped together as job type.

end time -> All is required. 

Same job title, location, start may be a different length shift (and therefore different end time) depending on the comapny (ie: two offices in same building, one has a 6 hour shift and other has a 8), so end time does depend on company.

Same company, location, and start time may have different length shift, depending job title (ie: boss and shift workers stay different amounts of time at same office), so end time does depend on job title 

Same company, job title, and start time may have different length shift depending on location (ie: two offices for same company, and new one is a newer location so has longer workdays, so different end times)

Same company, job title, and location may have different end time depending on start time (ie: night shift and day shift have different end tiems but all same company, title, location)


people required/people available-> These two have the same logic so grouped.

Same job title, location, start time may have different manpower requirements depending on company (ie: Two accounting firms in same office, where one of them employs 10 and the other 8, the company is only way to distinguish)

Same company, location, start time may have different manpower requirements depending on job title (ie: Only one manager, but 8 shift workers for same location, company and start time)

Same company, job title, start time may have different manpower requirements depending on location (ie: Second office from same company with same types of workers may have less employees (perhaps newer office, so not as much talent hired yet))

Same company, job title, and location may have different manpower requirements depending on start time (ie: Day shift has 10 employees, night shift has 5 employees due to the fact less work expected at night)


So, shifts is 2NF. 


```
Create Table ShiftWorkers(

    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    Worker_ID VarChar(10) Non-Null, #Foreign Key to workers

    Worker_Arrived Boolean,

    Hours_Worked Float,

    Performance_Rating Int,

    Constraint Shift_ID Foreign Key (Company_ID, Job_Title, Location, Start_Time) References Shifts(Shift_ID)

    Constraint Shift_Worker_ID Primary Key (Shift_ID, Worker_ID) 
)
```

To make discussing this simpler, shift id composites will be treated together. When discussing, it will be treated as if the attribute depends on the "shift" which will then quality as the company, job, location, and start time as those are the required features to uniquely identify a shift. 

Worker_arrived -> Shifts have multiple workers that may arrive, and workers have multiple shifts, so both are needed to identify.

Hours_worker -> Shifts may have different workers end up working a different amount of the full hours of shift, so worker is required. Different workers may work different shifts where they work different number of hours, so depends on both.

Performance_rating -> Shifts may have multiple employees getting different performance ratings, so worker is required clearly. Same worker may get multiple different performance reviews depending on the shift, so shift is necessary. 


So all tables were in 2NF already, though some did a get bit complicated to think about due to the amount of keys involved in the composite key. 


3NF normalization is next:

For 3NF normalization, we look for transitive dependinces. That is, for a non-composite/primary key feature of the table has things that depend on it. 

We can see if A depends on B, by viewing if B is enough to identify A. So we can see if different records with A can have different B values. 

```
Create Table Workers(

    Worker_ID VarChar(10) Primary Key,

    First_Name VarChar(100),

    Last_Name VarChar(100),

    Middle_Name VarChar(100)
)
```

Does first name depend on last name or middle name? No, people with same first name can have different different last names or middle names. 

Does last name depend on last name or middle name? Logic above applies fairly simply.

Workers is in 3NF.

```
Create Table WorkerSchedules(
    Schedule_ID VarChar(30) Primary Key,

    Worker_ID VarChar(10), #Foreign key to worker

    Date DateTime,

    Start_Time Time,

    End_Time Time,

    Foreign Key (Worker_ID) References Workers(Worker_ID)
)
```

Date does not depend on worker, as multiple workers may select the same datetime. Same for start time and end time. 

Start time does not depend on date time, as multiple days can have same start time. Start times does not depend on end time;  multiple end times may exist for same end time.

End time does not depend on date, for same reason as start time. End time does not depend on start time, multiple start times exist for same end time.

Worker_id does not depend on date, worker can work for multiple days. Worker does not depend on start time, end time, as same worker can have multiple start or end times. 

So WorkerSchedules is in 3NF

```
Create Table Companies(
    Company_Name VarChar(500) Primary Key,

    Company_Founded DateTime,

    Company_Matches Int,

    Company_Revenue Decimal(32, 2),
)
```

Company founded date does not depend on company matches. Multiple companies with say 15 matches, and they have different company founded dates. There is nothing wrong with such an idea, though it may be rare for multiple companies to have the same number of matches.

Company founded date does not depend on company revenue. Multiple companies may have the same revenue, and they won't all be the same founding date (or at least not inherently). 

Company matches does not depend on company founded date. Companies with the same founding date does not tell the same number of matches.

Company matches does not depend on revenue. Knowing revenue does not tell you the number of matches. 

Revenue does not depend on founded date. Knowing the founding date does not tell you the revenue.

Revenue does not depend on company matches. Knowing the number of matches does not tell you the revenue. 

As it seems, I realized the above is a shorter way to describe a dependency than what I had been using. 

So companies is in 3NF

```
Create Table JobTypes(
    Company_ID VarChar(500) Non-Null, #Foreign Key to company

    Job_Title VarChar(100) Non-Null, 
    ...#here will be where skills are listed, a column for each skill type where it is boolean true or false, along with other requirements like "Over 18 required, boolean")

    Pay_Rate Decimal(32, 2),

    Constraint Job_ID Primary Key (Company_ID, Job_Title),

    Foreign Key (Company_ID) References Companies(Company_ID)
)
```

There is only one non-prime aspect to the table, so it can't depend on on any non-prime attributes, so in 3NF. 

```
Create Table Shifts( 
    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    End_Time DateTime Non-Null,

    People_Required Int,

    People_Available Int,

    Constraints Shift_ID Primary Key (Company_ID, Job_Title,  Location, Start_Time),

    Foreign Key (Company_ID, Job_Title) References JobTypes(Job_ID)
)
```

End time does not depend on people required or people available. Knowing the people required or people available cannot tell you the end time of the job.

People required does not depend on end time. Knowing the end time is not sufficent to tell you the people required. The same is true for people available. 

People required does not depend on people available. They are clearly related, but neither directly tell the other. People required is >= people available (reminder available means available/selected/recruited so far). 

This table is in 3NF then. 

```
Create Table ShiftWorkers(

    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    Worker_ID VarChar(10) Non-Null, #Foreign Key to workers

    Worker_Arrived Boolean,

    Hours_Worked Float,

    Performance_Rating Int,

    Constraint Shift_ID Foreign Key (Company_ID, Job_Title, Location, Start_Time) References Shifts(Shift_ID)

    Constraint Shift_Worker_ID Primary Key (Shift_ID, Worker_ID) 
)
```


Worker arrived actually does depend on hours worked, so we discovered a violation for 3NF. Knowing that a worker worked any hours >=0 tells you the worker arrived, and seeing the worker worked 0 hour tells you the worker did not arrive. So, to update for 3NF we ought to remove worker arrived, as it has strictly less information than worker arrived. 

```
Create Table ShiftWorkers(

    Company_ID VarChar(500) Non-Null, 

    Job_Title VarChar(100) Non-Null, 
 
    Location VarChar(500) Non-Null, 

    Start_Time DateTime Non-Null,

    Worker_ID VarChar(10) Non-Null, #Foreign Key to workers

    Hours_Worked Float,

    Performance_Rating Int,

    Constraint Shift_ID Foreign Key (Company_ID, Job_Title, Location, Start_Time) References Shifts(Shift_ID)

    Constraint Shift_Worker_ID Primary Key (Shift_ID, Worker_ID) 
)
```

Hours worked does not depend on performance rating. We can imagine there is some correlation where if a worker doesnt work full shift, it may be bad on performance rating, but a bad performance rating can occur for a less than full shift and vice versa. Same logic to show there is no opposite deppendency.


So now, with the updated table for shiftworkers, all tables are in 3NF.






Problem solving journey:

I apologize, but as I have not done the code/except for the in the abtract sense of what I want my app to be, I don't have much to list in my journey for discovering anomalies.

Functional dependcies have been listed throughout the 1NF, 2NF, and 3NF step by step normalization process. 

In lieu of having anomalies that I actually observed, I will write 2 examples of each type of anomaly that could have occured in a worse data structuring.

Insertion anomaly:

1 If companies was joined with jobtypes, they would not be able to create a company without also creating a job (maybe with null characterstics). This would be an insertion anomaly. 3NF dealt with this as job details would rely on job id alone, so transitive relation with company_id. Or we could say 2NF because company details would not rely on job_id if we made that a composite key.

2 If workers was joined with worker schedules, one would not be able to create their profile without also creating a schedule entry. This would be an insertion anomaly. This would be fixed by 2NF as the composite key would be schedule id and worker id, presumably, and the worker details like skills/age/name would not depend on the schedule id part of the composite key at all (so 2NF would force split). 

Deletion anomaly:

1 Suppose companies was joined with job types as a single table. If one company was to have only a single job type, if that job type was deleted, all of the companies detail's would be deleted as well. This would be a deletion anomaly and does not occur because of the companies and jobs being seperate tables (They were from the start, but 3NF would seperate them as the job details would rely on the job id alone, so transitive relation with company, so require fix. Or we could say 2NF because company details would not rely on job_id if we made that a composite key)

2 Suppose workers was joined with worker schedules as a single table. If a worker had only one existing schedule, and then decided to delete it as they changed their mind, it would delete their details. This would be espically annoying as that worker would need to re-enter everything. This would be a deletion anomaly. This would be avoided by 2NF as the composite key would be schedule id and worker id, presumably, and the worker details like skills/age/name would not depend on the schedule id part of the composite key at all (so 2NF would force the split)

Update anomaly:

1 Suppose that companies was joined with job types as a single table. If one was to have a job appear twice for a company, and we wanted to update the payrate, we may update one of the record's pay rates but not all of them. This would be an update anomaly which is avoided because of the 3NF (3NF would say to seperate as if they were joined, the job details would not rely on the company id, but the job id, so transitive. Or we could say 2NF because company details would not rely on job_id if we made that a composite key.
)

2 Suppose that shift workers was joined with shifts as a single table. If one was to update a specific worker's performance report, one may accidently update all of the workers for that shift's performance report, which would be an update anomaly. This would be less likely/impossible to occur in 3NF (if they were joined, 2NF would make them split as people required would not depend on worker id(and worker id would be part of the composite key))


# Check in 3 entries: