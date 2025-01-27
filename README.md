# Netflix_Movies_Shows_Data_Analysis.
![Netflix_Logo](https://github.com/SanjayKerannavar/Netflix_SQL_Project/blob/de51c32570a8cf76c7fed1a231c21f3741d16ba2/Net1.png)

## Overview 
This project involves a comprehensive analysis of Netflix Movies and TV shows data using SQL. The goal is to extract valuable insights and 
answer various insights and answer various business questions based on the dataset. The following README provides a detailed account of the 
projects objective, business problems, solutions, findings, and conclusion, 

## Objective 

Analyze the distribution of the content types (Movies VS TV Shows).
Identifying the most common rating for movies and TV Shows.
Listing and analyzing content based on specific criteria and keywords.
Explore and categorize content based on specific criteria and keywords.

## Data_Sets

The dataset of the project is sourced from Kaggle dataset.

## Schema 

```SQL
create table Netflix(
show_id	  			varchar(10),
typ 	 			varchar(10),
title	 			varchar(150), 
director			varchar(210),
casts				varchar(1000),
country  			varchar(150), 
date_added  		varchar(50),
release_year 		 int,
rating    			 varchar(50),
duration  			 varchar(50),
listed_in 			 varchar(100),
descriptions	 	 varchar(300)
);
```

## Business Problems and Solutions 

### 1.Count the number of Movies vs TV Shows.
```SQL
SELECT  
Typ, 
count(*) as Total_TV_Shows 
FROM netflix 
GROUP by typ;
```

### 2. Find the Most Common Rating for Movies and TV Shows.
```SQL
WITH RatingCounts AS (
    SELECT 
        typ,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY typ, rating
),
RankedRatings AS (
    SELECT 
        typ,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY typ ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    typ,
    rating AS rating
FROM RankedRatings
WHERE rank = 1;
```

### 3 List All Movies Released in a Specific Year (e.g., 2020)
```SQL
SELECT * FROM netflix
where release_year = 2020 
and
typ = 'Movie' ;
```

### 4. Find the Top 5 Countries with the Most Content on Netflix
```SQL
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```





