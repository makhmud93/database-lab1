# Part 4: Normalization Workshop

## Task 4.1: Denormalized Table Analysis

### Given Table

StudentProject(
StudentID, StudentName, StudentMajor, ProjectID, ProjectTitle,
ProjectType, SupervisorID, SupervisorName, SupervisorDept,
Role, HoursWorked, StartDate, EndDate
)

---

## 1. Functional Dependencies

The functional dependencies are:

- StudentID → StudentName, StudentMajor
- ProjectID → ProjectTitle, ProjectType, SupervisorID, StartDate, EndDate
- SupervisorID → SupervisorName, SupervisorDept
- (StudentID, ProjectID) → Role, HoursWorked

---

## 2. Problems

The table has data redundancy because student, project, and supervisor information can be repeated in multiple rows.

### Update Anomaly

If a supervisor changes their department, the department must be updated in every row containing that supervisor.

### Insert Anomaly

A new project cannot be added if there is no student assigned to the project yet.

### Delete Anomaly

If we delete the only student working on a project, we may also lose information about that project.

---

## 3. First Normal Form (1NF)

The table is already in 1NF because all attributes contain atomic values and there are no repeating groups.

Therefore, no changes are required for 1NF.

Primary Key:

(StudentID, ProjectID)

---

## 4. Second Normal Form (2NF)

The table violates 2NF because there are partial dependencies.

- StudentID → StudentName, StudentMajor
- ProjectID → ProjectTitle, ProjectType, SupervisorID, StartDate, EndDate

### 2NF Decomposition

#### Student

Student(
StudentID PK,
StudentName,
StudentMajor
)

#### Project

Project(
ProjectID PK,
ProjectTitle,
ProjectType,
SupervisorID,
StartDate,
EndDate
)

#### StudentProject

StudentProject(
StudentID PK/FK,
ProjectID PK/FK,
Role,
HoursWorked
)

---

## 5. Third Normal Form (3NF)

There is a transitive dependency:

- ProjectID → SupervisorID
- SupervisorID → SupervisorName, SupervisorDept

Therefore:

ProjectID → SupervisorName, SupervisorDept

This violates 3NF.

### Final 3NF Decomposition

#### Student

Student(
StudentID PK,
StudentName,
StudentMajor
)

#### Supervisor

Supervisor(
SupervisorID PK,
SupervisorName,
SupervisorDept
)

#### Project

Project(
ProjectID PK,
ProjectTitle,
ProjectType,
SupervisorID FK,
StartDate,
EndDate
)

#### StudentProject

StudentProject(
StudentID PK/FK,
ProjectID PK/FK,
Role,
HoursWorked
)

---

# Task 4.2: Advanced Normalization

### Given Table

CourseSchedule(
StudentID, StudentMajor, CourseID, CourseName,
InstructorID, InstructorName, TimeSlot, Room, Building
)

---

## 1. Primary Key

The primary key is:

(StudentID, CourseID, TimeSlot)

This is a composite key because one student can enroll in multiple courses, and a course can have different sections or time slots.

---

## 2. Functional Dependencies

The functional dependencies are:

- StudentID → StudentMajor
- CourseID → CourseName
- InstructorID → InstructorName
- (Room, TimeSlot) → Building
- (CourseID, TimeSlot) → InstructorID, Room

---

## 3. BCNF Check

The table is NOT in BCNF.

BCNF requires that for every non-trivial functional dependency X → Y, X must be a superkey.

For example:

StudentID → StudentMajor

StudentID is not a superkey because one student can enroll in multiple courses.

Also:

CourseID → CourseName

CourseID is not a superkey because one course can have multiple students.

Therefore, the table violates BCNF.

---

## 4. BCNF Decomposition

We decompose the relation based on the functional dependencies.

### Step 1: Student Information

From:

StudentID → StudentMajor

we create:

Student(
StudentID PK,
StudentMajor
)

### Step 2: Course Information

From:

CourseID → CourseName

we create:

Course(
CourseID PK,
CourseName
)

### Step 3: Instructor Information

From:

InstructorID → InstructorName

we create:

Instructor(
InstructorID PK,
InstructorName
)

### Step 4: Course Section

From:

(CourseID, TimeSlot) → InstructorID, Room

we create:

CourseSection(
CourseID PK/FK,
TimeSlot PK,
InstructorID FK,
Room
)

### Step 5: Room Information

From:

(Room, TimeSlot) → Building

we create:
RoomSchedule(
Room PK,
TimeSlot PK,
Building
)

### Step 6: Enrollment

The relationship between students and course sections is:

Enrollment(
StudentID PK/FK,
CourseID PK/FK,
TimeSlot PK/FK
)

---

## 5. Potential Loss of Information

The decomposition is lossless because the relations preserve the original functional dependencies and are connected through common attributes.

The original information can be reconstructed by joining:

- Enrollment with Student
- Enrollment with CourseSection
- CourseSection with Instructor
- CourseSection with RoomSchedule

Therefore, no important information is lost.

The BCNF decomposition reduces data redundancy and helps prevent update, insert, and delete anomalies.
