Relax: Predicting User Adoption Takehome
Introduction
Este relatório analisa dados fornecidos pela empresa fictícia Relax Inc., no contexto de um desafio prático de Data Analytics. O desafio encontra-se disponível em Interview Query .
Foram disponibilizados dois ficheiros CSV:
takehome\_users.csv, com informações sobre 12.000 utilizadores registados nos últimos dois anos, incluindo dados como o método de criação de conta, data de criação, último login, e opções de marketing.
takehome\_user\_engagement.csv, que contém o registo de cada dia em que um utilizador efetuou login na plataforma.

Asking Questions
Através da análise exploratória dos dados (EDA) e da aplicação de técnicas de modelação preditiva, este projeto procura responder a duas perguntas centrais:
Quem são os utilizadores adotados?
Quais são os fatores que mais contribuem para prever a adoção de um utilizador?

Data Preparation
IMPORT LIBRARIES AND PREVIEW THE TABLES



SET 'OBJECT\_ID' AND 'TIME\_STAMP' AS COLUMN INDEX


GROUP BY USER\_ID

IDENTIFY WHICH USERS ARE ADOPTED USERS









MERGE DATASETS


Exploratory Data Analysis (EDA)
The goal of this section is to better understand the characteristics and behaviors of the users, as well as to identify potential patterns or relationships that may help explain user adoption. By exploring both user profile attributes and engagement data, we aim to uncover insights that can guide the development of predictive models and inform business decisions.
We begin by analyzing the distribution of adopted vs. non-adopted users, followed by an investigation of various user features such as account creation source, marketing preferences, organizational affiliation, and login activity. Visualizations and summary statistics are used throughout to highlight relevant trends and correlations.

DISTRIBUTION OF ADOPTED VS NON-ADOPTED USERS
 

‘ADOPTED USERS’ EVOLUTION OVER TIME
Create “Adopted\_date” Variable
Create a variable called “adopted\_date” – record the first date on which each user logged in for the third time within a 7-day period.
To understand when users became adopted users over time, we need to know the date on which that happened.

Generate the evolution graph


















TOTAL ADOPTED USERS BY TYPE OF ASSOCIATION


‘ADOPTED USERS’ BY CREATION SOURCE















ADOPTED USERS BY MAILING LIST SUBSCRIPTION






Exploratory Data Analysis (EDA)
