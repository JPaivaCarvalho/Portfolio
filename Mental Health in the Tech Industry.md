# Mental Health in the Tech Industry

## Project Overview
This project explores mental health attitudes and the prevalence of mental health conditions among employees in the tech industry. 
We will use SQL to analyze and gain insights from survey data.


## Understarnd the Dataset
The dataset consists of responses from employees in the technology industry to surveys on mental health conducted between 2014 and 2019. It includes three main tables:
- Survey: Contains metadata about each year's survey, including the survey ID and description.
- Question: Stores the text of each question and a unique identifier for each.
- Answer: Contains individual responses, linking users to questions and surveys, and storing their answers in text format.

Some questions have numeric answers (e.g., age), while many others are categorical (e.g., Yes/No, country, gender) or even open-text. This structure allows us to explore both quantitative trends (like averages or counts) and qualitative perceptions (such as comfort discussing mental health).

Understanding the types of questions and how responses are stored is key to designing meaningful queries and extracting insights from the data.

To better organize the analysis, we grouped the survey questions into thematic areas:
- Demographic Information
- Diagnosis, Treatment, and Mental Health Condition
- Employer Support and Benefits
- Communication and Comfort Discussing Mental Health
- Stigma and Perceived Consequences
- Open-Ended Responses and Comments

This thematic categorization helps structure the analysis logically and enables us to generate more relevant and actionable insights around different aspects of mental health in the workplace.


## 🔵Demographic Information
Includes data such as age, gender, role, location, work arrangement, and company size to enable segmentation of responses.

### 📊Age
Data cleaning was performed, considering valid age responses between 18 (the legal minimum working age) and 70 (the typical retirement age, and because only one response was recorded beyond this point).

```sql
SELECT 
  Age,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM (
  SELECT 
    CAST(a.AnswerText AS INT) AS Age
  FROM dbo.Answer a
  WHERE a.QuestionID = 1
    AND TRY_CAST(a.AnswerText AS INT) BETWEEN 18 AND 70
) AS sub
GROUP BY Age
ORDER BY Percentage DESC;
```

![imagem](https://github.com/user-attachments/assets/c5046c24-8360-46ef-9abb-cfd42eb42e9c)   ![imagem](https://github.com/user-attachments/assets/a274720e-4af8-4c11-b219-f10f07256384)


Média de idade por ano da survey                                      
```sql
SELECT 
    a.SurveyID,
    AVG(CAST(a.AnswerText AS FLOAT)) AS AverageAge
FROM dbo.Answer a
WHERE a.QuestionID = 1
  AND TRY_CAST(a.AnswerText AS INT) BETWEEN 18 AND 70
GROUP BY a.SurveyID
ORDER BY a.SurveyID;
```

![imagem](https://github.com/user-attachments/assets/13765063-82f0-4732-8c2d-19a3bd84e3bf)

Média de idade geral
```sql
SELECT 
    AVG(CAST(a.AnswerText AS FLOAT)) AS OverallAverageAge
FROM dbo.Answer a
WHERE a.QuestionID = 1
  AND TRY_CAST(a.AnswerText AS INT) BETWEEN 18 AND 70;
```

![imagem](https://github.com/user-attachments/assets/daf06609-9848-4170-be8e-361fbff15a1d)

### 📊Gender
Agrupou-se a lista de respostas em grupos padronizados de géneros.

```sql
SELECT 
  Gender_Group,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM (
  SELECT 
    CASE
      WHEN LOWER(a.AnswerText) LIKE '%male%' AND NOT LOWER(a.AnswerText) LIKE '%female%' THEN 'Male'
      WHEN LOWER(a.AnswerText) LIKE '%female%' AND NOT LOWER(a.AnswerText) LIKE '%male%' THEN 'Female'
      WHEN LOWER(a.AnswerText) LIKE '%non%' OR LOWER(a.AnswerText) LIKE '%genderqueer%' OR LOWER(a.AnswerText) LIKE '%fluid%' THEN 'Non-binary / Genderqueer'
      WHEN LOWER(a.AnswerText) LIKE '%trans%' THEN 'Transgender'
      ELSE 'Other / Unspecified'
    END AS Gender_Group
  FROM dbo.Answer a
  WHERE a.QuestionID = 2
) AS sub
GROUP BY Gender_Group
ORDER BY Total DESC;

```

![imagem](https://github.com/user-attachments/assets/d48c6bc7-b3f3-4d4b-8922-a96bd4af9186)


### 📊Country

```sql
SELECT 
  Country,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM (
  SELECT 
    CASE
      WHEN LOWER(a.AnswerText) IN ('united states', 'usa', 'us') THEN 'United States of America'
      WHEN LOWER(a.AnswerText) = 'uk' THEN 'United Kingdom'
      WHEN LOWER(a.AnswerText) = '-1' OR a.AnswerText IS NULL OR a.AnswerText LIKE '%other%' THEN 'Other / Unknown'
      ELSE a.AnswerText
    END AS Country
  FROM dbo.Answer a
  WHERE a.QuestionID = 3
) AS sub
GROUP BY Country
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/12f3a1b7-0155-40f0-afc8-d3d062f1a2ba)

### 📊Race

```sql
SELECT 
  CASE 
    WHEN AnswerText IN ('White', 'Caucasian', 'European American') THEN 'White'
    WHEN AnswerText IN ('Hispanic', 'White Hispanic') THEN 'Hispanic or Latino'
    WHEN AnswerText = 'Black or African American' THEN 'Black or African American'
    WHEN AnswerText = 'Asian' THEN 'Asian'
    WHEN AnswerText = 'American Indian or Alaska Native' THEN 'American Indian or Alaska Native'
    WHEN AnswerText = 'More than one of the above' THEN 'Multiracial or Other'
    WHEN AnswerText = 'I prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Invalid / Missing'
  END AS Race,
  COUNT(*) AS Total,
  CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)) AS Percentage
FROM dbo.Answer
WHERE QuestionID = 89
GROUP BY 
  CASE 
    WHEN AnswerText IN ('White', 'Caucasian', 'European American') THEN 'White'
    WHEN AnswerText IN ('Hispanic', 'White Hispanic') THEN 'Hispanic or Latino'
    WHEN AnswerText = 'Black or African American' THEN 'Black or African American'
    WHEN AnswerText = 'Asian' THEN 'Asian'
    WHEN AnswerText = 'American Indian or Alaska Native' THEN 'American Indian or Alaska Native'
    WHEN AnswerText = 'More than one of the above' THEN 'Multiracial or Other'
    WHEN AnswerText = 'I prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Invalid / Missing'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/af3ea24d-84d6-48e5-8e04-74642c2ea6bf)


## 🔵Job Information

### 📊Work Remotely

```sql
SELECT 
  a.AnswerText AS WorkRemotely,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM dbo.Answer a
WHERE a.QuestionID = 118
GROUP BY a.AnswerText
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/8032142d-ee07-4104-bea7-19ec45bae603)


### 📊Work Position

```sql
SELECT 
  a.AnswerText AS PositionType,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM dbo.Answer a
WHERE a.QuestionID = 117
GROUP BY a.AnswerText
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/c1cf1d11-7bb4-48a0-8f0f-16346f78f1e4)


### 📊Tech Role - 'Yes' or 'No'

```sql
SELECT 
  TechRoleCategory,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM (
  SELECT 
    CASE 
      WHEN a.AnswerText = '1' THEN 'Yes - Tech Role'
      WHEN a.AnswerText = '0' THEN 'No - Non-Tech Role'
      WHEN a.AnswerText = '-1' THEN 'Unknown / Not Answered'
      ELSE 'Other'
    END AS TechRoleCategory
  FROM dbo.Answer a
  WHERE a.QuestionID = 13
) AS sub
GROUP BY TechRoleCategory
ORDER BY Total DESC;

```

![imagem](https://github.com/user-attachments/assets/46afed9e-4ef0-4876-a276-7d262ed52f37)



### 📊Self-Employed - 'Yes' or 'No'

```sql
SELECT 
  SelfEmploymentStatus,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM (
  SELECT 
    CASE 
      WHEN a.AnswerText = '1' THEN 'Yes - Self-employed'
      WHEN a.AnswerText = '0' THEN 'No - Employed'
      WHEN a.AnswerText = '-1' THEN 'Unknown / Not Answered'
      ELSE 'Other'
    END AS SelfEmploymentStatus
  FROM dbo.Answer a
  WHERE a.QuestionID = 5
) AS sub
GROUP BY SelfEmploymentStatus
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/d1c2d7dd-1a81-4cef-b4aa-4485ec9cc869)


### 📊Company Size

```sql
SELECT 
  CompanySizeCategory,
  COUNT(*) AS Total,
  FORMAT(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 'N1') + '%' AS Percentage
FROM (
  SELECT 
    CASE 
      WHEN a.AnswerText = '-1' THEN 'Unknown / Not Answered'
      ELSE a.AnswerText
    END AS CompanySizeCategory
  FROM dbo.Answer a
  WHERE a.QuestionID = 8
) AS sub
GROUP BY CompanySizeCategory
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/7355cdd7-6acd-478e-a89b-145af6514fb5)

