Netflix Movies and TV Shows Data Analysis using SQL
Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.
Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.
Dataset

```
Business Problems and Solutions
1. Count the Number of Movies vs TV Shows
```sql
SELECT TYPE,COUNT(*) AS TOTAL_CONTENT
FROM NETFLIX_TITLES
GROUP BY TYPE;
```
Objective: Determine the distribution of content types on Netflix.
2. Find the Most Common Rating for Movies and TV Shows
```sql
SELECT TYPE,RATING 
FROM
 (select TYPE,
	RATING,COUNT(*),
	RANK() OVER(PARTITION BY TYPE ORDER BY COUNT(*) DESC) AS RANKING
 FROM NETFLIX_TITLES
 GROUP BY 1,2
 ) AS T1
 WHERE RANKING=1;
```
Objective: Identify the most frequently occurring rating for each type of content.
3. List All Movies Released in a Specific Year (e.g., 2020)
```sql
SELECT *
FROM NETFLIX_TITLES
WHERE TYPE='MOVIE' AND RELEASE_YEAR=2020;
```
Objective: Retrieve all movies released in a specific year.
4. Find the Top 5 Countries with the Most Content on Netflix
```sql
SELECT 
    jt.new_country AS COUNTRY,
    COUNT(*) AS TOTAL_CONTENT
FROM NETFLIX_TITLES,
JSON_TABLE(
    CONCAT('["', REPLACE(COUNTRY, ',', '","'), '"]'),
    '$[*]' COLUMNS (
        new_country VARCHAR(100) PATH '$'
    )
) jt
WHERE COUNTRY IS NOT NULL
GROUP BY jt.new_country
ORDER BY TOTAL_CONTENT DESC
LIMIT 5;
```
Objective: Identify the top 5 countries with the highest number of content items.
5. Identify the Longest Movie
```sql
SELECT * FROM NETFLIX_TITLES
WHERE TYPE='MOVIE' AND DURATION=( SELECT
	MAX(DURATION) FROM NETFLIX_TITLES);
```
Objective: Find the movie with the longest duration.
6. Find Content Added in the Last 5 Years
```sql
SELECT *
FROM NETFLIX_TITLES
WHERE STR_TO_DATE(DATE_ADDED, '%M %d, %Y') >= CURDATE() - INTERVAL 5 YEAR;
```
Objective: Retrieve content added to Netflix in the last 5 years.
7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
```sql
SELECT *
FROM NETFLIX_TITLES
WHERE DIRECTOR LIKE '%RAJIV CHILAKA';
```
Objective: List all content directed by 'Rajiv Chilaka'.
8. List All TV Shows with More Than 5 Seasons
```sql
SELECT *
FROM NETFLIX_TITLES
WHERE TYPE='TV SHOW' AND DURATION > '5 SEASONS'
ORDER BY DURATION;
```
Objective: Identify TV shows with more than 5 seasons.
9. Count the Number of Content Items in Each Genre
```sql
SELECT
    jt.genre,
    COUNT(*) AS total_count
FROM NETFLIX_TITLES nt
CROSS JOIN JSON_TABLE(
    CONCAT(
        '["',
        REPLACE(nt.LISTED_IN, ', ', '","'),
        '"]'
    ),
    '$[*]' COLUMNS (
        genre VARCHAR(100) PATH '$'
    )
) jt
GROUP BY jt.genre
ORDER BY total_count DESC;
```
Objective: Count the number of content items in each genre.
10.Find each year and the average numbers of content release in India on netflix.
return top 5 year with highest avg content release!
```sql
SELECT 
	EXTRACT(YEAR FROM STR_TO_DATE(DATE_ADDED, '%M %d, %Y')) AS YEAR,
    COUNT(*) AS COUNT,
    COUNT(*)/(SELECT COUNT(*) FROM NETFLIX_TITLES WHERE COUNTRY='India') *100 as avg_content
FROM NETFLIX_TITLES
WHERE COUNTRY='India'
GROUP BY year;
```
Objective: Calculate and rank years by the average number of content releases by India.
11. List All Movies that are Documentaries
```sql
SELECT * FROM NETFLIX_TITLES
WHERE LISTED_IN LIKE '%Documentaries';
```
Objective: Retrieve all movies classified as documentaries.
12. Find All Content Without a Director
```sql
SELECT * FROM NETFLIX_TITLES
WHERE DIRECTOR IS NULL OR TRIM(DIRECTOR)='';
```
Objective: List content that does not have a director.
13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
```sql
SELECT *
FROM NETFLIX_TITLES
WHERE `CAST` LIKE '%Salman Khan%'
  AND RELEASE_YEAR >= YEAR(CURDATE()) - 10;
```
Objective: Count the number of movies featuring 'Salman Khan' in the last 10 years.
14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
```sql
SELECT
    jt.actor,
    COUNT(*) AS movie_count
FROM NETFLIX_TITLES nt
CROSS JOIN JSON_TABLE(
    CONCAT(
        '["',
        REPLACE(REPLACE(nt.`CAST`, '"', '\\"'), ', ', '","'),
        '"]'
    ),
    '$[*]' COLUMNS (
        actor VARCHAR(200) PATH '$'
    )
) jt
WHERE nt.COUNTRY LIKE '%India%'
  AND nt.TYPE = 'Movie'
GROUP BY jt.actor
ORDER BY movie_count DESC
LIMIT 10 ;

```
Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.
15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
```sql
SELECT
    CASE
        WHEN LOWER(DESCRIPTION) LIKE '%kill%'
          OR LOWER(DESCRIPTION) LIKE '%violence%'
        THEN 'Bad'
        ELSE 'Good'
    END AS category,
    COUNT(*) AS total_content
FROM NETFLIX_TITLES
GROUP BY category;
```
Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.
Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

Thank you for your support, and I look forward to connecting with you!
