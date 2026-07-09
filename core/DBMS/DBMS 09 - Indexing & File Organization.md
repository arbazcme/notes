# DBMS 09 - Indexing & File Organization

## Navigation

* [1. Why Do We Need Indexing?](#1-why-do-we-need-indexing)
* [2. File Organization](#2-file-organization)

  * [2.1 Heap Files](#21-heap-files)
  * [2.2 Sequential Files](#22-sequential-files)
  * [2.3 Hashed Files](#23-hashed-files)
* [3. Types of Indexes](#3-types-of-indexes)

  * [3.1 Primary Index](#31-primary-index)
  * [3.2 Secondary Index](#32-secondary-index)
  * [3.3 Dense Index](#33-dense-index)
  * [3.4 Sparse Index](#34-sparse-index)
  * [3.5 Multi-Level Index](#35-multi-level-index)
* [4. B-Tree](#4-b-tree)
* [5. B+ Tree](#5-b-tree)
* [6. Hash Index](#6-hash-index)
* [7. Clustered vs Non-Clustered Index](#7-clustered-vs-non-clustered-index)
* [Final Interview Revision](#final-interview-revision)


---

# 1. Why Do We Need Indexing?

So far,

we have learned

how databases

store data,

execute transactions,

recover from crashes,

and support

multiple concurrent users.

Question:

How does a database

find

one particular row

among

millions

or even

billions

of rows?

Imagine

this SQL query.

```sql
SELECT *

FROM Employee

WHERE EmployeeID = 987654;
```

Question:

How does MySQL

find

Employee 987654?

Does it

start

from

the first row

and

keep checking

every row

until it finds

the correct one?

If it did,

large databases

would become

extremely slow.

This problem

is solved by

```text
Indexing.
```

---

# The Problem

Suppose

your Employee table

contains

```text
10 rows.
```

Searching

is easy.

Even

checking

every row

takes almost

no time.

Now suppose

Google

stores

```text
10 Billion

web pages.
```

Or Amazon

stores

```text
500 Million

products.
```

Or your bank

stores

```text
200 Million

transactions.
```

Question

Can the DBMS

scan

every row

for every query?

```text
Absolutely not.
```

It would be

far too slow.

---

# Linear Search

Suppose

Employee Table

| ID | Name |
|----|------|
|1|Alice|
|2|Bob|
|3|Charlie|
|4|David|
|5|Emma|

Search

```text
ID = 5
```

Without

an Index,

the DBMS performs

```text
Check Row 1

↓

Check Row 2

↓

Check Row 3

↓

Check Row 4

↓

Check Row 5
```

Worst Case

```text
O(n)
```

---

Now imagine

```text
100 Million Rows.
```

Worst case

means checking

almost

every row.

That is called

```text
Full Table Scan.
```

---

# Why Is It Slow?

The biggest reason

is not

CPU speed.

It is

```text
Disk Access.
```

Reading data

from RAM

takes

nanoseconds.

Reading data

from SSD

takes

microseconds.

Reading data

from Hard Disk

takes

milliseconds.

Milliseconds

may seem small,

but

millions

of disk reads

become

very expensive.

Therefore,

DBMS designers

try to

minimize

Disk I/O.

This is

one of the biggest goals

of

Indexing.

---

# Real-World Analogy

Suppose

you have

a 1200-page

textbook.

Question

How do you find

the chapter

on

"Transactions"?

Option 1

```text
Read

Page 1

↓

Page 2

↓

Page 3

↓

...

↓

Page 1200
```

Terrible.

Option 2

Open

the

Index

at the end

of the book.

```
Transactions

↓

Page 542
```

Go directly

to

Page 542.

Much faster.

Database Indexes

work

exactly

the same way.

---

# What Is An Index?

An Index

is

a separate

data structure

that stores

```text
Search Key

↓

Pointer

to

Actual Data.
```

Instead of

searching

the entire table,

the DBMS

searches

the much smaller

Index.

Once

the correct entry

is found,

it immediately

jumps

to

the actual row.

---

Suppose

Employee Table

| ID | Name |
|----|------|
|101|Alice|
|102|Bob|
|103|Charlie|

The Index

may look like

| Key | Pointer |
|-----|----------|
|101|Address A|
|102|Address B|
|103|Address C|

When searching

for

```text
102
```

The DBMS

does

```text
Search Index

↓

Find Pointer

↓

Jump to Row
```

Instead of

reading

the entire table.

---

# Benefits Of Indexing

Without Index

```text
Search

↓

O(n)
```

With a good Index

```text
Search

↓

O(log n)

(B+ Tree)
```

or

```text
Almost O(1)

(Hash Index)
```

depending

on

the type

of Index.

---

# Does Every Column Need An Index?

No.

Indexes

consume

extra storage.

Also,

every

INSERT,

UPDATE,

and

DELETE

must update

the Index too.

Therefore,

Indexes improve

Read performance,

but

slightly reduce

Write performance.

---

Example

Suppose

EmployeeID

is indexed.

Searching

is fast.

But

every time

a new employee

is inserted,

the DBMS

must

```text
Insert Row

↓

Update Index
```

Two operations

instead of one.

---

# Trade-Off

Indexes

are

a classic trade-off.

```text
Faster Reads

↓

More Storage

↓

Slightly Slower Writes
```

Almost every

interviewer

asks

this question.

---

# Interview Questions

### Why do databases need Indexes?

```text
To reduce

search time

and

minimize

Disk I/O.
```

---

### What happens

without

an Index?

```text
Full Table Scan.
```

---

### Do Indexes

improve

INSERT?

```text
No.

They make

INSERT,

UPDATE,

and

DELETE

slightly slower,

because

the Index

must also

be updated.
```

---

### Why are Indexes

worth it then?

```text
Because

databases perform

far more

Reads

than

Writes.
```

---

# Mental Model

Imagine

a dictionary.

Without

alphabetical ordering,

finding

the word

```text
Database
```

would require

reading

every page.

Instead,

the dictionary

maintains

an Index

through

alphabetical order.

Databases

do exactly

the same thing,

except

the Index

is another

data structure.

---

# Bridge

Question

Where

is the data

actually stored?

Before learning

Indexes,

we must first understand

how

database files

are organized

on disk.

Different storage methods

lead to

different

search performance.

---

# 2. File Organization

An Index

does not

store

the actual data.

It simply

helps locate

the data.

Question:

Where

is the data

actually stored?

Answer:

Inside

Database Files.

The way

these files

are organized

is called

```text
File Organization.
```

Different organizations

have different

advantages.

---

# The Three Common Types

```text
1.

Heap Files

----------------

2.

Sequential Files

----------------

3.

Hashed Files
```

These describe

how records

are physically stored

on disk.

---

# 2.1 Heap Files

Heap

does NOT mean

the Heap

from

Operating Systems.

Here,

Heap simply means

```text
Unordered Storage.
```

Whenever

a new record

arrives,

the DBMS

places it

wherever

space is available.

Example

Insert

```text
Alice

↓

Bob

↓

Charlie

↓

David
```

Later,

insert

Emma.

The DBMS

simply appends

Emma

to

the next free location.

No sorting.

No ordering.

---

## Advantages

- Very fast insertion.
- Simple implementation.

---

## Disadvantages

Searching

requires

```text
Full Table Scan.
```

Worst case

```text
O(n)
```

---

# 2.2 Sequential Files

Now,

suppose

records

are stored

in sorted order.

Example

```text
101

↓

102

↓

103

↓

104

↓

105
```

Notice

everything

is ordered.

Searching

becomes

much faster.

Binary Search

is now possible.

However,

Insertion

becomes difficult.

Suppose

we insert

```text
103.5
```

The DBMS

may need

to shift

many records

to preserve order.

---

## Advantages

- Fast searching.
- Efficient range queries.

---

## Disadvantages

- Slow insertions.
- Slow deletions.

---

# 2.3 Hashed Files

Hashing

stores

records

based on

a

Hash Function.

Example

```text
Hash(EmployeeID)

↓

Bucket Number

↓

Store Record
```

Searching

can become

almost

```text
O(1)
```

Average Case.

However,

Hashing

is poor

for

Range Queries.

Example

```sql
WHERE Salary

BETWEEN

50000

AND

80000
```

Hashing

cannot efficiently

answer

such queries.

---

# Comparison

| Organization | Search | Insert | Range Query |
|--------------|---------|---------|-------------|
| Heap | Slow | Fast | Poor |
| Sequential | Fast | Slow | Excellent |
| Hash | Very Fast | Fast | Poor |

---

# Interview Questions

### What is a Heap File?

```text
An unordered

collection

of records.
```

---

### Which organization

is best

for

Range Queries?

```text
Sequential Files.
```

---

### Which organization

provides

almost O(1)

search?

```text
Hashing.
```

---

# Bridge

Now we know

how data

is physically stored.

Question

How can

the DBMS

search

these files

efficiently

without

scanning

every record?

The answer is

```text
Indexes.
```

Next,

we'll study

Primary,

Secondary,

Dense,

Sparse,

and

Multi-Level Indexes,

which eventually lead us

to

the most important structure

used in modern databases:

```text
B+ Trees.
```

# 3. Types of Indexes

In the previous topic,

we learned

that an Index

helps the DBMS

find records

without scanning

the entire table.

Question:

Are all Indexes

the same?

```text
No.
```

There are several

types of indexes,

each designed

for different situations.

The most important ones are

```text
1. Primary Index

2. Secondary Index

3. Dense Index

4. Sparse Index

5. Multi-Level Index
```

Understanding these

makes

B-Trees

and

B+ Trees

much easier.

---

# 3.1 Primary Index

Suppose

our Employee table

is physically stored

sorted by

EmployeeID.

| EmployeeID | Name |
|------------|------|
|101|Alice|
|102|Bob|
|103|Charlie|
|104|David|
|105|Emma|

Notice

the records themselves

are already

sorted.

Now,

the DBMS creates

an Index.

Instead of

storing

every record,

it stores

only enough information

to locate

blocks of records.

Example

```text
Index

101 → Block 1

104 → Block 2

107 → Block 3
```

Question

Why?

Because

the table

is already sorted.

The DBMS

doesn't need

an entry

for every row.

This Index

is called

```text
Primary Index.
```

---

## Definition

A Primary Index

is an Index

built on

the attribute

according to which

the data file

is physically ordered.

Usually,

this attribute

is the

Primary Key.

---

## Important Point

People often confuse

```text
Primary Key

and

Primary Index.
```

They are

not the same.

Primary Key

↓

Logical Constraint

Ensures uniqueness.

Primary Index

↓

Physical Data Structure

Used for searching.

A table

can have

a Primary Key

without

explicitly creating

a Primary Index.

Modern DBMSs

usually create one

automatically.

---

# Example

Suppose

records are stored

like this.

```text
101

102

103

104

105
```

Searching

ID = 104

The DBMS

uses

the Primary Index

to jump

directly

to the correct block,

instead of

checking

every record.

---

## Advantages

- Very fast searching.
- Excellent for range queries.
- Less storage than Dense Indexes.

---

## Disadvantages

- Requires ordered data.
- Insertions may require reorganization.

---

# 3.2 Secondary Index

Question

What if

the table

is ordered

by

EmployeeID,

but

users search

using

Department?

Example

```sql
SELECT *

FROM Employee

WHERE Department='CSE';
```

The Primary Index

cannot help much,

because

the table

is not ordered

by Department.

The solution

is another Index,

called

```text
Secondary Index.
```

---

## Example

Employee Table

| EmployeeID | Department |
|------------|------------|
|101|CSE|
|102|ECE|
|103|CSE|
|104|MECH|

Notice

CSE appears

multiple times.

The table

is NOT ordered

by Department.

A Secondary Index

might store

```text
CSE

↓

Pointers

↓

101

↓

103

----------------

ECE

↓

102

----------------

MECH

↓

104
```

Now,

searching

Department = CSE

becomes

very fast.

---

## Definition

A Secondary Index

is an Index

built on

an attribute

other than

the ordering attribute.

---

## Important Point

Unlike

Primary Indexes,

Secondary Indexes

usually need

one entry

for

every search key,

because

the records

are scattered

throughout the file.

---

## Advantages

- Fast searches

on non-primary columns.
- Multiple Secondary Indexes

can exist.

---

## Disadvantages

- Extra storage.
- Slower updates.

---

# Primary vs Secondary Index

| Primary | Secondary |
|----------|-----------|
| Built on ordering field | Built on non-ordering field |
| Usually one | Can be many |
| Less storage | More storage |
| Faster maintenance | Higher maintenance cost |

---

# 3.3 Dense Index

Question

Should

every record

have

an Index Entry?

Suppose

Employee Table

```text
101

102

103

104

105
```

Dense Index

looks like

```text
101

↓

Pointer

----------------

102

↓

Pointer

----------------

103

↓

Pointer

----------------

104

↓

Pointer

----------------

105

↓

Pointer
```

Notice

every record

has

one Index Entry.

This is called

```text
Dense Index.
```

---

## Definition

A Dense Index

contains

an Index Entry

for

every search key

in the data file.

---

## Advantages

- Very fast lookup.
- Direct access.
- Simple searching.

---

## Disadvantages

- Larger Index.
- More storage.
- More maintenance.

---

# 3.4 Sparse Index

Question

Do we really need

an Index Entry

for every row?

Not always.

Suppose

each disk block

stores

100 records.

Instead of

100 entries,

we keep

only

one.

Example

```text
Block 1

101

102

103

...

200

----------------

Block 2

201

202

203

...

300
```

Sparse Index

```text
101

↓

Block 1

----------------

201

↓

Block 2
```

Searching

ID = 150

Step 1

Find

largest key

≤ 150

↓

101

↓

Go to

Block 1

↓

Search only

inside

that block.

---

## Definition

A Sparse Index

contains

entries

only for

some search keys,

usually

the first key

of every block.

---

## Advantages

- Very small Index.
- Less storage.
- Easier maintenance.

---

## Disadvantages

- Slightly slower

than Dense Index.
- Requires

ordered files.

---

# Dense vs Sparse

| Dense | Sparse |
|--------|---------|
| Entry for every record | Entry for some records |
| Larger | Smaller |
| Faster | Slightly slower |
| More maintenance | Less maintenance |

---

# Why Not Just Use Sparse Everywhere?

Suppose

the data

is unordered.

Example

```text
500

120

700

300
```

Can one entry

represent

an entire block?

No.

Because

there is

no ordering.

Therefore,

Sparse Indexes

require

sorted files.

Dense Indexes

can work

even when

records

are unordered.

This is

a favourite

interview question.

---

# 3.5 Multi-Level Index

Suppose

our Dense Index

contains

20 million entries.

Question

How do we search

the Index itself?

Scanning

20 million

Index entries

is still slow.

The solution

is beautiful.

Build

another Index

on top

of

the first Index.

Example

```text
Level 2 Index

↓

Level 1 Index

↓

Data File
```

Now,

Level 2

helps locate

the correct part

of Level 1.

Then

Level 1

locates

the data.

Question

Suppose

Level 2

becomes huge?

Answer

Build

another Index

again.

Eventually,

we get

something

that looks

like

a tree.

This idea

naturally evolves into

```text
B-Trees

and

B+ Trees.
```

This is why

B+ Trees

are called

Multi-Level Indexes.

---

# Interview Questions

### What is a Primary Index?

```text
An Index

built on

the ordering attribute

of the data file.
```

---

### What is a Secondary Index?

```text
An Index

built on

a non-ordering attribute.
```

---

### Dense Index?

```text
One Index Entry

for

every record.
```

---

### Sparse Index?

```text
One Index Entry

for

some records,

usually

one per block.
```

---

### Which is faster?

```text
Dense Index.
```

---

### Which uses less storage?

```text
Sparse Index.
```

---

### Why do we need

Multi-Level Indexes?

```text
Because

the Index itself

can become very large.

Searching

the Index

also needs

to be efficient.
```

---

# Mental Model

Imagine

a university library.

One shelf

contains

100 books.

Instead of

listing

every book

at the entrance,

the librarian writes

only

the first book

of every shelf.

```text
Shelf 1

↓

Algorithms

----------------

Shelf 2

↓

Databases

----------------

Shelf 3

↓

Operating Systems
```

This is

a Sparse Index.

Now imagine

the library

has

100,000 shelves.

The librarian

creates

an Index

of shelves.

Then

an Index

of that Index.

Eventually,

the library

has

multiple levels

of Indexes.

That,

in essence,

is exactly

how

B+ Trees

work.

---

# Bridge

We have now reached

the most important topic

in DBMS Indexing.

Question

Why don't databases

use

Binary Search Trees?

Why not

AVL Trees?

Why not

Red-Black Trees?

Why did

almost every modern DBMS

choose

```text
B Trees

and

B+ Trees?
```

The next topic

answers

this question

in detail.

# 4. B-Tree

We have learned

that

Indexes

speed up searching.

Question:

Why don't databases

simply use

```text
Binary Search Tree?
```

Or

```text
AVL Tree?

Red-Black Tree?
```

These trees already provide

```text
O(log n)
```

search.

So why invent

another tree?

The answer is

```text
Disk I/O.
```

---

# The Real Cost

In DSA,

we usually count

comparisons.

```text
BST

↓

O(log n)
```

Looks excellent.

Databases,

however,

care much more about

```text
Disk Access.
```

Suppose

one node

of a Binary Search Tree

contains

only

one key.

Example

```text
          50
         /  \
       20    80
      / \    / \
    10 30 60 90
```

Searching

for

90

requires

visiting

multiple nodes.

If

each node

is stored

on disk,

every node visit

means

another

Disk Read.

Even though

there are only

four comparisons,

there may also be

four expensive

Disk I/Os.

---

# Why Is Disk I/O Expensive?

Approximate speeds

```text
CPU Register

↓

~1 ns

-------------------

RAM

↓

~100 ns

-------------------

SSD

↓

~100 μs

-------------------

Hard Disk

↓

~5-10 ms
```

Notice

Disk access

is

millions of times

slower

than CPU operations.

Databases therefore ask

```text
How can we reduce

the number

of Disk Reads?
```

Not

How do we reduce

comparisons?

---

# The Main Idea Behind B-Trees

Instead of storing

only

one key

per node,

store

many keys.

Example

Instead of

```text
      50
```

store

```text
20 | 35 | 50 | 70 | 90
```

inside

one node.

Now,

one Disk Read

examines

five keys,

not one.

Immediately,

the height

of the tree

becomes much smaller.

Smaller height

↓

Fewer Disk Reads.

---

# Structure Of A B-Tree

Unlike

a Binary Tree,

a B-Tree node

can have

many children.

Example

```text
             [30 | 60]

          /      |       \

     [10|20] [40|50] [70|80|90]
```

Notice

one node

stores

multiple keys.

The children

represent

ranges.

```text
<30

30-60

>60
```

---

# Properties Of A B-Tree

Every textbook

lists

many properties.

For interviews,

remember these.

---

## Property 1

Keys inside a node

are

always sorted.

Example

```text
10 | 25 | 40 | 80
```

Never

```text
40 | 10 | 80
```

---

## Property 2

Every child

represents

a value range.

Example

```text
20 | 50

↓

Child 1

<20

----------------

Child 2

20-50

----------------

Child 3

>50
```

---

## Property 3

All leaf nodes

exist

at the same level.

This keeps

the tree balanced.

Searching

never becomes

O(n).

Height

always remains

small.

---

## Property 4

Insertion

and

Deletion

automatically

split

or

merge nodes

to maintain

balance.

The user

never needs

to rebalance

the tree manually.

---

# Searching In A B-Tree

Suppose

we search

65.

Tree

```text
            [30 | 60]

         /      |      \

   [10|20] [40|50] [70|80]
```

Compare

```text
65

>

60
```

Go right.

Next node

```text
70|80
```

65

is not found.

Done.

Notice

only

two nodes

were read.

---

# Why Is Height Small?

Suppose

every node

stores

200 keys.

Instead of

Binary Tree

where

every node

has

2 children,

one node

now branches

into

201 children.

The tree

becomes

extremely wide.

Therefore,

its height

becomes

very small.

Example

```text
Binary Tree

1,000,000 records

↓

Height

≈20

--------------------

B-Tree

Order 200

↓

Height

≈3
```

Three Disk Reads

instead of twenty.

Huge improvement.

---

# B-Tree Insertion (High Level)

Suppose

the node

has space.

Simply insert

the key.

Example

```text
20|40|60

↓

Insert

50

↓

20|40|50|60
```

Easy.

---

Suppose

the node

is already full.

Example

```text
10|20|30|40
```

Insert

50.

Overflow occurs.

Solution

```text
Split

the node.
```

Middle key

moves upward.

Remaining keys

split into

left

and

right nodes.

This keeps

the tree balanced.

---

# Deletion

Deletion

is the opposite.

If

a node

contains

too few keys,

the DBMS

may

borrow

from

a sibling,

or

merge

two nodes.

Again,

balance

is preserved.

---

# Why Isn't B-Tree Perfect?

Question

Suppose

the database stores

millions

of records.

Where

should

the actual records

be stored?

Inside

every node?

That would

make

internal nodes

very large.

Searching

would become slower.

The solution is

```text
B+ Tree.
```

---

# B+ Tree

Almost every

modern DBMS

(MySQL,

PostgreSQL,

Oracle,

SQL Server)

uses

B+ Trees,

not ordinary

B-Trees.

Question

Why?

Because

B+ Trees

optimize

Disk Access

even further.

---

# Biggest Difference

B-Tree

stores

data

inside

every node.

Example

```text
Root

↓

Keys

+

Records
```

B+ Tree

stores

only

keys

inside

internal nodes.

Actual records

exist

only

inside

leaf nodes.

```text
Root

↓

Keys

↓

Leaf

↓

Actual Records
```

Internal nodes

become

much smaller.

Smaller nodes

↓

More keys

fit

inside one disk page.

More keys

↓

Lower tree height.

Lower height

↓

Fewer Disk Reads.

This single idea

is why

B+ Trees dominate

modern databases.

# 5. B+ Tree

In the previous topic,

we learned

that

B-Trees

reduce

Disk I/O

by storing

many keys

inside one node.

Question:

If B-Trees

are already good,

why do

MySQL,

PostgreSQL,

Oracle,

SQL Server,

MongoDB

and almost every modern DBMS

use

```text
B+ Trees
```

instead?

The answer is

```text
Better Disk Utilization

+

Faster Range Queries

+

Smaller Internal Nodes.
```

---

# B-Tree vs B+ Tree

This is

the single most important

DBMS interview topic.

Let's compare them.

## B-Tree

```text
          [40]

        /      \

   [20|30]   [60|80]

```

Notice

actual records

can exist

inside

every node.

Searching

may stop

at

an internal node.

---

## B+ Tree

```text
              [40]

          /          \

      [20|30]      [60|80]

           ↓            ↓

---------------------------------

10 20 30 40 60 80 90

(Leaf Nodes)

---------------------------------
```

Internal nodes

contain only

```text
Keys

+

Pointers
```

Actual records

exist

**only**

inside

the Leaf Nodes.

This is

the biggest difference.

---

# Why Store Records Only In Leaves?

Question

Suppose

every internal node

also stores

entire records.

Each node

becomes

very large.

Large node

↓

Fewer keys

fit inside

one Disk Page.

↓

Tree becomes taller.

↓

More Disk Reads.

Instead,

remove

all records

from internal nodes.

Now

internal nodes

contain

only keys.

Much smaller.

Much more compact.

Much wider.

Much shorter.

Much faster.

---

# Linked Leaf Nodes

This is

the second

big difference.

Every Leaf Node

is linked.

Example

```text
Leaf 1

↓

Leaf 2

↓

Leaf 3

↓

Leaf 4
```

Like

a Linked List.

Question

Why?

To support

Range Queries.

---

# Why Are Range Queries Fast?

Suppose

SQL

asks

```sql
SELECT *

FROM Employee

WHERE Salary

BETWEEN

50000

AND

80000;
```

B+ Tree

works like this.

Step 1

Find

50000.

Step 2

Follow

the linked leaves.

```text
50000

↓

51000

↓

52000

↓

53000

↓

...

↓

80000
```

No need

to restart

the search

for every value.

Extremely fast.

---

Question

Can Hashing

do this?

```text
No.
```

Hashing

destroys ordering.

B+ Trees

preserve ordering.

That is why

databases

love

B+ Trees.

---

# Searching

Searching

works almost

the same

as

B-Trees.

Suppose

search

65.

```text
Root

↓

Internal Node

↓

Leaf Node

↓

Record Found
```

Time Complexity

```text
O(log n)
```

---

# Insertions

Suppose

the Leaf

has space.

Simply insert.

Suppose

it becomes full.

Split.

Promote

the middle key

to the parent.

Tree remains balanced.

Exactly

like

B-Trees.

---

# Deletion

Delete

the key.

If

underflow occurs,

borrow

from

siblings.

Otherwise,

merge.

Balance

is automatically maintained.

---

# Why B+ Trees Are Preferred

Reason 1

Internal nodes

are smaller.

↓

More keys

per node.

↓

Smaller height.

↓

Fewer Disk Reads.

---

Reason 2

Leaf Nodes

are linked.

↓

Excellent

Range Queries.

---

Reason 3

Every search

always ends

at a Leaf.

↓

Predictable performance.

---

Reason 4

Very cache friendly.

Modern CPUs

and DBMSs

benefit

from

this layout.

---

# Interview Questions

### Why don't databases use BST?

```text
BST

becomes tall

and

causes

too many

Disk Reads.
```

---

### Why not AVL Tree?

```text
Still

only

2 children

per node.

Tree height

remains

much larger

than

B+ Tree.
```

---

### Why not Red-Black Tree?

```text
Better than BST,

but

still

binary.

Disk I/O

remains high.
```

---

### Why B+ Tree?

```text
High Fan-Out

↓

Small Height

↓

Very Few

Disk Reads.
```

---

### Why are Leaf Nodes linked?

```text
Fast

Range Queries.
```

---

### Where are

actual records

stored?

```text
Only

in

Leaf Nodes.
```

---

# B-Tree vs B+ Tree

| B-Tree | B+ Tree |
|---------|----------|
| Records in every node | Records only in leaves |
| Leaves not linked | Leaves linked |
| Good range queries | Excellent range queries |
| Larger internal nodes | Smaller internal nodes |
| Rare today | Used by almost every DBMS |

---

# 6. Hash Index

Question

Can we make

search

even faster

than

O(log n)?

Sometimes

Yes.

Using

```text
Hashing.
```

---

# Main Idea

Instead of

ordering keys,

compute

```text
Hash(Key)

↓

Bucket

↓

Record
```

Example

```text
Hash(101)

↓

Bucket 4
```

Searching

becomes

almost

```text
O(1)
```

Average Case.

---

# Example

```text
Hash(EmployeeID)

↓

Bucket

↓

Record
```

Need

Employee 105.

Compute

```text
Hash(105)

↓

Bucket 3

↓

Record Found.
```

---

# Advantages

- Extremely fast equality search.
- Simple lookup.
- Excellent for

```sql
WHERE ID=100
```

---

# Disadvantages

Hashing

cannot answer

```sql
Salary

BETWEEN

50000

AND

80000
```

Because

ordering

is lost.

Hashing

also suffers

from

```text
Collisions.
```

Multiple keys

may map

to

the same bucket.

---

# B+ Tree vs Hash Index

| B+ Tree | Hash |
|-----------|------|
| O(log n) | O(1) average |
| Ordered | Unordered |
| Excellent Range Queries | Poor Range Queries |
| Used most often | Used for equality search |

---

# Interview Question

### When should Hash Index be used?

```text
Equality Search.
```

Example

```sql
WHERE ID=100
```

---

### When should B+ Tree be used?

```text
Range Queries

Sorting

ORDER BY

BETWEEN

<

>

<=

>=
```

---

# 7. Clustered vs Non-Clustered Index

Another

very common

interview question.

---

## Clustered Index

The table

itself

is stored

in

the same order

as

the Index.

Example

```text
Index

↓

101

102

103

104

↓

Actual Table

101

102

103

104
```

Both

follow

the same order.

Usually

only

one

Clustered Index

can exist.

---

## Non-Clustered Index

The table

is stored

one way.

The Index

is stored

another way.

Example

Table

```text
101

105

102

104
```

Index

```text
101

102

104

105
```

Index entries

point

to

actual records.

Many

Non-Clustered Indexes

may exist.

---

# Comparison

| Clustered | Non-Clustered |
|------------|---------------|
| Data physically ordered | Separate Index |
| Usually one | Many |
| Faster Range Queries | Good point lookups |

---

# Frequently Asked Interview Questions

### Why do databases use B+ Trees?

```text
Small Height

↓

Very Few

Disk Reads

↓

Fast Range Queries.
```

---

### Why are internal nodes small?

```text
They store

only keys

and pointers.
```

---

### Why are leaves linked?

```text
To support

efficient

Range Queries.
```

---

### Hash or B+ Tree?

```text
Equality

↓

Hash

-------------------

Range

↓

B+ Tree
```

---

# Entire DBMS09 Mental Model

```text
Huge Table

↓

Need Fast Search

↓

Index

↓

Primary

Secondary

↓

Dense

Sparse

↓

Multi-Level Index

↓

B-Tree

↓

B+ Tree

↓

Very Small Height

↓

Very Few

Disk Reads
```

---

# Final Interview Revision

## Heap File

```text
Unordered.

Fast Insert.

Slow Search.
```

---

## Sequential File

```text
Ordered.

Fast Search.

Slow Insert.
```

---

## Hash File

```text
Hash Function.

Very Fast Equality Search.
```

---

## Primary Index

```text
Built

on

ordering field.
```

---

## Secondary Index

```text
Built

on

non-ordering field.
```

---

## Dense Index

```text
Entry

for

every record.
```

---

## Sparse Index

```text
Entry

for

some records.
```

---

## Multi-Level Index

```text
Index

on

another Index.
```

---

## B-Tree

```text
Multiple Keys

per node.

Balanced.

Low Height.
```

---

## B+ Tree

```text
Records

only

in leaves.

Leaves linked.

Excellent

Range Queries.
```

---

## Hash Index

```text
Equality Search.

No Range Queries.
```

---

## Clustered Index

```text
Table

stored

in Index order.
```

---

## Non-Clustered Index

```text
Separate Index

points

to

actual records.
```

---

# 🎉 DBMS09 Complete

Congratulations!

You have now covered the complete DBMS roadmap:

```text
DBMS00

↓

Introduction

↓

ER Model

↓

Relational Model

↓

SQL

↓

Normalization

↓

Transactions

↓

Concurrency Control

↓

Recovery

↓

Indexing
```
