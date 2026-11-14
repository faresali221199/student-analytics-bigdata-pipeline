# 🎓 Student Data Engineering Pipeline

## 📌 Project Overview

This project implements an end-to-end Data Engineering pipeline that extracts student information, academic grades, and attendance records from MongoDB, processes the data using PySpark, and stores it in HDFS using the **Medallion Architecture (Bronze, Silver, and Gold)**.

Apache Hive provides external tables for querying the processed datasets, while Microsoft Power BI connects to the analytical data to build an interactive Student Performance Dashboard.

The project demonstrates how to build a scalable data pipeline that transforms raw data into meaningful insights for academic performance and attendance monitoring.

## 🏗️ Architecture

![image.png](./Images/Untitled%20Diagram%20(3).gif)

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| MongoDB | Source database for student information, grades, and attendance |
| Python | Data extraction and pipeline development |
| PySpark | Distributed data processing, cleaning, transformation, and integration |
| HDFS | Distributed storage for the Bronze, Silver, and Gold layers |
| Apache Hive | SQL querying and external table management |
| Docker & Docker Compose | Running the data engineering environment |
| Power BI Desktop | Interactive dashboards and analytical reporting |

## 📂 Source Database

**Database:** `school` (MongoDB)

The database contains three collections:

- **students:** `student_id`, `name`, `age`, `city`, `gpa`
- **grades:** `student_id`, `math`, `python`
- **attendances:** `student_id`, `date`, `status` (Present/Absent)

## 🥉 Bronze Layer — Raw Data

**Storage:** HDFS

Stores raw data extracted from MongoDB using PySpark, preserving source values.

```text
/bronze/school/students/
/bronze/school/grades/
/bronze/school/attendances/
```

## 🥈 Silver Layer — Cleaned Data

**Storage:** HDFS | **Processing:** PySpark

Cleans and validates data by handling missing values, removing duplicates, standardizing data types, and checking data quality.

```text
/silver/school/students/
/silver/school/grades/
/silver/school/attendances/
```

## 🥇 Gold Layer — Integrated Data

**Storage:** HDFS | **Processing:** PySpark

Joins the cleaned datasets using `student_id` to create a single analytical dataset containing student details, grades, and attendance.

```text
/gold/school/student_analytics/
```

### Gold Table Schema

| Column | Description |
|---|---|
| `student_id` | Student identifier |
| `name` | Student name |
| `age` | Student age |
| `city` | Student city |
| `gpa` | Student GPA |
| `math` | Mathematics score |
| `python` | Python score |
| `date` | Attendance date |
| `status` | Attendance status |

### Example Analytical Questions

The Gold dataset can support analysis such as:

- Which students have the highest GPA?
- What is the average Mathematics score?
- What is the average Python score?
- Which students were absent?
- Which cities have the highest average GPA?
- Is attendance associated with academic performance?

## 🗄️ Apache Hive Integration

Apache Hive provides SQL access to the datasets stored in HDFS through external tables.

Create an external table for each Bronze, Silver, and Gold dataset, pointing each table to its corresponding HDFS directory.

Example:

```sql
CREATE EXTERNAL TABLE students_silver (
    student_id INT,
    name STRING,
    age INT,
    city STRING,
    gpa DOUBLE
)
STORED AS PARQUET
LOCATION '/silver/school/students';
```

Repeat the process for the Silver grades, Silver attendance, and Gold analytical datasets using their corresponding schemas and HDFS locations.

**Why external tables?**

- Hive can query files already stored in HDFS.
- Data does not need to be copied into a separate Hive-managed storage directory.
- The HDFS data remains accessible to other compatible processing tools.
- Hive tables provide a SQL interface for downstream analytics.

The Gold external table acts as the primary dataset for the Power BI dashboard.
## 📊 Power BI Dashboard Design

**Dashboard Title:** Student Performance & Attendance Analytics

**Design:** Clean white background, dark navy titles, blue accents, and consistent spacing.

### Dashboard — Student Overview

**KPI Cards:**
- Total Students
- Average GPA
- Average Mathematics Score
- Average Python Score
- Attendance Rate

**Visualizations:**
- **Students by City:** Bar chart showing student distribution.
- **Academic Performance:** Clustered bar chart comparing Mathematics and Python scores.
- **Attendance Distribution:** Donut chart showing Present vs. Absent records.
- **Student Summary:** Table containing name, city, GPA, grades, and attendance status.

**Slicers:** City, Attendance Status, and Attendance Date.

**Suggested Layout:**

```text
┌─────────────────────────────────────────────────────┐
│ STUDENT PERFORMANCE & ATTENDANCE ANALYTICS           │
├──────────┬──────────┬──────────┬──────────┬──────────┤
│ Students │ Avg GPA  │ Avg Math │ Avg Python│ Attendance│
├────────────────────────┬────────────────────────────┤
│ Students by City       │ Academic Performance        │
│ Bar Chart              │ Clustered Bar Chart         │
├────────────────────────┼────────────────────────────┤
│ Attendance Distribution│ Student Summary Table       │
│ Donut Chart            │ Name, GPA, Grades, Status   │
└────────────────────────┴────────────────────────────┘
```
