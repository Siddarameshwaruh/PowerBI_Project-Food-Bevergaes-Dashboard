# PowerBI_Project-Food-Bevergaes-Dashboard

**1. Demographic Insights (examples)**

***a. Who prefers energy drink more? (male/female/non-binary?)***

	SELECT 
	    gender AS Gender,
	    COUNT(respondent_id) AS `Total Respondents`
	FROM
	    dim_repondents
	GROUP BY gender
	ORDER BY COUNT(respondent_id) DESC;

***b. Which age group prefers energy drinks more?***

SELECT 
    age, COUNT(respondent_id) AS total_respondents
FROM
    dim_repondents
GROUP BY age
ORDER BY total_respondents DESC;

***c. Which type of marketing reaches the most Youth (15-30)?***

WITH cte1 AS
(
SELECT  r.age, 
			s.marketing_channels as `Marketing Channels`,
            COUNT(s.respondent_id) as `Total Respondents`, 
            DENSE_RANK() OVER(PARTITION BY r.age ORDER BY count(s.respondent_id) DESC) AS rank1
	FROM dim_repondents r JOIN fact_survey_responses s
     USING(respondent_id) 
     WHERE Age IN ("15-18", "19-30")
      GROUP BY Age, `Marketing Channels`
      ORDER BY count(s.respondent_id) DESC
      )
SELECT age AS "Age",  `Marketing Channels`, `Total Respondents` FROM cte1 
	WHERE `Total Respondents` IN 
		(SELECT max(`Total Respondents`) FROM cte1	
         GROUP BY age
			) AND rank1 = 1


**2. Consumer Preferences:**

***a. What are the preferred ingredients of energy drinks among respondents?***

SELECT DISTINCT
    ingredients_expected AS `Expected Ingredients`,
    COUNT(Respondent_ID) AS `Total Respondents`
FROM
    fact_survey_responses
GROUP BY Ingredients_expected
ORDER BY COUNT(Respondent_ID) DESC

***b. What packaging preferences do respondents have for energy drinks?***

SELECT DISTINCT
    packaging_preference, COUNT(Respondent_ID) total_respondents
FROM
    fact_survey_responses
GROUP BY packaging_preference
ORDER BY total_respondents DESC


**3. Competition Analysis:**

***a. Who are the current market leaders?***

SELECT DISTINCT
    current_brands, COUNT(respondent_id) AS total_respondents
FROM
    fact_survey_responses
GROUP BY current_brands
ORDER BY total_respondents DESC
LIMIT 5;

***b. What are the primary reasons consumers prefer those brands over ours?***

WITH cte1 AS (
SELECT DISTINCT current_brands, count(respondent_id) 
	FROM fact_survey_responses s
    GROUP BY current_brands
    ORDER BY count(respondent_id) DESC
    LIMIT 3)
SELECT c.current_brands, s.reasons_for_choosing_brands, count(s.reasons_for_choosing_brands) AS number_of_responses 
FROM cte1 AS c JOIN fact_survey_responses s
USING(current_brands)
GROUP BY c.current_brands, s.reasons_for_choosing_brands
HAVING s.reasons_for_choosing_brands <> "other"
ORDER BY current_brands, s.reasons_for_choosing_brands DESC;




















   
