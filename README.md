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

## Business Problems and Solutions
