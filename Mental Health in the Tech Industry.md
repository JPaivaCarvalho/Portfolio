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
    CAST(a.AnswerText AS INT) AS Age,
    COUNT(*) AS Total
FROM dbo.Answer a
WHERE a.QuestionID = 1
  AND TRY_CAST(a.AnswerText AS INT) BETWEEN 18 AND 70
GROUP BY CAST(a.AnswerText AS INT)
ORDER BY Age;
```

![imagem](https://github.com/user-attachments/assets/be66447b-fff6-41bb-93c9-b11def446b05) ![imagem](https://github.com/user-attachments/assets/1f3a2037-5534-4b76-9cd7-eadd1691ce76)
 

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
  CASE
    WHEN LOWER(a.AnswerText) LIKE '%male%' AND NOT LOWER(a.AnswerText) LIKE '%female%' THEN 'Male'
    WHEN LOWER(a.AnswerText) LIKE '%female%' AND NOT LOWER(a.AnswerText) LIKE '%male%' THEN 'Female'
    WHEN LOWER(a.AnswerText) LIKE '%non%' OR LOWER(a.AnswerText) LIKE '%genderqueer%' OR LOWER(a.AnswerText) LIKE '%fluid%' THEN 'Non-binary / Genderqueer'
    WHEN LOWER(a.AnswerText) LIKE '%trans%' THEN 'Transgender'
    ELSE 'Other / Unspecified'
  END AS Gender_Group,
  COUNT(*) AS Total
FROM dbo.Answer a
WHERE a.QuestionID = 2
GROUP BY
  CASE
    WHEN LOWER(a.AnswerText) LIKE '%male%' AND NOT LOWER(a.AnswerText) LIKE '%female%' THEN 'Male'
    WHEN LOWER(a.AnswerText) LIKE '%female%' AND NOT LOWER(a.AnswerText) LIKE '%male%' THEN 'Female'
    WHEN LOWER(a.AnswerText) LIKE '%non%' OR LOWER(a.AnswerText) LIKE '%genderqueer%' OR LOWER(a.AnswerText) LIKE '%fluid%' THEN 'Non-binary / Genderqueer'
    WHEN LOWER(a.AnswerText) LIKE '%trans%' THEN 'Transgender'
    ELSE 'Other / Unspecified'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/e1182092-a3eb-4c4d-ab08-79adabe3b017)

### 📊Country

```sql
SELECT 
  CASE
    WHEN LOWER(a.AnswerText) IN ('united states', 'usa', 'us') THEN 'United States of America'
    WHEN LOWER(a.AnswerText) = 'uk' THEN 'United Kingdom'
    WHEN LOWER(a.AnswerText) = '-1' OR a.AnswerText IS NULL OR a.AnswerText LIKE '%other%' THEN 'Other / Unknown'
    ELSE a.AnswerText
  END AS Country,
  COUNT(*) AS Total
FROM dbo.Answer a
WHERE a.QuestionID = 3  -- supondo que QID=3 seja "What country do you live in?"
GROUP BY 
  CASE
    WHEN LOWER(a.AnswerText) IN ('united states', 'usa', 'us') THEN 'United States of America'
    WHEN LOWER(a.AnswerText) = 'uk' THEN 'United Kingdom'
    WHEN LOWER(a.AnswerText) = '-1' OR a.AnswerText IS NULL OR a.AnswerText LIKE '%other%' THEN 'Other / Unknown'
    ELSE a.AnswerText
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/03c5743f-1aed-4c65-b280-c3eff5e97585)

## 🔵Job Information

### 📊Work Remotely

```sql
SELECT 
  a.AnswerText AS WorkRemotely,
  COUNT(*) AS Total
FROM dbo.Answer a
WHERE a.QuestionID = 118  -- substituir pelo ID real
GROUP BY a.AnswerText
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/ff4e8b10-3a69-4e16-be5c-098a208e747d)


### 📊Work Position

```sql
SELECT 
  a.AnswerText AS PositionType,
  COUNT(*) AS Total
FROM dbo.Answer a
WHERE a.QuestionID = 117  -- substituir pelo ID real
GROUP BY a.AnswerText
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/0773ab34-3bb7-4b99-b0ff-8f109176c433)

### 📊Tech Role - 'Yes' or 'No'

```sql
SELECT 
  CASE 
    WHEN a.AnswerText = '1' THEN 'Yes - Tech Role'
    WHEN a.AnswerText = '0' THEN 'No - Non-Tech Role'
    WHEN a.AnswerText = '-1' THEN 'Unknown / Not Answered'
    ELSE 'Other'
  END AS TechRoleCategory,
  COUNT(*) AS Total
FROM dbo.Answer a
WHERE a.QuestionID = 13
GROUP BY 
  CASE 
    WHEN a.AnswerText = '1' THEN 'Yes - Tech Role'
    WHEN a.AnswerText = '0' THEN 'No - Non-Tech Role'
    WHEN a.AnswerText = '-1' THEN 'Unknown / Not Answered'
    ELSE 'Other'
  END
ORDER BY Total DESC;
```

![imagem](https://github.com/user-attachments/assets/22b5b05f-2eb9-4ef0-ae27-f1b660b0fe16)


### 📊Self-Employed - 'Yes' or 'No'
