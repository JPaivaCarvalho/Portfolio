# Total Optimization Plan for the Google Merchandise Store

### Introduction
  - The objective is to increase online sales and improve the user experience on the website.
  - We must analyze, draw conclusions, and present an action plan based on data from the Google Analytics Demo Account.

### Understand Dataset
🔵 Date Range: From January 1st until 27th April 2025. **Why?**
  - Recent data → I get an up-to-date view of user behavior.
  - Sufficient volume → There is enough traffic and conversions for the analysis to be statistically meaningful.
  - Campaigns and seasonality → I can detect the impact of special periods (e.g., Christmas, sales, etc.).
  - Avoid outdated data → Websites and campaigns change over time, so data from a year ago may no longer reflect the current reality.

🔵 Unsampled report
  - This means the report is based on 100% of the available data.
  - In other words, no sampling was applied — viewing all real data, without estimates.

🔵 Thresholding applied
  - Google applied thresholding to protect user privacy.
  - This means that some data may be hidden (for example, if the number of users is very small and individuals could potentially be identified).
  - I will only see data in the report elements that meet the minimum data volume requirements.

🔵 Data source
  - Indicates that the report is using both intraday and daily data.
  - "Intraday" means it also shows data from the current day, even if the day’s data is not yet complete.

### 1. Audience Analysis

🔵 **Demography**

**- Which two age groups generate the most revenue?**

![imagem](https://github.com/user-attachments/assets/17a8422a-084e-4235-9d0f-79c13e6ef171)

The two age groups generating the highest revenue are:
25-34 years old, with a total revenue of $105,938.41.
35-44 years old, with a total revenue of $64,671.15.

**- Gender: which one has the higher conversion rate?**

![imagem](https://github.com/user-attachments/assets/96e0b9c5-7414-47ff-84da-10586472b02e)

Male users contributed to 35.21% of the total revenue, slightly outperforming female users who contributed 33.14%.

🔵 **Location**

**- Which are the five countries with the most revenue?**

![imagem](https://github.com/user-attachments/assets/a9398bb6-7fcc-4752-828c-55b94b1c555d)

The U.S. market is by far the most important, generating over 90% of the total revenue.
All other countries combined account for only about 7–8% of the total revenue.

There are countries with high traffic but very low conversion rates and revenue. In particular, India, Philippines, and South Korea have a 
relatively high number of users but contribute minimally to total revenue. This suggests that while these markets attract traffic, they do 
not convert effectively, which could be due to factors such as purchasing power, language barriers, or product-market fit.

🔵 **Devices**

**- Desktop, Mobile, or Tablet: where is the conversion rate better? And the average revenue?**

Conversion rate (Key events: purchase): the conversion is represented in the 'Key events – purchase' column. 
The best conversion rate (in terms of purchase volume) comes from Desktop, with more than 90% of the conversions.

![imagem](https://github.com/user-attachments/assets/2dbd7567-7151-47a0-94fc-2ddf5b942c61)

Average revenue by device

![imagem](https://github.com/user-attachments/assets/e49ab0c0-5408-43cb-ab65-8163dec337bb)


| Device  | Revenue       | Active Users  | Revenue per User (approx.)  |
|---------|---------------|---------------|-----------------------------|
| Desktop | $526,425.62   | 115,897       | ~$4.54                      |
| Mobile  | $25,624.76    | 44,822        | ~$0.57                      |
| Tablet  | $1,236.86     | 2,256         | ~$0.55                      |

Desktop also has the highest average revenue per user.<br>  


### 2. Acquisition Analysis

🔵 **Traffic Sources**

**- Which are the 3 best channels (Source/Medium) in revenue?**

![imagem](https://github.com/user-attachments/assets/7159a30a-0710-434b-b9b2-c7a4f6cfa4d3)

Based on the "Session source / medium" report, the top three revenue-generating channels are:
1. (direct) / (none) – $306,968.22 (55.48% of total revenue)
2. google / organic – $131,296.84 (23.73%)
3. google / cpc – $41,322.71 (7.47%)

These three channels combined account for over 86% of total revenue, indicating the strong impact of 
direct visits and Google traffic (both organic and paid). 
**(direct) / (none)** → Users who arrived at the site without a detectable external referral, meaning direct access.

**-Which channel has the worst conversion rate despite having high traffic?**

![imagem](https://github.com/user-attachments/assets/7cff243c-e029-4ec1-883c-0b1671049ef3)

Channels with high traffic but low conversion:

**(direct) / (none)**: While it drives over 56% of all sessions, its session key event rate is only 16.72%, below the average. 
This could indicate low engagement or untagged campaign traffic being grouped as "direct".

**baidu / organic**: Generates over 10k sessions but has a very poor key event rate of 0.45%, suggesting low-quality or 
irrelevant traffic.

🔵 **Campaigns**

**- Is there any specific campaign (utm_campaign) that has generated a lot of traffic? Did that traffic convert well?**

The campaign with the highest UTM-tagged traffic was [Evergreen] Merch Store US and CA | Search, with 10,925 sessions. 
![imagem](https://github.com/user-attachments/assets/45e36f6e-b90d-45ba-b919-5e29c266b5f1)

It also showed strong performance, with a session key event rate of 35.65% and generated approximately $11,860 in revenue.
![imagem](https://github.com/user-attachments/assets/b27c913b-194b-4a14-a188-8a27ccbe3575)


### 3. Behaviour Analysis

🔵 **Behavior Flow**

**- Which page loses the most users?**

![imagem](https://github.com/user-attachments/assets/58541b31-2ea3-47c7-9a5a-bbce4bf45430)

Although the report does not directly show the 'Exits' metric, we can infer potential drop-off points by observing
High number of views, Low engagement time, Very low (or null) revenue and Few key events

The home page "/" is the most likely page where users drop off. Despite attracting the most traffic, it has the 
lowest engagement time (19s), very few key events, and almost no revenue. This suggests that many users leave the 
site without taking meaningful action from the home page. Huge volume of traffic (23.2% of views), very low interaction time
(19 seconds), almost no valuable actions (only 37 key events) and almost no revenue

**- What is the most common path users take to reach the purchase?**

Let's consider that the customer journey consists of the following steps: 
**1st Landing Page > 2nd Product Page > 3rd Add to Cart (event) > 4th Begin Checkout (event) > 5th Purchase (event)**

In the case of the Google Merchandise Store's e-commerce experience, it was necessary to use 'Explorations' in GA4. 
Three funnels of Explorations were defined. They only differ in the Product Page.

  **Product Page '/shop/apparel'**
  ![imagem](https://github.com/user-attachments/assets/f6af257e-41ef-41a9-b95c-e9cc436f684a)

  **Product Page '/shop/apparel/mens'**
  ![imagem](https://github.com/user-attachments/assets/056c9283-5bfe-476c-9c79-4da77d11e26e)

  **Product Page '/shop/new'**
  ![imagem](https://github.com/user-attachments/assets/a30b69f4-a89a-4640-9d2f-ed0c6fe060c5)

The choice of these product pages is due to them being the pages with the most traffic. As we can observe from the last stage 
('Purchase'), the second funnel is the one that shows to be the most common path leading to the purchase.
**1st Landing Page '/' > 2nd Product Page '/shop/apparel/mens' > 3rd Add to Cart ('add_to_cart' event) > 4th Begin Checkout ('begin_checkout' event)**
**> 5th Purchase ('purchase' event)**

🔵 **Landing Pages**

**- What are the top 3 landing pages?**
