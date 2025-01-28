# Netflix_Movies_Shows_Data_Analysis
![Netflix_Logo](https://github.com/SanjayKerannavar/Netflix_SQL_Project/blob/a4674672fbd97aa3cce7a7d03a0f7161051c46ad/Image.png)

## Overview 
This project involves a comprehensive analysis of Netflix Movies and TV shows data using SQL. The goal is to extract valuable insights and 
answer various insights and answer various business questions based on the dataset. The following README provides a detailed account of the 
projects objective, business problems, solutions, findings, and conclusion.

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

### 5. Identify the Longest Movie
```SQL
SELECT 	*
FROM netflix
WHERE typ = 'Movie'
and 
duration = (select max (duration) from netflix);
```

### 6. Find Content Added in the Last 5 Years
```SQL
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
```

### 7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
```SQL
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS D_Name
    FROM netflix
) AS S
WHERE D_Name = 'Rajiv Chilaka';
```

### 8. List All TV Shows with More Than 3 Seasons
```SQL
SELECT *
FROM netflix
WHERE typ = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 3;
```

 ### 9. Count the Number of Content Items in Each Genre
```SQL
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
```

 ### 10.Find each year and the average numbers of content release in India on netflix.
```SQL
SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```

### 11. List All Movies that are Documentaries
```SQL
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
```

### 12. Find All Content Without a Director
```SQL
SELECT * 
FROM netflix
WHERE director IS NULL;
```

### 13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
```SQL
SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
```

### 14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India.
```SQL
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```

### 15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords.
```SQL
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN descriptions ILIKE '%kill%' OR descriptions ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
```

### 16.	Find all movies or TV shows releasedin the same year as a specific title (e.g., "Stranger Things").
```SQL
SELECT 
    title, 
    typ, 
    release_year
FROM Netflix
WHERE release_year = (
    SELECT release_year 
    FROM Netflix 
    WHERE title = 'Stranger Things'
);
```




