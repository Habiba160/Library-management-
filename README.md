
 Mini Library Management System & 
Relational Algebra Optimization 

 Project Overview 
This project showcases two database-focused tasks: 
● Task 1: Design and implementation of a Mini Library Management System using SQL 
(with ERD, stored procedures, functions, triggers, and indexes). 
● Task 2: Relational Algebra translation and query optimization using heuristic 
techniques. 

 Task 1: Mini Library Management System 

 Objective 
Design a mini-library management system and implement core operations using: 
● ERD design principles 
● Stored Procedures 
● Scalar Functions 
● Triggers 
● Indexing for performance 

 ERD Design 
Entities: 
● Books (BookID, Title, Author, CopiesAvailable, TotalCopies) 
● Members (MemberID, Name, Email, TotalBooksBorrowed, IsActive) 
● BorrowedBooks (BorrowID, MemberID, BookID, BorrowDate, DueDate, 
ReturnDate, IsReturned) 
Relationships: 
● A Member can borrow many books. 
● A Book can appear in many borrow records. 
Design Features: 
● Includes Cardinality & Participation Constraints. 
● Avoids fan traps and chasm traps. 

 SQL Implementation 

 Table Creation 
● CREATE TABLE statements for Books, Members, and BorrowedBooks with 
appropriate primary keys, foreign keys, and constraints. 
⚙
 Stored Procedure: BorrowBook 
Handles: 
● Checking if the member is active. 
● Checking if the book has available copies. 
● Inserting a record into BorrowedBooks. 
● Updating CopiesAvailable in Books. 
● Updating TotalBooksBorrowed in Members. 

 Indexing 
Indexes created to improve performance: 
● Books(BookID) 
● Members(MemberID) 

 These fields are commonly used for joins and lookups, making them ideal 
candidates for indexing. 

 Function: GetBooksBorrowed(MemberID) 
● Returns the number of books currently borrowed (and not yet returned) by a member. 

 Trigger: PreventBorrowIfNoCopies 
Prevents a borrowing attempt if: 
● CopiesAvailable = 0 
● OR the member is not active (IsActive = false). 

 Sample Data Insertion 
Includes inserting: 
● 3 sample books 
● 3 sample members 
● 5 borrowing attempts (some successful, some failing due to validations) 

 Task 2: SQL to Relational Algebra Conversion 

 Original SQL Query 
sql 
CopyEdit 
SELECT S.Name, C.Title, E.Grade 
FROM STUDENT S, COURSE C, ENROLLMENT E 
WHERE S.StudentID = E.StudentID 
AND C.CourseID = E.CourseID 
AND S.Major = 'Computer Science' 
AND C.Credits >= 3; 

 Initial Relational Algebra Expression (Unoptimized) 
mathematica 
CopyEdit 
π_{S.Name, C.Title, E.Grade} 
( 
σ_{S.StudentID = E.StudentID AND C.CourseID = E.CourseID AND S.Major 
= 'Computer Science' AND C.Credits >= 3} 
(STUDENT × COURSE × ENROLLMENT) 
) 

 Initial Query Tree Representation 
mathematica 
CopyEdit 
π_{S.Name, C.Title, E.Grade} 
| 
σ_{S.StudentID = E.StudentID AND C.CourseID = E.CourseID AND S.Major = 
'Computer Science' AND C.Credits >= 3} 
| 
STUDENT × COURSE × ENROLLMENT 

 Optimized Relational Algebra Expression 
mathematica 
CopyEdit 
π_{S.Name, C.Title, E.Grade} 
( 
  (σ_{S.Major = 'Computer Science'}(STUDENT) 
   ⋈_{S.StudentID = E.StudentID} ENROLLMENT) 
   ⋈_{C.CourseID = E.CourseID} 
   σ_{C.Credits >= 3}(COURSE) 
) 
 
 

 Optimized Query Tree 
mathematica 
CopyEdit 
π_{S.Name, C.Title, E.Grade} 
         | 
       ⋈_{C.CourseID = E.CourseID} 
      /                               \ 
     ⋈_{S.StudentID = E.StudentID}      σ_{C.Credits >= 3}(COURSE) 
    /                                \ 
σ_{S.Major = 'Computer Science'}(STUDENT)    ENROLLMENT 
 
 
 Optimization Steps 
● Selection Pushdown: Apply filtering (S.Major = 'Computer Science', 
C.Credits >= 3) early to reduce data size. 
 
● Join Conversion: Replace the Cartesian product with theta joins based on meaningful 
join conditions. 
 
● Join Ordering: Perform selective joins first to minimize intermediate table sizes. 
 
● Projection Late: Project only the necessary fields after all joins are completed. 
 
 
