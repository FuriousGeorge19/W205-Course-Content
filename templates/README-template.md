
## Course Description

Storing, managing, and processing datasets are foundational processes in data
science. This course introduces the fundamental knowledge and skills of data
engineering that are required to be effective as a data scientist. This course
focuses on the basics of data pipelines, data pipeline flows and associated
business use cases, and how organizations derive value from data and data
engineering.  As these fundamentals of data engineering are introduced,
learners will interact with data and data processes at various stages in the
pipeline, understand key data engineering tools and platforms, and use and
connect critical technologies through which one can construct storage and
processing architectures that underpin data science applications.

## Course Format

The course is organized as an online inverted classroom. During each week, students first
work through a set of asynchronous materials, including video lectures, readings, and other
activities. Once a week, students meet for a 90-minute live session, in which they connect with
an instructor and other students in an online classroom. A functioning webcam and an audio
headset are required to participate in the live sessions. Students must complete all assigned
asynchronous material before the scheduled live session begins.

## Course Objectives

### Tools and Technologies

Students will:

- Build practical experience building data pipelines.
- Build practical experience cleaning, anonymizing, and plumbing data.
- Learn tooling for queries and query management (e.g., BigQuery, SQL).
- Learn tooling for analytics (jupyter, python, py-based-libs).
- Get exposure to advanced tooling for analytics (spark, kafka, etc).
- Learn how to leverage revision control.
- Learn how to use docker to assemble common tools for analysis.
- Build practical experience leveraging cloud-based resources for data analytics.
- Build practical experience consuming data and services from APIs.
- Get exposure to events and event-log based analytics.

### Concepts

Students will:

- Learn to keep their analysis grounded in business relevance.
- Get exposure to some basic distributed storage and compute concepts.
- Get exposure to some basic RDBMS concepts.
- Get exposure to RDB -vs- NoSQL tooling and approaches.
- Get exposure to some basic data warehousing concepts.
- Learn the basics of virtualization and containerization.
- Understand how analysis changes wrt scale / complexity of data.
- Learn about data partitioning.
- Learn about latency in data analysis.
- Get exposure to ETL -vs- NoETL.
- Learn the basic concepts of web-based applications.
- Understand how basic data privacy, security, and chain-of-custody works.


## Course Tools

