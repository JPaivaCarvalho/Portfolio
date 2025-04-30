# Digital Music Store Analysis Case Study

## Objectives
Analyze data from a digital music store to:
  - Understand customer preferences
  - Track sales trends (by genre, artist, etc.)
  - Optimize the music catalog

## Tools
  - SQL Server Management Studio 20 - SQL for data extraction and analysis
  - Excel and [Power BI: Data Export and Visualization](https://github.com/JPaivaCarvalho/Portfolio/blob/main/Digital%20Music%20Store%20Dashboard.pbix) **"Download Raw File"**

## Understand Dataset
- Source: [Kaggle](https://www.kaggle.com/datasets/samaxtech/chinook-music-store-data)

| Table                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Customer                  | Store customers (name, email, country, etc.)                         |
| Invoice                   | Invoices/sales made to customers                                     |
| InvoiceLine               | Details of tracks sold per invoice                                   |
| Track                     | Each track in the store (name, duration, price, etc.)                |
| Album                     | Album to which the track belongs                                     |
| Artist                    | Artist responsible for the album                                     |
| Genre                     | Musical genre of the track                                           |
| MediaType                 | File format of the track                                             |
| Employee                  | Employees (support representatives, for example)                     |
| Playlist                  | Playlists created with associated tracks                             |
| PlaylistTrack             | Relationship between tracks and playlists                            |<br><br>

| Possible Relationship     | Meaning                                                              |
|---------------------------|----------------------------------------------------------------------|
| Customer → Invoice        | A customer can have multiple purchases                               |
| Invoice → InvoiceLine     | Each invoice contains multiple tracks                                |
| InvoiceLine → Track       | Each sale line refers to a specific track                            |
| Track → Album → Artist    | Each track belongs to an album, and each album belongs to an artist  |
| Track → Genre             | Each track has a genre                                               |
| Track → MediaType         | Each track has a file format (e.g., MP3, MP4,...)                    |

## Key Questions and SQL Queries

**Q1) What are the top-selling genres? The lowest-selling genres?**
```sql
SELECT 
    g.Name AS Genre,
    COUNT(il.InvoiceLineId) AS TotalSold
FROM InvoiceLine il
JOIN Track t ON il.TrackId = t.TrackId
JOIN Genre g ON t.GenreId = g.GenreId
GROUP BY g.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/b962060d-9d15-4734-8659-8963d75853eb)

The best sellers are Rock - 835 tracks sold - Latin - 386 tracks - and Metal - 264 tracks. The lowest sellers are Comedy - 9 tracks - , Science Fiction - 6 tracks - 
and Rock and Roll - 6 tracks. <br><br>


**Q2) What are the top-selling genres by country? And the top-selling artists by country?**
```sql
SELECT 
    c.Country,
    g.Name AS Genre,
    COUNT(il.InvoiceLineId) AS TotalSold
FROM Customer c
JOIN Invoice i ON c.CustomerId = i.CustomerId
JOIN InvoiceLine il ON i.InvoiceId = il.InvoiceId
JOIN Track t ON il.TrackId = t.TrackId
JOIN Genre g ON t.GenreId = g.GenreId
GROUP BY c.Country, g.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/7980cb74-da27-47d6-9f90-b3d98f1530a0)

Rock is the genre with the most sales. Crossing this with the country, USA is the one with the most songs sold, 
and Rock is the genre that contributes most to these sales. In addition to the USA, Canada, Brazil and France are other 
examples where Rock is the genre with the most sales.<br><br>

```sql
SELECT 
    c.Country,
    ar.Name AS Artist,
    COUNT(il.InvoiceLineId) AS TotalSold
FROM Customer c
JOIN Invoice i ON c.CustomerId = i.CustomerId
JOIN InvoiceLine il ON i.InvoiceId = il.InvoiceId
JOIN Track t ON il.TrackId = t.TrackId
JOIN Album al ON t.AlbumId = al.AlbumId
JOIN Artist ar ON al.ArtistId = ar.ArtistId
GROUP BY c.Country, ar.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/e9948fb0-533c-45ab-972d-1966e28ef1a4)

Grouping by artist shows that rock is the best-selling genre. The artists we see are mostly
rock musicians.<br><br>

**Q3) Which artists have the most sales? And lowest sales?**
```sql
FROM InvoiceLine il
JOIN Track t ON il.TrackId = t.TrackId
JOIN Album al ON t.AlbumId = al.AlbumId
JOIN Artist ar ON al.ArtistId = ar.ArtistId
GROUP BY ar.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/83d92d26-6b1a-4d8b-887e-0b3c0d222f2c)
![imagem](https://github.com/user-attachments/assets/4a0b3252-6732-4b52-bfbe-e97dd1c24acc)

Iron Maiden leads with 140 tracks sold, followed by U2 and Metallica.
The top-selling artists are primarily from the rock and metal genres.

In contrast, among the lowest-selling artists, most had only one track sold, with a few reaching two.
These names are largely composed of classical musicians, orchestras, and ensembles.

**Q4) Which countries buy the most?**
• By invoice
```sql
SELECT 
    c.Country,
    COUNT(i.InvoiceId) AS TotalPurchases
FROM Customer c
JOIN Invoice i ON c.CustomerId = i.CustomerId
GROUP BY c.Country
ORDER BY TotalPurchases DESC;
```
![imagem](https://github.com/user-attachments/assets/3696efa2-57c3-41bd-b004-4a242164ee2b)<br><br>

• By revenue
```sql
SELECT 
    c.Country,
    SUM(i.Total) AS TotalRevenue
FROM Customer c
JOIN Invoice i ON c.CustomerId = i.CustomerId
GROUP BY c.Country
ORDER BY TotalRevenue DESC;
```
![imagem](https://github.com/user-attachments/assets/1a3d1010-5c33-4d95-86c2-144cd4522db9)

The United States is by far the market with the highest revenue.
Canada and France also stand out as important markets.<br><br>


**Q5) Which file format is the best seller?**
```sql
SELECT 
    mt.Name AS MediaType,
    COUNT(il.InvoiceLineId) AS TotalSold
FROM InvoiceLine il
JOIN Track t ON il.TrackId = t.TrackId
JOIN MediaType mt ON t.MediaTypeId = mt.MediaTypeId
GROUP BY mt.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/ae230556-4183-4f61-a34f-e9b7e38cfe06)

The most preferred format is clearly the "MPEG audio file", by a wide margin.<br><br>

**Q6) Which playlists have the most songs sold?**
```sql
SELECT 
    p.Name AS Playlist,
    COUNT(il.InvoiceLineId) AS TotalSold
FROM InvoiceLine il
JOIN Track t ON il.TrackId = t.TrackId
JOIN PlaylistTrack pt ON t.TrackId = pt.TrackId
JOIN Playlist p ON pt.PlaylistId = p.PlaylistId
GROUP BY p.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/99bea94c-9779-494a-8281-01891577c65e)

"Music" dominates with 4,258 songs sold, followed by "90's Music" and "TV Shows".
Classical-themed playlists appear frequently, but with fewer sales.<br><br>

**Q7) What is the average duration of the most sold tracks?**

• By format
```sql
SELECT 
    mt.Name AS MediaType,
    COUNT(il.InvoiceLineId) AS TotalSold,
    ROUND(AVG(t.Milliseconds), 0) AS AvgDurationMs,
    ROUND(AVG(t.Milliseconds) / 60000.0, 2) AS AvgDurationMinutes
FROM InvoiceLine il
JOIN Track t ON il.TrackId = t.TrackId
JOIN MediaType mt ON t.MediaTypeId = mt.MediaTypeId
GROUP BY mt.Name
ORDER BY TotalSold DESC;
```
![imagem](https://github.com/user-attachments/assets/da00a3fb-eb4a-4590-85cb-0a9e03f54a53)

The "MPEG audio file" is the most sold format with an average track duration of 4.42 minutes.
Interestingly, the "Protected MPEG-4 video file" has the highest average duration, reaching over 
40 minutes, suggesting these may be long-form recordings or full video tracks.<br><br>

• By genre
```sql
SELECT 
    g.Name AS Genre,
    COUNT(il.InvoiceLineId) AS TotalSold,
    ROUND(AVG(t.Milliseconds), 0) AS AvgDurationMs,
    ROUND(AVG(t.Milliseconds) / 60000.0, 2) AS AvgDurationMinutes
FROM InvoiceLine il
JOIN Track t ON il.TrackId = t.TrackId
JOIN Genre g ON t.GenreId = g.GenreId
GROUP BY g.Name
ORDER BY TotalSold DESC;
```
Rock is the most sold genre, with average duration of 4.71 minutes.
The genre TV Shows stands out with an exceptionally high average duration of 37.19 minutes, likely due to 
full episodes or long recordings.
Genres like Hip Hop/Rap and Bossa Nova tend to have shorter average durations.<br><br>

**Q8) How have sales developed over time?**

• Annually
```sql
SELECT 
    YEAR(i.InvoiceDate) AS Year,
    COUNT(i.InvoiceId) AS TotalInvoices,
    SUM(i.Total) AS TotalRevenue
FROM Invoice i
GROUP BY YEAR(i.InvoiceDate)
ORDER BY Year;
```
![imagem](https://github.com/user-attachments/assets/5a7356c8-f599-403b-8891-caf1fe34bd2c)

Over the years, the total number of invoices remained consistent around 83 per year, with slight variations 
in total revenue. The peak revenue occurred in 2022 (€481.45), followed closely by 2024.<br><br>

• Monthly
```sql
SELECT 
    FORMAT(i.InvoiceDate, 'yyyy-MM') AS Month,
    COUNT(i.InvoiceId) AS TotalInvoices,
    SUM(i.Total) AS TotalRevenue
FROM Invoice i
GROUP BY FORMAT(i.InvoiceDate, 'yyyy-MM')
ORDER BY Month;
```
![imagem](https://github.com/user-attachments/assets/26282757-a01c-4175-b542-182a0a450a88)    
![imagem](https://github.com/user-attachments/assets/67082e93-d9d7-428e-b9a2-0b431c13ca76)

Stable number of invoices. Almost every month consistently shows 7 invoices, which indicates a very stable 
customer base. Slight variations (e.g. 6 invoices in Jan 2021, Sep 2022, Nov 2023, Sep 2024) are isolated and 
likely not significant.

Most months have revenue around €37.62, suggesting this is the typical average revenue per month from the current 
customer base. However, we do not observe a clear pattern of certain months performing better or worse in terms 
of sales.<br><br>

**Q9) How much does each customer spend on average in total? How much revenue is generated on average per month?**

• Average Revenue per Month
```sql
SELECT 
    COUNT(DISTINCT FORMAT(i.InvoiceDate, 'yyyy-MM')) AS TotalMonths,
    SUM(i.Total) AS TotalRevenue,
    ROUND(SUM(i.Total) / COUNT(DISTINCT FORMAT(i.InvoiceDate, 'yyyy-MM')), 2) AS AvgRevenuePerMonth
FROM Invoice i;
```
![imagem](https://github.com/user-attachments/assets/778ccb07-ee39-43ab-b074-95557e0fd2e0)

Over a period of 60 months, the store generated a total of €2,328.60 in revenue.
This results in an average monthly revenue of €38.81.<br><br>

• Average Revenue per Customer
```sql
SELECT 
    COUNT(DISTINCT c.CustomerId) AS TotalCustomers,
    SUM(i.Total) AS TotalRevenue,
    ROUND(SUM(i.Total) / COUNT(DISTINCT c.CustomerId), 2) AS AvgRevenuePerCustomer
FROM Customer c
JOIN Invoice i ON c.CustomerId = i.CustomerId;
```
![imagem](https://github.com/user-attachments/assets/83da00bb-05e5-4e56-9430-2620378d3100)

Each customer spends, on average, €39.47 across all their purchases in the store.<br><br>


## Recommendations - Catalog Optimization
1. **Focus on Top Performers:** prioritize promoting genres and artists with proven sales — Iron Maiden, U2, and Metallica 
lead sales. Rock and Metal dominate in popularity.

2. **Reassess Low-Selling Catalog:** remove them or lower prices. Dozens of artists — mostly classical ensembles and
orchestras — had only 1 or 2 tracks sold.

3. **Countries with the highest sales:** offer personalized campaigns for the most active customers. Create music service bundles.
Or bundles of frequently purchased music in those countries.

