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

![imagem](https://github.com/user-attachments/assets/be66447b-fff6-41bb-93c9-b11def446b05)  ![imagem](https://github.com/user-attachments/assets/51d96168-b51f-43af-b4a8-d5b854146227)

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