In this class we will be using cloud instances on Google Cloud Platform (GCP).
We will set these up in the first week of class, but if you'd like to get a head
start, here are [instructions](http://www.example.com/) for how to do that.

To follow those instructions, you'll need the following Google Cloud Storage
URL for our current w205 student tools image:

    gs://mids-w205/mids-w205-tools-bionic-1553717198.tar.gz


## Evaluation & Grading

### Assignments

There are 12 assignments that cumulatively form 3 projects:

- Query Project
- Tracking User Activity
- Understanding User Behavior

Each assignment will be a part of one of these projects.

These assignments are the core of this course. Your work on them is one of the
best ways for you to learn.  Each assignment will be a hands-on lab that is
completed both individually and revisited during the synchronous weekly group
sessions. Ten of the twelve assignments will be graded.

### Due Dates for Assignments

To allow you to get the most out of each assignment, we'll work on them
iteratively and, at each instructor's discretion, in groups.
For example, for Assignment 1:

1. you'll work in groups and hear from us about the content of the assignment
   in Week 1
2. you'll work on it independently and/or in groups during the week, coming to
   Week 2's synchronous session with an attempt at the assignment and any
   questions you have
3. we will review it and address questions during Week 2's synchronous session
4. the final version of the assignment will be due the end of week 2


|   Assignment | Due Date    |
|         ---: | :---------- |
|       signup | Will not be graded – does not count - provided for students to practice with |
|           01 | Friday, January 18th, 2019, 11:59 pm Berkeley time |
|           02 | Friday, January 25th, 2019, 11:59 pm Berkeley time |
|           03 | Friday, February 1st, 2019, 11:59 pm Berkeley time |
|           04 | Friday, February 8th, 2019, 11:59 pm Berkeley time |
|           05 | Friday, February 15th, 2019, 11:59 pm Berkeley time |
|           06 | Friday, February 22nd, 2019, 11:59 pm Berkeley time |
|           07 | Friday, March 1st, 2019, 11:59 pm Berkeley time |
|           08 | Friday, March 8th, 2019, 11:59 pm Berkeley time |
|           09 | Friday, March 15th, 2019, 11:59 pm Berkeley time |
|           10 | Friday, March 22nd, 2019, 11:59 pm Berkeley time |
| Spring Break | no class – nothing due |
|           11 | Friday, April 5th, 2019, 11:59 pm Berkeley time |
|           12 | Friday, April 19th, 2019, 11:59 pm Berkeley time (extra week provided for advanced options) |

No work may be accepted after Friday, April 19th, 2019, 11:59 pm Berkeley time
(Unless an incomplete has been approved due to extreme circumstances by the
Dean’s office).

The dates above are provided as a default reference.  They may vary by section,
so check with your section instructor for definitive due dates.


### How Grades are Assigned

Each assignment is scored 0-10 according to:

- No late submissions (why we drop the lowest two assignment grades)
- Unless otherwise stated in the assignment, GitHub must be used from the git
  command line utility and follow proper source code control (penalty -1)
- Submissions in markdown should all be professionally formatted (penalty -1)
- All data in table format must be formatted in markdown or Pandas (penalty -1)
- For assignments 6 through 12, all steps in class must be repeated and
  separately annotated, including python and pyspark steps. Grader will
  estimate the percentage of steps missing or not separately annotated and
  assess a penalty of -1 for anything missing and -1 for each additional 10%
  missing

Your overall final semester grade will calculated as follows:

| Semester Letter Grade   |   Rubric   |
| :---: | :------------------- |
| A+ | The average of the highest 10 of the 12 assignments should be 9.7000 or higher without rounding |
| A  | The average of the highest 10 of the 12 assignments should be 9.3000 or higher without rounding |
| A- | The average of the highest 10 of the 12 assignments should be 9.0000 or higher without rounding |
| B+ | The average of the highest 10 of the 12 assignments should be 8.7000 or higher without rounding |
| B  | The average of the highest 10 of the 12 assignments should be 8.3000 or higher without rounding |
| B- | The average of the highest 10 of the 12 assignments should be 8.0000 or higher without rounding |
| Below B- | Instructor’s discretion, but no harsher than the pattern above |

These may vary by section, so check with your instructor for definitive grading
policies.


## Readings

Most readings are available through a subscription to
https://www.safaribooksonline.com/. Other readings are blog posts and links.


## Prerequisites

- Previous experience with Python
- Basic knowledge of Unix/Linux commands and tools as well as concepts such as
  processes, file systems
- In addition we'll use Docker, Git, and SQL as well as other tools
- If you feel like you're not where you'd like to be with these
  technologies/tools, here are some resources to get up to speed. There are
  options, pick which one best suits your needs

#### SQL

    SQL Tutorial
    w3schools.com
    https://www.w3schools.com/sql/default.asp

    Learning SQL, 2nd Edition
    by Alan Beaulieu
    https://www.safaribooksonline.com/library/view/learning-sql-2nd/9780596801847/

#### The Command Line

	Learning the Shell
	by William E. Shotts, Jr.
	http://linuxcommand.org/lc3_learning_the_shell.php

#### Git

	Pro Git book
	by Scott Chacon and Ben Straub 
	https://git-scm.com/book/en/v2

#### Python

	Python for Data Analysis, 2nd Edition
	by William Wesley McKinney
	https://www.safaribooksonline.com/library/view/python-for-data/9781491957653/

#### Docker

	Getting Started with Docker
	https://docs.docker.com/get-started/

	Using Docker
	by Adrian Mouat
	https://www.safaribooksonline.com/library/view/using-docker/9781491915752/

## Course Outline

The course consists of 4 sections:

-   [Introduction](#introduction)
    -   [Week 01 - Introduction](#week-01---introduction)
    -   [Week 02 - Working with Data](#week-02---working-with-data)
    -   [Week 03 - Welcome to the Queryside](#week-03---welcome-to-the-queryside)
-   [The Basics](#the-basics)
    -   [Week 04 - Storing Data](#week-04---storing-data)
    -   [Week 05 - Storing Data II](#week-05---storing-data-ii)
    -   [Week 06 - Transforming Data](#week-06---transforming-data)
    -   [Week 07 - Sourcing Data](#week-07---sourcing-data)
    -   [Week 08 - Querying Data](#week-08---querying-data)
-   [Streaming](#streaming)
    -   [Week 09 - Ingesting Data](#week-09---ingesting-data)
    -   [Week 10 - Transforming Streaming Data](#week-10---transforming-streaming-data)
    -   [Week 11 - Storing Data III](#week-11---storing-data-iii)
    -   [Week 12 - Querying Data II](#week-12---querying-data-ii)
-   [Putting it All Together](#putting-it-all-together)
    -   [Week 13 - Understanding Data](#week-13---understanding-data)
    -   [Week 14 - Patterns for Data Pipelines](#week-14---patterns-for-data-pipelines)


A 3-week Introduction that covers the basics of storage and retrieval concepts
and tools; a 5-week Basics section that provides a deeper exploration of
working with data and data pipelines; a 4-week section that focuses on
Streaming Data; and a concluding section, Putting it All Together, that
integrates concepts and skills from the entire course into a cohesive model of
the data pipeline.

In addition to the sequenced material covered, the course also includes
Tutorial materials that focus on technical skills associated with data
engineering technologies, tools, and platforms. These tutorials also provide a
practical foundation for the discussions and activities that will take place in
the live classroom for specific weeks in the term.

