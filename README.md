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

###1. Count the Number of Movies vs TV Shows
```sql
SELECT 
    type, 
    COUNT(*) AS count 
FROM netflix 
GROUP BY type;
```
###2. Find the Most Common Rating for Movies and TV Shows
```sql
SELECT 
    type, 
    rating, 
    COUNT(*) AS most_common 
FROM netflix 
GROUP BY type, rating 
HAVING NOT (type = 'Movie' AND rating = 'TV-MA') 
ORDER BY most_common DESC 
LIMIT 2;
```

3. List All Movies Released in a Specific Year (e.g., 2020)

SELECT * 
FROM netflix 
WHERE release_year = 2020 AND type = 'Movie';


---

4. Find the Top 5 Countries with the Most Content on Netflix

SELECT 
    UNNEST(string_to_array(COALESCE(country, ''), ',')) AS country_new, 
    COUNT(show_id) AS content_count 
FROM netflix 
GROUP BY country_new 
ORDER BY content_count DESC 
LIMIT 5;


---

5. Identify the Longest Movie

SELECT * 
FROM netflix 
WHERE type = 'Movie' 
  AND CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) = (
      SELECT MAX(CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER)) 
      FROM netflix 
      WHERE type = 'Movie' AND duration ~ '^[0-9]+ min$'
  );


---

6. Find Content Added in the Last 5 Years

SELECT * 
FROM netflix 
WHERE TO_DATE(TRIM(date_added), 'Month dd, yyyy') >= NOW() - INTERVAL '5 years';


---

7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'

SELECT * 
FROM netflix 
WHERE director ILIKE '%Rajiv Chilaka%';


---

8. List All TV Shows with More Than 5 Seasons

SELECT * 
FROM netflix 
WHERE type = 'TV Show' 
  AND CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER) > 5;


---

9. Count the Number of Content Items in Each Genre

SELECT 
    UNNEST(string_to_array(COALESCE(listed_in, ''), ',')) AS genre, 
    COUNT(*) AS count 
FROM netflix 
GROUP BY genre 
ORDER BY count DESC;


---

10. List All Movies that are Documentaries

SELECT * 
FROM netflix 
WHERE listed_in ILIKE '%Documentaries%';


---

11. Find All Content Without a Director

SELECT * 
FROM netflix 
WHERE director IS NULL;


---

12. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

SELECT * 
FROM netflix 
WHERE cast ILIKE '%Salman Khan%' 
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;


---

13. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

SELECT 
    UNNEST(string_to_array(COALESCE(cast, ''), ',')) AS actor, 
    COUNT(*) AS movie_count 
FROM netflix 
WHERE country = 'India' 
GROUP BY actor 
ORDER BY movie_count DESC 
LIMIT 10;


---

14. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

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


---

15. Identify the Average Duration of Movies and TV Shows by Genre

SELECT 
    UNNEST(string_to_array(COALESCE(listed_in, ''), ',')) AS genre, 
    type, 
    AVG(CAST(SPLIT_PART(duration, ' ', 1) AS INTEGER)) AS avg_duration 
FROM netflix 
WHERE duration IS NOT NULL 
GROUP BY genre, type 
ORDER BY avg_duration DESC;


---

16. Find the Top 5 Most Productive Directors

SELECT 
    director, 
    COUNT(*) AS content_count 
FROM netflix 
WHERE director IS NOT NULL 
GROUP BY director 
ORDER BY content_count DESC 
LIMIT 5;


---

17. Find the Month with the Most Content Added

SELECT 
    TO_CHAR(TO_DATE(TRIM(date_added), 'Month dd, yyyy'), 'Month') AS month, 
    COUNT(*) AS content_count 
FROM netflix 
WHERE date_added IS NOT NULL 
GROUP BY month 
ORDER BY content_count DESC 
LIMIT 1;


---

18. Calculate the Percentage of Content That is TV Shows

SELECT 
    ROUND((COUNT(CASE WHEN type = 'TV Show' THEN 1 END) * 100.0) / COUNT(*), 2) AS tv_show_percentage 
FROM netflix;


---

19. Identify the Oldest Movies or TV Shows Still Available

SELECT * 
FROM netflix 
WHERE release_year = (SELECT MIN(release_year) FROM netflix);


---

20. Find the Most Common Genre by Country

SELECT 
    country, 
    UNNEST(string_to_array(COALESCE(listed_in, ''), ',')) AS genre, 
    COUNT(*) AS genre_count 
FROM netflix 
WHERE country IS NOT NULL 
GROUP BY country, genre 
ORDER BY country, genre_count DESC;





## Business Problems and Solutions

## Business Problems and Solutions
