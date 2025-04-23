# Digital Music Store Analysis Case Study

## Objectives
Analyze data from a digital music store to:
  - Understand customer preferences
  - Track sales trends (by genre, artist, etc.)
  - Optimize the music catalog

## Tools
  - SQL Server Management Studio 20 - SQL for data extraction and analysis
  - Excel and Power BI: Data Export and Visualization

## Understand Dataset

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
| PlaylistTrack             | Relationship between tracks and playlists                            |

| Possible Relationship     | Meaning                                                              |
|---------------------------|----------------------------------------------------------------------|
| Customer → Invoice        | A customer can have multiple purchases                               |
| Invoice → InvoiceLine     | Each invoice contains multiple tracks                                |
| InvoiceLine → Track       | Each sale line refers to a specific track                            |
| Track → Album → Artist    | Each track belongs to an album, and each album belongs to an artist  |
| Track → Genre             | Each track has a genre                                               |
| Track → MediaType         | Each track has a file format (e.g., MP3, MP4,...)                    |

## Key Questions and SQL Queries

Q1) What are the top-selling genres?
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
![imagem](https://github.com/user-attachments/assets/e2d52106-fe21-4a9b-9fc1-910b378aac30)

The best seller is Rock - 835 songs sold.


Q2) What are the top-selling genres by country? And the top-selling artists by country?
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
examples where Rock is the genre with the most sales.

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
rock musicians.

Q3) Which countries buy the most?
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
![imagem](https://github.com/user-attachments/assets/3696efa2-57c3-41bd-b004-4a242164ee2b)

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
Canada and France also stand out as important markets.


Q4) Which file format is the best seller?
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

The most preferred format is clearly the "MPEG audio file", by a wide margin.

Q5) Which playlists have the most songs sold?
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
Classical-themed playlists appear frequently, but with fewer sales.

Q6) What is the average duration of the most sold tracks?
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
40 minutes, suggesting these may be long-form recordings or full video tracks.

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
Genres like Hip Hop/Rap and Bossa Nova tend to have shorter average durations.

Q8) How have sales developed over time?

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
in total revenue. The peak revenue occurred in 2022 (€481.45), followed closely by 2024.
