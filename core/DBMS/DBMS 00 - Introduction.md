# DBMS 00 - Introduction

## Navigation

- [1. Why Do We Need To Store Data?](#1-why-do-we-need-to-store-data)
- [2. What Is Data?](#2-what-is-data)
- [3. What Is Information?](#3-what-is-information)
- [4. What Is A Database?](#4-what-is-a-database)
- [5. Why Can't We Simply Use Files?](#5-why-cant-we-simply-use-files)
- [6. Problems With File Systems](#6-problems-with-file-systems)
- [7. What Is A DBMS?](#7-what-is-a-dbms)
- [8. How Does A DBMS Solve File System Problems?](#8-how-does-a-dbms-solve-file-system-problems)
- [9. Advantages Of DBMS](#9-advantages-of-dbms)
- [10. Data Independence](#10-data-independence)
- [11. Interview Questions](#11-interview-questions)
- [12. Final Mental Model](#12-final-mental-model)
---

# 1. Why Do We Need To Store Data?

Before learning databases,

ask yourself one simple question:

```text
Why do we store data at all?
```

Imagine you own a small shop.

Customers visit every day.

You write their details on paper.

```text
Customer

↓

Name

Phone Number

Purchased Item

Amount Paid
```

After one day,

you have a few records.

Easy.

---

After one year,

you may have:

```text
10,000 Customers
```

Now suppose someone asks:

```text
Show me

Rahul's Purchase

From

Last October.
```

Can you search thousands of papers quickly?

```text
No.
```

It would take a long time.

As businesses grow,

the amount of data grows too.

Humans cannot efficiently manage

millions of records manually.

Computers solve this problem.

---

## What Do We Actually Want?

Whenever we store data,

we expect to:

```text
Store It

↓

Find It Quickly

↓

Update It

↓

Delete It

↓

Keep It Safe
```

These five operations are the foundation of every database.

---

## Real-Life Examples

Your phone stores:

```text
Contacts

Messages

Photos

Videos

Call History
```

Instagram stores:

```text
Users

Posts

Followers

Comments

Likes
```

Amazon stores:

```text
Products

Customers

Orders

Payments
```

Banks store:

```text
Accounts

Customers

Transactions

Loans
```

Every modern application stores data.

The question is not

**whether** data should be stored,

but

**how** it should be stored efficiently.

---

## Memory Trick

```text
Store

↓

Search

↓

Update

↓

Delete

↓

Protect
```

These are the basic goals

of data storage.

---

# 2. What Is Data?

Data is the

smallest building block

of a database.

Think of data as:

```text
Raw Facts
```

Example:

```text
101

Arbaaz

21

CSE
```

These values exist,

but by themselves,

they don't tell us much.

We don't know:

```text
Is 101

A Roll Number?

Employee ID?

Room Number?
```

Similarly,

```text
95
```

Could mean:

```text
Marks

Temperature

Battery Percentage

Age
```

Without context,

they are simply:

```text
Data
```

---

## Definition

```text
Data

=

Raw Facts

Without Meaning.
```

---

## Memory Trick

```text
Raw Facts

↓

Data
```

# 3. What Is Information?

We learned that:

```text
Data

=

Raw Facts
```

But raw facts alone

are often useless.

Example:

```text
101

Arbaaz

21

CSE
```

Question:

What do these values mean?

```text
We Don't Know.
```

---

## Why Isn't Data Enough?

Suppose someone gives you:

```text
87
```

Can you answer:

```text
Marks?

Temperature?

Battery Percentage?

Age?
```

No.

Because data has **no context**.

Without context,

data cannot help us

make decisions.

---

## What Is Information?

Information is simply:

```text
Data

+

Meaning

+

Context
```

Once data is organized

and given meaning,

it becomes useful.

That useful data is called:

```text
Information
```

---

## Example

Suppose we organize

the earlier data.

```text
Roll No    : 101

Name       : Arbaaz

Age        : 21

Department : CSE
```

Now every value

has a purpose.

This is no longer

just raw data.

It has become:

```text
Information
```

---

## Another Example

Raw Data:

```text
95
```

Information:

```text
Student Marks

95 / 100
```

---

Raw Data:

```text
38
```

Information:

```text
Body Temperature

38°C
```

The value

didn't change.

Only the

meaning changed.

---

## Data vs Information

| Data | Information |
|------|-------------|
| Raw Facts | Processed Data |
| No Context | Has Context |
| Difficult To Understand | Easy To Understand |
| Input | Output |

---

## Real Life Example

Imagine your phone

shows:

```text
9876543210
```

Just a number.

Now your phone says:

```text
Mom

9876543210
```

Nothing about the number changed.

The phone simply

added meaning.

Now it has become:

```text
Information
```

---

## Why Is Information Important?

People and businesses

do not make decisions

using raw data.

They make decisions

using:

```text
Information.
```

For example:

A bank doesn't ask:

```text
What numbers

are stored?
```

It asks:

```text
Which customer

hasn't paid

their loan?
```

That requires

organized,

meaningful information.

---

# 4. What Is A Database?

Now suppose

a school has

only one student.

```text
Roll No : 101

Name    : Arbaaz

Age     : 21
```

Easy.

Everything fits

on one page.

---

Now imagine

the school has:

```text
50,000 Students
```

Each student has:

```text
Name

Roll Number

Phone

Email

Address

Marks

Attendance
```

Question:

Can we remember

all this information?

```text
No.
```

Can we search

through thousands

of papers quickly?

```text
No.
```

We need

a better way

to organize

large amounts

of information.

---

## Definition

A database is:

```text
An Organized Collection

Of Related Information.
```

Notice

two important words.

---

### Organized

The information

is stored

in a structured way.

Not randomly.

Example:

```text
Students

Teachers

Courses

Marks
```

Everything has

its proper place.

---

### Related

The stored information

belongs together.

Example:

```text
Student

↓

Course

↓

Marks
```

These records

are connected.

They are not

random pieces

of information.

---

## Real Life Databases

Your phone stores:

```text
Contacts

Messages

Photos
```

Instagram stores:

```text
Users

Posts

Followers

Likes

Comments
```

Amazon stores:

```text
Products

Customers

Orders

Payments
```

Banks store:

```text
Accounts

Customers

Transactions
```

Every modern application

uses a database.

---

## Database Is NOT DBMS

Many beginners think:

```text
Database

=

DBMS
```

This is wrong.

A database is only:

```text
Stored Information.
```

The software

that creates,

updates,

searches

and manages

that database

is called:

```text
DBMS
```

We will study that next.

---

## Memory Trick

```text
Raw Facts

↓

Data

↓

Meaning Added

↓

Information

↓

Large Organized Collection

↓

Database

↓

Managed By

↓

DBMS
```

# 5. Why Can't We Simply Use Files?

We now know what a database is.

A natural question arises:

```text
If computers already had files,

why did people invent DBMS?
```

This is one of the most important questions in DBMS.

To answer it,

let's first understand

how data was stored

before DBMS existed.

---

## How Did People Store Data Earlier?

Suppose you own a small college.

You want to store information about students.

Without a DBMS,

you might create a file like this:

```text
students.txt
```

Inside the file:

```text
101, Arbaaz, CSE, 21

102, Rahul, ECE, 20

103, Priya, CSE, 22
```

Everything is simply written

one line after another.

The operating system

only knows:

```text
This Is A File.
```

It does **not** know:

```text
Which Value Is Roll Number?

Which Value Is Name?

Which Value Is Age?
```

To the OS,

it is just

a sequence of bytes.

---

## Then Who Understands The Data?

The application itself.

Example:

```text
College Software

↓

Reads students.txt

↓

Splits Each Line

↓

Extracts

Roll Number

Name

Department

Age
```

The operating system

does not do this.

The application programmer

must write all the code.

---

## A Simple Analogy

Imagine someone gives you

this piece of paper:

```text
101

Arbaaz

21

CSE
```

Can you understand it?

Maybe.

Maybe not.

Now imagine

another person writes:

```text
Roll Number : 101

Name        : Arbaaz

Age         : 21

Department  : CSE
```

Much easier.

A DBMS works

like the second example.

A normal file

is closer to the first.

---

# 6. Problems With File Systems

Using files works

for very small programs.

But as data grows,

many problems appear.

These problems

are exactly why

DBMS was invented.

---

## Problem 1 - Data Redundancy

Redundancy means:

```text
Same Data

Stored Multiple Times.
```

Example:

Suppose the college has

two files.

```text
students.txt

fees.txt
```

Both store:

```text
Student Name

Phone Number
```

Now Arbaaz changes

his phone number.

Question:

How many files

must be updated?

```text
Both Files.
```

If one file

is forgotten,

different files

will contain

different phone numbers.

---

## Problem 2 - Data Inconsistency

Suppose:

```text
students.txt

↓

Phone Number

9876543210
```

But:

```text
fees.txt

↓

Phone Number

9999999999
```

Question:

Which one is correct?

Nobody knows.

This situation

is called:

```text
Data Inconsistency.
```

---

## Problem 3 - Difficult Searching

Suppose a file contains:

```text
5 Million Students
```

Question:

Find

all CSE students

whose marks are above 90.

With normal files,

the program must

read every record,

check every field,

and compare every value.

Searching becomes slow

and difficult.

---

## Problem 4 - Difficult Updates

Suppose:

```text
Department Name

Changes
```

You may need

to update

thousands of records

manually.

Large updates

become difficult.

---

## Problem 5 - Poor Security

Suppose a file contains:

```text
Employee Salaries
```

Question:

Can every employee

open this file?

If yes,

everyone can see

everyone else's salary.

Files alone

provide very limited

control over

who can access

which data.

---

## Problem 6 - No Concurrency Control

Suppose:

Two employees

edit the same file

at exactly

the same time.

```text
Employee A

↓

Updating Salary

------------------

Employee B

↓

Updating Salary
```

Question:

Whose changes

should be saved?

Without proper control,

one update

may overwrite

the other.

Some data

may be lost.

---

## Problem 7 - Poor Backup & Recovery

Suppose the computer

suddenly crashes.

Question:

Can the file

always be restored?

```text
Not Easily.
```

If the file

becomes corrupted,

important information

may be lost forever.

---

# Memory Trick

```text
Files

↓

Redundancy

↓

Inconsistency

↓

Slow Searching

↓

Difficult Updates

↓

Poor Security

↓

No Concurrency

↓

Poor Recovery
```

---

# Bridge To The Next Topic

People realized:

```text
Files

Work Well

For Small Programs.
```

But for:

```text
Banks

Hospitals

Airlines

Amazon

Instagram
```

they become

very difficult

to manage.

A better solution

was needed.

That solution is called:

```text
DBMS
```

We will study it next.

# 7. What Is A DBMS?

We have now seen

why storing data

only in files

creates many problems.

Question:

```text
Can we build

a software

that manages

all data for us?
```

The answer is:

```text
Yes.
```

That software is called:

```text
Database Management System

(DBMS)
```

---

## Definition

A DBMS is software that allows us to:

```text
Create

Store

Retrieve

Update

Delete

Manage

Data Efficiently.
```

Think of it as

a manager

that sits between

the user

and the database.

---

## Simple Architecture

```text
User

↓

Application

↓

DBMS

↓

Database
```

Notice:

The user

does **not**

directly access

the database.

Everything goes through

the DBMS.

---

## Real Life Example

Suppose you use Instagram.

When you press:

```text
Like
```

Does Instagram

directly modify

the database?

```text
No.
```

The request goes to:

```text
Instagram Server

↓

DBMS

↓

Database
```

The DBMS checks:

- Is the request valid?
- Is the user allowed?
- Is the data consistent?
- Can multiple users modify safely?

Only then

does it update

the database.

---

# 8. How Does A DBMS Solve File System Problems?

Earlier,

we had:

```text
Files

↓

Many Problems
```

Now see

how DBMS solves them.

---

## Data Redundancy

Files:

```text
Same Data

Stored Multiple Times.
```

DBMS:

Stores data

in a structured manner,

reducing duplication.

---

## Data Inconsistency

Files:

Different copies

may contain

different values.

DBMS:

Updates data

centrally,

keeping information

consistent.

---

## Searching

Files:

Programmer

searches manually.

DBMS:

Simply write:

```sql
SELECT *
FROM Students
WHERE Department='CSE';
```

The DBMS

does the searching.

---

## Security

Files:

Very limited control.

DBMS:

Allows permissions like:

```text
Read

Write

Update

Delete
```

Different users

can have

different permissions.

---

## Concurrency

Files:

Two users

may overwrite

each other's changes.

DBMS:

Uses

transactions

and locking

to safely handle

multiple users.

---

## Backup & Recovery

Files:

Crash may

lose data.

DBMS:

Maintains logs,

backups

and recovery mechanisms.

---

# Memory Trick

```text
Files

↓

Problems

↓

DBMS

↓

Solutions
```

---

# 9. Advantages Of DBMS


A DBMS provides:

```text
Reduced Redundancy

↓

Consistency

↓

Fast Searching

↓

Better Security

↓

Concurrency Control

↓

Backup & Recovery

↓

Data Sharing

↓

Easy Maintenance
```

---


# 10. Data Independence

Suppose

the DBA changes

how data

is physically stored.

Question:

Should every application

also change?

```text
No.
```

This is called:

```text
Data Independence.
```

The goal is:

```text
Storage Changes

Should Not

Break Applications.
```

We'll study

Logical

and

Physical

Data Independence

in detail later.

---

# 11. Interview Questions

### What Is A DBMS?

```text
Software

That Creates,

Stores,

Retrieves,

Updates

And Manages

Databases.
```

---

### Why Was DBMS Invented?

```text
To Solve

The Problems

Of File Systems.
```

---

### Database vs DBMS?

```text
Database

↓

Stored Data

-------------------

DBMS

↓

Software

That Manages

The Database.
```

---

### Name Four Advantages Of DBMS.

```text
Reduced Redundancy

↓

Consistency

↓

Security

↓

Backup & Recovery
```

---

### Who Is A DBA?

```text
The Person

Responsible For

Managing

The Database.
```

---

# 12. Final Mental Model

```text
Need To Store Data

↓

Data

↓

Information

↓

Database

↓

Files

↓

Problems

↓

DBMS

↓

Secure

Fast

Reliable

Data Management

↓

Modern Applications
```
