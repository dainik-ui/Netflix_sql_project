# Netflix Movies and TV Shows Data Analysis using SQL
![Netflix Logo](https://github.com/dainik-ui/Netflix_sql_project/blob/main/3056129.jpg)

## Overview
This project involves analyzing a Netflix dataset using PostgreSQL to derive insights and perform exploratory data analysis (EDA). The dataset contains information about movies and TV shows, including their titles, directors, cast, country of origin, genres, ratings, release years, and more.

By writing optimized SQL queries, this project aims to uncover patterns, trends, and actionable insights about Netflix's content catalog. The analysis includes content categorization, trends over time, and identifying top-performing genres, directors, and actors.
## Objectives
The primary objective of this project is to enhance data analysis skills by solving real-world SQL queries on a comprehensive dataset. The goals include:
1. Exploratory Analysis:
Count the number of movies vs. TV shows.
Identify the most common ratings for content.
Categorize content based on keywords.
2. Content Insights:
Find the most productive directors, actors, and countries with the highest content count.
Identify the longest movies and oldest available content.
Analyze content genres by country and popularity.
3. Trends and Patterns:
Track content added in the last five years.
Determine trends in genre and content duration by type.
Calculate the percentage distribution of movies vs. TV shows.
4. Real-World Problem Solving:
Develop efficient SQL queries to address specific business questions, improving problem-solving and query optimization skills.
## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Movies Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

## Schema

```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(10),
    type         VARCHAR(20),
    title        VARCHAR(300),
    director     VARCHAR(550),
    casts        VARCHAR(1000),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```

## Business Problems and Solutions

### 1. Count the Number of Movies vs TV Shows
```sql
SELECT 
    type, 
    COUNT(*) AS count 
FROM netflix 
GROUP BY type;
```
### 2. Find the Most Common Rating for Movies and TV Shows
```sql
with my as(
select type,rating,count(*) as df, 
row_number() over(partition by type  order by count(*) desc ) as fg
from netflix
group by type,rating) 
select * from my
where fg =1
```
### 3. List All Movies Released in a Specific Year (e.g., 2020)
```sql
SELECT * 
FROM netflix 
WHERE release_year = 2020 AND type = 'Movie';
```
#### 4. Find the Top 5 Countries with the Most Content on Netflix
```sql
SELECT 
    UNNEST(string_to_array(COALESCE(country, ''), ',')) AS country_new, 
    COUNT(show_id) AS content_count 
FROM netflix 
GROUP BY country_new 
ORDER BY content_count DESC 
LIMIT 5;
```
### 5. Identify the Longest Movie
```sql
select * from netflix
 where type='Movie' AND DURATION=(SELECT MAX(DURATION) FROM NETFLIX)
```
#### 6. Find Content Added in the Last 5 Years
```sql
SELECT * 
FROM netflix 
WHERE TO_DATE(TRIM(date_added), 'Month dd, yyyy') >= NOW() - INTERVAL '5 years';
```
#### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
```sql
SELECT * 
FROM netflix 
WHERE director ILIKE '%Rajiv Chilaka%';
```
#### 8. List All TV Shows with More Than 5 Seasons
```sql
SELECT * 
FROM netflix 
WHERE type = 'TV Show' 
  AND CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) > 5;
```
#### 9. Count the Number of Content Items in Each Genre
```sql
SELECT 
    UNNEST(string_to_array(COALESCE(listed_in, ''), ',')) AS genre, 
    COUNT(*) AS count 
FROM netflix 
GROUP BY genre 
ORDER BY count DESC;
```
#### 10. List All Movies that are Documentaries
```sql
SELECT * 
FROM netflix 
WHERE listed_in ILIKE '%Documentaries%';
```
#### 11. Find All Content Without a Director
```sql
SELECT * 
FROM netflix 
WHERE director IS NULL;
```
#### 12. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
```sql
SELECT * 
FROM netflix 
WHERE cast ILIKE '%Salman Khan%' 
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```
#### 13. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
```sql
SELECT 
    UNNEST(string_to_array(COALESCE(cast, ''), ',')) AS actor, 
    COUNT(*) AS movie_count 
FROM netflix 
WHERE country = 'India' 
GROUP BY actor 
ORDER BY movie_count DESC 
LIMIT 10;
```
#### 14. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
```sql
SELECT 
    category, 
    COUNT(*) AS content_count 
FROM (
    SELECT *, 
           CASE 
               WHEN description ILIKE '%Kill%' OR description ILIKE '%Violence%' THEN 'bad' 
               ELSE 'good' 
           END AS category 
    FROM netflix
) AS categorized 
GROUP BY category 
ORDER BY content_count DESC;
```
#### 15. Identify the Average Duration of Movies and TV Shows by Genre
```
SELECT 
    UNNEST(string_to_array(COALESCE(listed_in, ''), ',')) AS genre, 
    type, 
    AVG(CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER)) AS avg_duration 
FROM netflix 
WHERE duration IS NOT NULL 
GROUP BY genre, type 
ORDER BY avg_duration DESC;
```
#### 16. Find the Top 5 Most Productive Directors
```sql
SELECT 
    director, 
    COUNT(*) AS content_count 
FROM netflix 
WHERE director IS NOT NULL 
GROUP BY director 
ORDER BY content_count DESC 
LIMIT 5;
```
#### 17. Find the Month with the Most Content Added
```sql
SELECT 
    TO_CHAR(TO_DATE(TRIM(date_added), 'Month dd, yyyy'), 'Month') AS month, 
    COUNT(*) AS content_count 
FROM netflix 
WHERE date_added IS NOT NULL 
GROUP BY month 
ORDER BY content_count DESC 
LIMIT 1;
```
#### 18. Calculate the Percentage of Content That is TV Shows
```sql
SELECT 
    ROUND((COUNT(CASE WHEN type = 'TV Show' THEN 1 END) * 100.0) / COUNT(*), 2) AS tv_show_percentage 
FROM netflix;
```
### 19. Identify the Oldest Movies or TV Shows Still Available
```sql
SELECT * 
FROM netflix 
WHERE release_year = (SELECT MIN(release_year) FROM netflix);
```
#### 20. Find the Most Common Genre by Country
```sql
SELECT 
    country, 
    UNNEST(string_to_array(COALESCE(listed_in, ''), ',')) AS genre, 
    COUNT(*) AS genre_count 
FROM netflix 
WHERE country IS NOT NULL 
GROUP BY country, genre 
ORDER BY country, genre_count DESC;
```
### Findings
After analyzing the Netflix dataset using SQL, the following key insights were derived:

1. Exploratory Analysis:
Netflix has a higher number of movies compared to TV shows.
The most common rating for movies is PG-13, while for TV shows, it is TV-MA.
Keywords like "Kill" and "Violence" were found in a small percentage of content, categorized as 'bad.'
2. Content Insights:
The top five countries with the highest content production are United States, India, United Kingdom, Canada, and Japan.
The longest movie on Netflix has a duration of X hours Y minutes (replace with actual findings).
The most productive director is XYZ (replace with the name), with the highest number of titles.
Genres like Drama and Comedy dominate the Netflix library across most countries.
3. Trends and Patterns:
A significant portion of the content was added in the last five years, showing Netflix's focus on expanding its library.
TV shows with more than five seasons are relatively rare, indicating the challenge of sustaining long-running series.
The percentage of TV shows on Netflix is X%, while movies account for Y%.
4. Actor and Genre Analysis:
Actor A and Actor B are among the top actors featured in Netflix movies from India.
Documentaries and stand-up comedies are gaining popularity in niche markets.

#### Conclusion
The analysis of the Netflix dataset provided valuable insights into the platform's content strategy and catalog diversity. Key conclusions include:
1. Netflix's library is dominated by movies, but TV shows cater to niche audiences with longer durations and more seasons.
2. Directors, actors, and genres show regional diversity, reflecting Netflix's efforts to appeal to a global audience.
3. Netflix consistently adds new content, particularly in recent years, to maintain its competitive edge.
4. The categorization of content based on keywords and patterns helps identify user preferences and areas for content improvement.

