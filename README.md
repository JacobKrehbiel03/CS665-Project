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


# Check in 3 entries: