# Mental Health in the Tech Industry

## Project Overview
This project explores mental health attitudes and the prevalence of mental health conditions among employees in the tech industry. 
We will use SQL to analyze and gain insights from survey data.


## Understand the Dataset
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


Average age by survey year                                    
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

Overall average age
```sql
SELECT 
    AVG(CAST(a.AnswerText AS FLOAT)) AS OverallAverageAge
FROM dbo.Answer a
WHERE a.QuestionID = 1
  AND TRY_CAST(a.AnswerText AS INT) BETWEEN 18 AND 70;
```

![imagem](https://github.com/user-attachments/assets/daf06609-9848-4170-be8e-361fbff15a1d)

### 📊Gender
The list of responses was grouped into standardized gender categories.

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

#### Live in

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

#### Work in

```sql
SELECT 
  AnswerText AS Country,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 50
GROUP BY AnswerText
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/8d54106c-9738-41d0-a5d7-86d1412cb765)


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
  END AS CleanedRace,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
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

![imagem](https://github.com/user-attachments/assets/55ad9f35-9889-491f-8a48-5e80405bd0c5)


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

## 🔵Diagnosis, Treatment, and Condition
Covers personal experiences with mental health issues, including diagnosis, treatment, and how these conditions affect work.

### 📊Family History of Mental Illness

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes.') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'no.') THEN 'No'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END AS Family_History_mental_illness,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 6
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes.') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'no.') THEN 'No'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/86b8128b-41b7-4136-8d5f-6c93fd1dfc92)

Group invalid or empty responses as 'Missing'.


### 📊Sought treatment for a mental health disorder - 'Yes' or 'No'

```sql
SELECT 
  CASE 
    WHEN AnswerText = '1' THEN 'Yes'
    WHEN AnswerText = '0' THEN 'No'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 7
GROUP BY 
  CASE 
    WHEN AnswerText = '1' THEN 'Yes'
    WHEN AnswerText = '0' THEN 'No'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/65c654e6-26d3-44cb-8af0-d25df4270b13)

### 📊Mental health disorder in the past - 'Yes' or 'No'

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'maybe' THEN 'Maybe'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'possibly' THEN 'Possibly'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('don''t know', 'dont know') THEN 'Don''t Know'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    WHEN ISNUMERIC(AnswerText) = 1 THEN 'Other'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 32
  AND (ISNUMERIC(AnswerText) = 0 OR TRY_CAST(AnswerText AS INT) <> -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'maybe' THEN 'Maybe'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'possibly' THEN 'Possibly'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('don''t know', 'dont know') THEN 'Don''t Know'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    WHEN ISNUMERIC(AnswerText) = 1 THEN 'Other'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/b1ff62af-8165-483d-88e4-489a787ad44d)

### 📊Mental health disorder currently - 'Yes' or 'No'

Use the same query, only the QuestionID changes to 33.

![imagem](https://github.com/user-attachments/assets/ffeb0571-c98c-4a5f-9f2d-d1869fe40a00)

### 📊Mental health disorder impact on Work

a) When being treated

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'never' THEN 'Never'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'rarely' THEN 'Rarely'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'sometimes' THEN 'Sometimes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'often' THEN 'Often'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'very often' THEN 'Very Often'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'always' THEN 'Always'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'not applicable to me' THEN 'Not applicable to me'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 35
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'never' THEN 'Never'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'rarely' THEN 'Rarely'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'sometimes' THEN 'Sometimes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'often' THEN 'Often'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'very often' THEN 'Very Often'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'always' THEN 'Always'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'not applicable to me' THEN 'Not applicable to me'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/4035f513-9e03-43ae-bde7-3addf569a448)

b) When not being treated
Use the same query, only the QuestionID changes to 49.

![imagem](https://github.com/user-attachments/assets/2a8ba652-e6c5-4e64-a559-41561b567ad6)


## 🔵Employer Support and Benefits
Focuses on the resources, benefits, and support systems that companies offer for mental health.

### 📊Employer-provided support

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'i don''t know' THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('dont know', 'don''t know') THEN 'I don''t know'
	WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'Not eligible for coverage / NA' THEN 'Not eligible for coverage / NA'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 10
	AND (ISNUMERIC(AnswerText) = 0 OR TRY_CAST(AnswerText AS INT) <> -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'i don''t know' THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('dont know', 'don''t know') THEN 'I don''t know'
	WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'Not eligible for coverage / NA' THEN 'Not eligible for coverage / NA'
    WHEN AnswerText IS NULL OR LTRIM(RTRIM(AnswerText)) = '' THEN 'Missing'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/0c8f200a-1008-47b4-be09-f7bf95fa3775)

### 📊Employer-provided support

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 11
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

### 📊Employer Anonymity Policy

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 11
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/e5a48650-3dd4-46a9-bdcf-541be95c6397)

### 📊Employer-provided Health Coverage

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 14
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/6baade9e-2adf-4dbe-b1d1-db017bb8a6d6)

### 📊Mental Health Communication

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 15
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```
![imagem](https://github.com/user-attachments/assets/c6982fe7-2c87-49e6-b70d-efe1f6e86c0f)

### 📊Employer resources

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 16
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/a72630eb-9825-4c78-82b5-41e6083b9dd7)

### 📊Previous Employers

a) Mental Health Benefits

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'no, none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS Answer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 23
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'no, none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/40bfe6b2-6a83-4d23-87d3-5b9a00afb279)


b) Previous care awareness

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS Answer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 24
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'yes' THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'no' THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/8d13fc08-2e90-4a4b-9bc0-2310e18a5f21)
* n/a ('n/a (none offered)', 'n/a (not currently aware)', 'n/a (was not aware)')

c) Previous formal discussion mental health

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 25
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/01cd4a80-aa31-40e8-bc01-fcc1bd6002f1)


d) Previous support resources

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), ' %') AS Percentage
FROM dbo.Answer
WHERE QuestionID = 26
  AND (TRY_CAST(AnswerText AS INT) IS NULL OR TRY_CAST(AnswerText AS INT) != -1)
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/d9d4314b-bc88-49ac-8e63-d4869780ee6d)

e) Employer Anonymity Policy

```sql
SELECT 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'no, none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'somewhat' THEN 'Somewhat'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    -- Adiciona outras opções específicas se houver
    ELSE LTRIM(RTRIM(AnswerText))  -- Mantém outras respostas literalmente, sem agrupar em 'Other'
  END AS CleanedAnswer,
  COUNT(*) AS Total,
  CONCAT(
    CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)), 
    ' %'
  ) AS Percentage
FROM dbo.Answer
WHERE QuestionID = 27
  AND TRY_CAST(AnswerText AS INT) IS NULL  -- Exclui valores numéricos como -1
  AND LTRIM(RTRIM(AnswerText)) <> ''       -- Exclui respostas em branco
GROUP BY 
  CASE 
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('yes', 'yes, they all did') THEN 'Yes'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('no', 'no, none did') THEN 'No'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'some did' THEN 'Some'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'somewhat' THEN 'Somewhat'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) IN ('i don''t know', 'don''t know', 'dont know') THEN 'I don''t know'
    WHEN LOWER(LTRIM(RTRIM(AnswerText))) = 'prefer not to answer' THEN 'Prefer not to answer'
    ELSE LTRIM(RTRIM(AnswerText))  -- Inclui explicitamente o texto original como categoria
  END
ORDER BY Total DESC;
```

### 📊Employer Physical and Mental health - 0 to 10

a) Physical Health

```sql
SELECT 
  CAST(AnswerText AS INT) AS Score,
  COUNT(*) AS Total,
  CONCAT(
    CAST(COUNT(*) * 100.0 / SUM(COUNT(*)) OVER () AS DECIMAL(5,2)),
    ' %'
  ) AS Percentage
FROM dbo.Answer
WHERE QuestionID = 64
  AND TRY_CAST(AnswerText AS INT) BETWEEN 0 AND 10  -- só aceita valores de 0 a 10
  AND TRY_CAST(AnswerText AS INT) IS NOT NULL       -- exclui NULL e textos inválidos
  AND LTRIM(RTRIM(AnswerText)) <> ''                -- exclui strings vazias
  AND TRY_CAST(AnswerText AS INT) <> -1             -- exclui -1 explicitamente
GROUP BY CAST(AnswerText AS INT)
ORDER BY Score ASC;
```

![imagem](https://github.com/user-attachments/assets/9de9dd4e-cedf-4d71-85b2-4af6da51162e)


b) Mental Health



## 🔵Communication and Comfort Discussing Mental Health
Evaluates how comfortable employees feel discussing mental health with coworkers, supervisors, or during job interviews.

## 🔵Stigma and Perceived Consequences
Addresses fears of negative outcomes, stigma in the workplace, and how mental health disclosures may affect careers.

## 🔵Open-Ended Responses and Comments
Contains qualitative feedback and personal reflections, providing deeper insight into individual experiences and suggestions.
