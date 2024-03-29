# E-Commerce-Study
[Click here](https://public.tableau.com/app/profile/angel.zapata2615/viz/E-CommerceDashboard_16827981312070/Dashboard1?publish=yes) for my Tableau Dashboard!
<p align="center"> 
<img width="644" alt="Screenshot 2023-08-14 142933" src="https://github.com/azap2124/e-commerce_study/assets/126125206/8c7559b6-3572-4817-afb1-a8584d977f05">
</p>

## Introduction: Exploring Product Sales and Merchant Metrics
In the dynamic world of e-commerce, businesses have to understand consumer behavior, product trends, and merchant performance to stay ahead in a competitive market. To shed light on these important aspects, this data analysis project embarks on an exploration of an extensive dataset. Our main objective is to tackle questions that explore different aspects of product sales and merchant-related metrics.

In this data analysis project, we explore women's shopping preferences on the Wish platform. We aim to answer the following questions: 
1. What are the most popular products based on units sold? How do they compare in terms of price and rating?
2. Do products with ad boosts have higher sales compared to those without ad boosts?
3. What are the most common product tags and how do they relate to sales?
4. Is there a relationship between merchant rating and product quality as indicated by the badges?
5. Do companies that sell to many countries have higher revenues? 
6. Is there a relationship between merchant profile pictures and sales?

## Preparing our data
The dataset I used can be found [here](https://data.world/jfreex/summer-products-and-sales-performance-in-e-commerce-on-wish). This data comes from the Wish platform. It incorporates data gathered from 2020. 

This data covers information on various product attributes in the database. It includes details such as the original title of the product, its price, retail price, units sold, ad boosts usage, ratings, and rating counts. The dataset also contains information on badges associated with each product, product tags, color, size variation, countries shipped to, origin country, merchant rating count, merchant rating, whether the merchant has a profile picture, and unique product IDs. With this extensive dataset, we can gain valuable insights into product trends, merchant performance, and customer preferences in an online marketplace.

I will be using Microsoft SQL Server Management Studio to analyze the data.

## Processing
#### Cleaning the Data
* Used Excel to remove duplicates in the data, streamlining the dataset and ensuring that each record is unique.
* Removed columns that were irrelevant to this analysis, streamlining the dataset further and focusing only on the essential variables pertinent to our research objectives. 
* For consistency, I converted all columns to the snake_case naming convention.
* Converted data types when importing data to SQL Server to it's corresponding data types in the database schema. This ensures the data is accurately stored in the database, allowing for efficient querying.

  
## Analysis
### What are the most popular products based on units sold? How do they compare in terms of price and rating?
The following code retrieves the top 10 products from the *summer_products* table in the *wish* database based on the highest number of units sold. The products are then sorted in descending order based on the number of units sold, presenting the highest selling products with their corresponding ratings and revenue generated. Here are the results along with their corresponding revenues: 
1. Sleeveless Lace Crop Tops - $2,500,000
2. Summer Stranger Things Tracksuit 2 Piece Outfit - $4,800,000
3. ZANZEA Strand Retro Longtop Kleid Kaftan Dress - $700,000
4. Beachwear Spaghetti Strap Jumpsuit Rompers - $3,300,000
5. Summer Bandage Cut Out Crop Top - $2,200,000
6. Summer V-Neck Loose Jumpsuit - $1,900,000
7. Loose Lace Blouse with Bat Sleeves - $300,000
8. Beach Casual Striped T-shirt Mini Dress - $4,050,000
9. Plus Size Summer V Neck Floral Printed Dresses - $300,000
10. Summer LOVE Printed Sleeveless Tank Top T-Shirt - $250,000
```
-- Top 10 units by revenue
-- Shows highest units sold and their ratings 
SELECT TOP 10
	title_orig,
	units_sold,
	retail_price,
	(units_sold*retail_price) AS total_revenue,
	rating,
	rating_count
FROM wish. dbo.summer_products
WHERE title_orig IS NOT NULL
ORDER BY units_sold DESC;
```
Next, I aimed to analyze the lowest rated products. For a balanced comparison, I found the average rating count across all products. This allowed me to fairly compare the products based on consistent rating counts. The average rating count for all products is **897 reviews**. 
```
-- Average rating count
-- Shows average rating count for all the products 
SELECT AVG(rating_count) AS avg_rating_count
FROM wish. dbo.summer_products;
```
Moving forward, I was curious to identify the top 10 products with the lowest ratings. To ensure a fair comparison, these products were required to have a minimum of 800 reviews and bear the lowest ratings. As we can see, three of them cater to plus-size women, while three products feature v-neck styles. These observations suggest potential areas for improvements. The products and their ratings are: 
1. **Plus Size** Summer Cross Bandage Loose Dress - 2.93
2. Summer Sleepwear Lace Night Dress - 2.97
3. **Plus Size** Summer Sleeveless Low Cut Tank Top - 3.01
4. Summer Tie Dye Printed Two Piece Set - 3.13
5. **Plus Size** Loose V neck long sleeve Floral print dress - 3.18
6. Summer **V-neck** Short Sleeve Dress - 3.19
7. Summer Beachwear **V-neck** Sleeveless Boho Mini Dress - 3.23
8. Summer Bikini Set High waist Bathing Suit - 3.25
9. Summer Loose **V Neck** Blouse Shirt - 3.27
10. One-Piece Push Up Bikini Monokini Swimsuit - 3.28
```
-- Top 10 worst rated products
-- Shows worst rated products with a rating count of 800 or more
SELECT TOP 10
	title_orig,
	units_sold,
	retail_price,
	(units_sold*retail_price) AS total_revenue,
	rating,
	rating_count
FROM wish. dbo.summer_products
WHERE title_orig IS NOT NULL AND rating_count >= 800
ORDER BY rating ASC;
```
The average rating for the best-selling products and the least-selling products are provided below. A difference of 0.153 could be considered minor and might not signify a major difference in the customer's perceived quality. This observation makes me wonder how the top-selling products manage to achieve such high sales volumes despite the small difference in average ratings. To obtain these averages, I created subqueries.
* Best-selling: 3.946
* Least-selling: 3.793
```
-- Average rating for best selling 10 products
SELECT AVG (rating) AS avg_best_selling_products
FROM
(
SELECT TOP 10
	title_orig,
	units_sold,
	retail_price,
	(units_sold*retail_price) AS total_revenue,
	rating
FROM wish. dbo.summer_products
WHERE title_orig IS NOT NULL
ORDER BY units_sold DESC
)
AS avg_best_selling_products;

-- Average rating for least selling products
SELECT AVG (rating) AS avg_least_selling_products
FROM
(
SELECT TOP 10
	title_orig,
	units_sold,
	retail_price,
	(units_sold*retail_price) AS total_revenue,
	rating,
	rating_count
FROM wish. dbo.summer_products
WHERE title_orig IS NOT NULL AND rating_count >= 800
ORDER BY units_sold ASC
)
AS avg_least_selling_products;
```

### Do products with ad boosts have higher sales compared to those without ad boosts?
After running these codes, it's clear that there isn't a direct link between ad boosts and sales. There's around a $11,000 difference, but given the overall revenue generated, this gap doesn't really seem all that significant. I used subqueries to calculate these. 
* Average revenue for products that use ads: $100,852.38
* Average revenue for products that don't use ads: $111,721.59
```
-- Average revenue for products that use ads
SELECT ROUND(AVG(total_revenue),2) AS uses_ads
FROM
(
SELECT title_orig,
	(units_sold*retail_price) AS total_revenue
FROM wish. dbo.summer_products
WHERE title_orig IS NOT NULL AND uses_ad_boosts = 1
) 
AS avg_revenue_ads;

-- Average revenue for products that don't use ads
SELECT ROUND(AVG(total_revenue),2) AS no_ads
FROM
(
SELECT title_orig,
	(units_sold*retail_price) AS total_revenue
FROM wish. dbo.summer_products
WHERE title_orig IS NOT NULL AND uses_ad_boosts = 0
) 
AS avg_revenue_no_ads;
```

### What are the most common product tags and how do they relate to sales?
Below are the tags that brought in the most revenue. I counted the number of tags in each product and then added their revenues using a GROUP BY clause. 
|value|	tag_count|total_revenue|
|-----|----------|-------------|
|Women's Fashion|1,301|$142,085,944|
|Women|950|$112,049,239|
|Fashion|1,070|$111,594,890|
|Summer|1,304|$94,012,398|
|Casual|894|$68,669,314|
|Dress|538|$66,922,725|
|sexy|325|$64,130,228|
|Tops|503|$55,257,908|
|Plus Size|632|$48,916,631|
|sleeveless|570|$48,889,972|

```
-- Divides, counts and compares the tags by revenue 
SELECT value, 
	COUNT(value) AS tag_count,
	SUM(units_sold * retail_price) AS total_revenue 
FROM wish.dbo.summer_products
CROSS APPLY STRING_SPLIT(tags, ',') AS tag
GROUP BY value
ORDER BY total_revenue DESC;
```

###  Is there a relationship between merchant rating and product quality as indicated by the badges?
* There's 151 merchants with at least one badge
* There's 1405 merchants without badges
```
-- Merchant rating and number of badges
SELECT COUNT(merchant_rating) AS merchants_with_badges
FROM wish.dbo.summer_products
WHERE badges_count >= 1;

-- Merchant rating for badge non-holders
SELECT COUNT(merchant_rating) AS merchants_no_badges
FROM wish.dbo.summer_products
WHERE badges_count = 0;
```
* Average rating for badge holders: 4.168
* Average rating for badge non-holders: 4.022
```
-- Average rating for mechants with at least one badge
SELECT AVG(merchant_rating) AS avg_rating
FROM wish.dbo.summer_products
WHERE badges_count >= 1;

-- Average rating for badge non-holders
SELECT AVG(merchant_rating) AS avg_rating
FROM wish.dbo.summer_products
WHERE badges_count = 0;
```
* Average rating for merchants with the product quality badge: 4.1705
* Average rating for merchants that **do not** hold a product quality badge: 4.0222
```
-- Average rating for merchants with the product quality badge
SELECT AVG(merchant_rating) AS avg_rating
FROM wish.dbo.summer_products
WHERE badge_product_quality = 1;

-- Average rating for merchants that DO NOT hold product quality badge
SELECT AVG(merchant_rating) AS avg_rating
FROM wish.dbo.summer_products
WHERE badge_product_quality = 0;  
```
Having a badge seems to be associated with slightly higher average ratings for merchants, but even merchants without a badge maintain a relatively high average rating. This might suggest that other factors, such as customer service, shipping efficiency, and overall shopping experience, could also play a significant role in determining merchant ratings.

###  Do companies that sell to many countries have higher revenues? 
To address this question, my initial step was to calculate the average number of countries to which these products are shipped to. This served as a base measure for the subsequent analysis.
* In average, these products are shipped to **40** different countries. 
```
-- Average of countries_shipped_to
-- Calculates the average of how many countries are shipped to per product
SELECT AVG(countries_shipped_to) AS average_countries_shipped
FROM wish.dbo.summer_products;
```
Subsequently, I formulated a CASE expression to classify the products. They were labeled as 'low' for those shipped to fewer than 29 countries, 'average' for those shipped between 30 and 60 countries, and 'high' for those shipped to 61 countries or more. I then proceeded to create a subquery to determine the count within each individual category. I was interested if whether there was a noticeable difference between the 'low' and 'high' categories. 
* Products that ship to less than 29 countries: 329
* Products that ship to more than 61 countries: 98
```
-- Count of products that ship to less than 29 countries 
SELECT COUNT(countries_shipped_to) AS low_countries
FROM
(
SELECT (units_sold * retail_price) AS total_revenue,
countries_shipped_to,
	CASE
		WHEN (countries_shipped_to) < 29 THEN 'low'
		WHEN (countries_shipped_to) BETWEEN 30 AND 60 THEN 'average'
		WHEN (countries_shipped_to) > 61 THEN 'high'
	END countries_level
FROM wish.dbo.summer_products
)
AS low_average
WHERE countries_level = 'low';

-- Count of products that ship to more than 61 countries 
SELECT COUNT(countries_shipped_to) AS high_countries
FROM
(
SELECT (units_sold * retail_price) AS total_revenue,
countries_shipped_to,
	CASE
		WHEN (countries_shipped_to) < 29 THEN 'low'
		WHEN (countries_shipped_to) BETWEEN 30 AND 60 THEN 'average'
		WHEN (countries_shipped_to) > 61 THEN 'high'
	END countries_level
FROM wish.dbo.summer_products
)
AS low_average
WHERE countries_level = 'high';
```
I then sought to find the average revenue for each individual categories using subqueries. There was indeed a significant difference ($37,552) between products shipped to less than 29 countries and products shipped to 61 countries or more. 
* Average revenue for products that ship to less than 29 countries: $40,592.47
* Average revenue for products that ship to more than 61 countries: $78,144.74
* Average revenue for products that ship to between 30 and 60 countries: $128,272.41
```
-- Average revenue for products that ship to less than 29 countries 
SELECT ROUND(AVG(total_revenue),2) AS avg_revenue_low 
FROM
(
SELECT (units_sold * retail_price) AS total_revenue,
countries_shipped_to,
	CASE
		WHEN (countries_shipped_to) < 29 THEN 'low'
		WHEN (countries_shipped_to) BETWEEN 30 AND 60 THEN 'average'
		WHEN (countries_shipped_to) > 61 THEN 'high'
	END countries_level
FROM wish.dbo.summer_products
)
AS low_average
WHERE countries_level = 'low';

-- Average revenue for products that ship to more than 61 countries 
SELECT ROUND(AVG(total_revenue),2) AS avg_revenue_high 
FROM
(
SELECT (units_sold * retail_price) AS total_revenue,
countries_shipped_to,
	CASE
		WHEN (countries_shipped_to) < 29 THEN 'low'
		WHEN (countries_shipped_to) BETWEEN 30 AND 60 THEN 'average'
		WHEN (countries_shipped_to) > 61 THEN 'high'
	END countries_level
FROM wish.dbo.summer_products
)
AS high_average
WHERE countries_level = 'high';

-- Average revenue for products that ship to between 30 and 60 countries 
SELECT ROUND(AVG(total_revenue),2) AS avg_revenue_medium
FROM
(
SELECT (units_sold * retail_price) AS total_revenue,
countries_shipped_to,
	CASE
		WHEN (countries_shipped_to) < 29 THEN 'low'
		WHEN (countries_shipped_to) BETWEEN 30 AND 60 THEN 'average'
		WHEN (countries_shipped_to) > 61 THEN 'high'
	END countries_level
FROM wish.dbo.summer_products
)
AS medium_average
WHERE countries_level = 'medium';
```
### Is there a relationship between merchant profile pictures and sales?
There's a significant difference between products where merchants have profile pictures to those who don't. This could mean that customers perceive profiles with profile pictures as trustworthy and credible. 
* Products where merchants have a profile pictures sold a lot more units than those who don't have a profile picture
* Has profile picture sold on average: 7,617 units 
* Does not have a profile picture sold on average: 3,824 units
```
SELECT AVG(units_sold) AS avg_units_sold
FROM wish.dbo.summer_products
WHERE merchant_has_profile_picture = 1;

SELECT AVG(units_sold) AS avg_units_sold
FROM wish.dbo.summer_products
WHERE merchant_has_profile_picture = 0;
``` 
## Sharing my Results
In the course of this data analysis project, I uncovered several noteworthy insights that provide valuable understanding into products and customer preferences on the Wish platform. Here's what the data revealed:
### Low-Rated Products
I identified the top 10 products with the lowest ratings, each with a minimum of 800 reviews. 
* Variety in Categories: Among the lowest-rated products, three are tailored for plus-size women, while another 	three feature v-neck styles. This suggests potential opportunities for improvement in these specific segments.
  
### Customer Perception and Behavior
1. **Average Ratings vs. Sales**: There was a slight difference of 0.153 in average ratings between best-selling and least-selling products which may not impact customer perceptions of quality. This raises questions about what factors contribute to the high sales volumes of top-rated products. Factors such as price, product relevance, visual appeal, and personal preferences also play a role. It's possible that the products' strengths in these other areas contribute to their high sales volumes.
2. **Ad Boosts**: Surprisingly, the revenue difference of around $11,000 between products using and not using ad boosts doesn't seem substantial given the overall revenue generated. This prompts a closer examination of the effectiveness of ad boosts in driving revenue.
3. **Tags**: Tags associated with 'Women's Fashion,' 'Women,' and 'Fashion' played a pivotal role in revenue generation. This insight can guide marketing efforts.
4. **Trustworthiness of Profile Pictures**: The presence of profile pictures for merchants led to a significant difference in units sold compared to products without them. This suggests that customers are more inclined to trust products associated with merchants who have profile pictures.

### Geographical Impact
Examining revenue on shipping destinations per product revealed the following insights:
* Products shipped to less than 29 Countries: Products shipped to fewer than 29 countries generated an average revenue of $40,592.47.
* More than 61 Countries: Products shipped to over 61 countries yielded an average revenue of $78,144.74. The broader reach highlights the potential of catering to a global demand.
* 30 to 60 Countries: Products sent to 30 to 60 countries achieved an average revenue of $128,272.41. 
The geographical influence on revenue encourages us to consider adapting shipping strategies to more countries. This approach could uncover revenue potential and enhance market presence.


