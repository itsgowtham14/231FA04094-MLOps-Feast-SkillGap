# Curriculum-Industry Skill Feature Store Using Feast

## Student Details

**Name:** Gowtham Challapalli  
**Register Number:** 231FA04094  
**Section:** [Enter Section]

## Problem Statement

The project addresses the curriculum-industry skill gap by representing students using academic performance, technical skills, practical experience, and soft skills. The objective is to build a simple Feast-based feature store that provides consistent features for machine-learning training and prediction.

## Dataset

- **Original records:** 5,000 students
- **Core technical skills:** 12
- **Original columns:** 27
- **Feature dataset records:** 15,000
- **Feature dataset columns:** 29
- **Target:** `EmployabilityStatus`

### Original Dataset Columns

`Student_ID, CGPA, Attendance, Backlogs, Year, Python, Java, DSA, SQL, OOP, DBMS, OS, CN, Git, Linux, Docker, Cloud, Projects, Internship, Certifications, Hackathons, OpenSource, LeetCode, Communication, Teamwork, Leadership, EmployabilityStatus`

The dataset was synthetically generated using curriculum and industry-oriented student skill attributes. Three timestamped snapshots were created for each student to demonstrate historical feature retrieval using Feast.

## Feature Engineering

The 12 technical skills are:

`Python, Java, DSA, SQL, OOP, DBMS, OS, CN, Git, Linux, Docker, Cloud`

A derived feature called `TechnicalSkillScore` was created from these skills.

Formula:

`TechnicalSkillScore = mean(12 technical skill values) / 2 × 100`

### Feast Features

| Feature | Meaning |
|---|---|
| CGPA | Academic performance |
| Attendance | Attendance percentage |
| Backlogs | Number of academic backlogs |
| Year | Academic year |
| Python | Python skill level |
| Java | Java skill level |
| DSA | Data Structures and Algorithms skill |
| SQL | SQL skill level |
| OOP | Object-Oriented Programming skill |
| DBMS | Database Management Systems skill |
| OS | Operating Systems skill |
| CN | Computer Networks skill |
| Git | Git/version-control skill |
| Linux | Linux skill |
| Docker | Docker skill |
| Cloud | Cloud computing skill |
| TechnicalSkillScore | Overall technical skill score |
| Projects | Number of projects |
| Internship | Internship experience |
| Certifications | Number of certifications |
| Hackathons | Hackathon participation |
| OpenSource | Open-source participation |
| LeetCode | LeetCode problems solved |
| Communication | Communication skill |
| Teamwork | Teamwork skill |
| Leadership | Leadership skill |

## Feast Architecture

    Original Dataset
          ↓
    Feature Engineering
          ↓
    Parquet Offline Data
          ↓
    Feast FeatureView
          ↓
     ┌─────────────────────┐
     ↓                     ↓
    Historical Features   Materialization
     ↓                     ↓
    Model Training       Online Store
                           ↓
                      Online Retrieval
                           ↓
                        Prediction

## Implementation

### Entity

The Feast entity is `student`.

The join key is `Student_ID`, which uniquely identifies each student.

### Data Source

The feature data is stored in:

`data/student_features.parquet`

A Feast `FileSource` uses `event_timestamp` as the timestamp field.

### FeatureView

The FeatureView is named:

`student_features`

It contains 26 student features and is configured for online serving.

### Registration

The Entity and FeatureView were registered using Feast's `apply` operation.

### Historical Feature Retrieval

Historical features were retrieved using:

`get_historical_features()`

The resulting historical dataset contained:

- **15,000 rows**
- **29 columns**
- **0 missing values**

The 29 columns consist of the entity, timestamp, target, and 26 Feast features.

### Machine Learning Model

A `RandomForestClassifier` was trained using the features retrieved through Feast.

The model uses 26 input features and predicts `EmployabilityStatus`.

### Materialization

The features were materialized from the offline Parquet source into the local SQLite online store.

### Online Feature Retrieval

Online features were retrieved using:

`get_online_features()`

For `Student_ID = 1`, Feast successfully returned the student's feature values, including `TechnicalSkillScore`.

## Results

### Historical Feature Retrieval

    Shape: (15000, 29)
    Missing values: 0

### Online Feature Retrieval

For `Student_ID = 1`:

    CGPA: 6.4
    Attendance: 66
    Backlogs: 1
    Year: 3
    Projects: 2
    Internship: 0
    Certifications: 1
    LeetCode: 52
    Communication: 1
    Teamwork: 0
    Leadership: 1
    TechnicalSkillScore: 8.33

### Final Prediction

For `Student_ID = 1`:

    Predicted Class: 2

    Class 0: 1.50%
    Class 1: 0.00%
    Class 2: 98.50%

## Required Analysis

### 1. What is the entity in your Feast implementation?

The entity is `student`, identified using the `Student_ID` join key.

### 2. List the features stored in your FeatureView.

The FeatureView contains:

`CGPA, Attendance, Backlogs, Year, Python, Java, DSA, SQL, OOP, DBMS, OS, CN, Git, Linux, Docker, Cloud, TechnicalSkillScore, Projects, Internship, Certifications, Hackathons, OpenSource, LeetCode, Communication, Teamwork, Leadership`

### 3. Explain how one feature was calculated.

`TechnicalSkillScore` was calculated from the 12 technical skill values using:

`TechnicalSkillScore = mean(12 technical skill values) / 2 × 100`

This produces an overall technical skill score on a 0–100 scale.

### 4. What is the difference between your original dataset and the feature dataset?

The original dataset contains 5,000 student records and 27 columns. The feature dataset contains three timestamped snapshots for each student, resulting in 15,000 records and 29 columns. It also contains the engineered `TechnicalSkillScore` and `event_timestamp`.

### 5. What is the purpose of the offline store?

The offline store contains historical feature data. It is used for historical feature retrieval and machine-learning training.

### 6. What is the purpose of the online store?

The online store contains materialized feature values that can be retrieved quickly during model prediction.

### 7. What is the purpose of `feast apply`?

`feast apply` registers and updates Feast entities, data sources, and FeatureViews in the Feast registry.

### 8. What does materialization do?

Materialization transfers feature values from the offline source into the online store so they can be retrieved efficiently during online inference.

### 9. What is the advantage of retrieving features through Feast instead of manually calculating them separately during training and prediction?

Feast provides a consistent feature definition and retrieval mechanism for both training and prediction. This reduces duplicated feature-engineering logic and helps avoid training-serving inconsistencies.

### 10. State two limitations of your current dataset.

1. The dataset is synthetically generated and may not accurately represent real-world student outcomes.
2. The dataset has limited real industry evidence such as actual job descriptions, employer requirements, placement outcomes, and hiring decisions.

### 11. State two ways your feature store could be improved when more curriculum and industry evidence becomes available.

1. Integrate real job descriptions and employer requirements to create industry-demand and role-specific skill features.
2. Add real longitudinal student data such as assessments, internships, projects, certifications, and placement outcomes.

## Repository Structure

    231FA04094-MLOps-Feast-SkillGap/
    ├── README.md
    ├── SkillGap_Feast.ipynb
    ├── feature_store.yaml
    └── data/
        ├── student_dataset_ml.csv
        ├── student_features.parquet
        ├── feast_training_dataset.parquet
        └── feast_random_forest.pkl

## Technologies Used

- Python
- Pandas
- Scikit-learn
- Feast
- Parquet
- SQLite
- Jupyter Notebook
- Git
- GitHub
