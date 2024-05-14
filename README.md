# 8W-SQL-Challenge_C1-Dannys-Diner

SQL DBMS used: PostgreSQL

https://8weeksqlchallenge.com/case-study-1/

## Case Study Questions

Each of the following case study questions can be answered using a single SQL statement:

1. What is the total amount each customer spent at the restaurant?

    SQL Statement:
    ```
    SELECT 
      customer_id, 
      SUM(price) AS total_price
      FROM sales
    INNER JOIN menu on menu.product_id = sales.product_id
    GROUP BY sales.customer_id
    ORDER BY sales.customer_id ASC
    ```
    Output:
    
    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/682cb7da-07c5-4c0a-954c-89e9cd6f4cb2)

   _Customer A had spent the most amount out of all the customers!_


   
2. How many days has each customer visited the restaurant?

    SQL Statement:
    ```
    SELECT
    	s.customer_id,
    	COUNT(DISTINCT(s.order_date)) AS days_visited
    FROM sales s
    GROUP BY s.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/5566e955-0821-4577-af71-fee5e47aca3e)

   _Customer B visited the shop the most!_

   
3. What was the first item from the menu purchased by each customer?

    SQL Statement:
    ```
    SELECT
    	sodr.customer_id,
    	sodr.product_name,
    	sodr.order_date
    FROM(
    	SELECT
    		s.customer_id,
    		s.order_date,
    		m.product_name,
    		DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS rank
    	FROM sales s
    	INNER JOIN menu m ON s.product_id = m.product_id
    ) AS sodr
    WHERE rank = 1
    GROUP BY sodr.customer_id, sodr.product_name, sodr.order_date
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/1671512f-17a8-44e7-93da-90c626df9b47)


    _Customer A had two transactions (i.e., bought products) on the same first day they had made purchase which are curry and sushi!_

4. What is the most purchased item on the menu and how many times was it purchased by all customers?

    SQL Statement:
    ```
    SELECT 
    	sales.product_id,
    	menu.product_name,
    	COUNT(sales.product_id) AS menu_item_sales_count
    FROM sales
    INNER JOIN menu on menu.product_id = sales.product_id
    GROUP BY sales.product_id, menu.product_name
    ORDER BY menu_item_sales_count DESC
    LIMIT 1
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/a372dcca-9ce8-4256-83de-13de2c1babdb)

    _Ramen is the most popular product!_

5. Which item was the most popular for each customer?

    SQL Statement:
    ```
    -- CTE to get the rank of each product_id based on the COUNT() per unique customer_id
    WITH cte_ranked_purchase_cnt AS (
    	SELECT
    		s.customer_id
    		,s.product_id
    		,COUNT(s.product_id) AS purchase_cnt
    		,DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY COUNT(s.product_id) DESC) AS ranked
    	FROM sales s
    	GROUP BY s.customer_id, s.product_id
    )
    
    -- Access the  CTE to filter all rows with ranked = 1
    -- INNER JOIN with "menu" table to access product_name
    SELECT
    	s.customer_id,
    	m.product_name,
    	s.purchase_cnt
    FROM cte_ranked_purchase_cnt s
    INNER JOIN menu m ON s.product_id = m.product_id
    WHERE ranked = 1
    ORDER BY s.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/6a7b0562-da34-45a8-a497-a6684dd435a5)

   _It seems that customer B is fond of all the products!_

   
6. Which item was purchased first by the customer after they became a member?

    SQL Statement:
    ```
	--CTE to get the rank of each purchase based on order_date
	--The order is ASC (default) to get the date of first purchase before the customer became a member
	WITH cte_order_date_ranked AS(
		SELECT
			s.customer_id
			,s.order_date
			,s.product_id
			,DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date) AS order_date_rank
		FROM sales s
		INNER JOIN members m ON s.customer_id = m.customer_id
		WHERE s.order_date > m.join_date
	)
	
	--Access CTE and get the purchases with rank = 1
	--INNER JOIN with "menu" table to get the product_name
	SELECT
		cte_odr.customer_id
		,men.product_name
		,cte_odr.order_date AS first_order_after_join
	FROM cte_order_date_ranked AS cte_odr
	INNER JOIN menu men ON cte_odr.product_id = men.product_id
	WHERE cte_odr.order_date_rank = 1
	ORDER BY cte_odr.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/baf66603-7e02-42f9-9199-3511fea6f842)

    
7. Which item was purchased just before the customer became a member?
    
    _Note: Purchases on the day customers became members are excluded from the count._
    
    SQL Statement:
    ```
	--CTE to get the rank of each purchase based on order_date
	--The order is DESC to get the most recent purchase before the customer became a member
	WITH cte_order_date_ranked AS(
		SELECT
			s.customer_id
			,s.order_date
			,s.product_id
			,DENSE_RANK() OVER(PARTITION BY s.customer_id ORDER BY s.order_date DESC) AS order_date_rank
		FROM sales s
		INNER JOIN members m ON s.customer_id = m.customer_id
		WHERE s.order_date < m.join_date
	)
	
	--Access CTE and get the purchases with rank = 1
	--INNER JOIN with "menu" table to get the product_name
	SELECT
		cte_odr.customer_id
		,men.product_name
		,cte_odr.order_date AS last_purchase_before_join
	FROM cte_order_date_ranked AS cte_odr
	INNER JOIN menu men ON cte_odr.product_id = men.product_id
	WHERE cte_odr.order_date_rank = 1
	ORDER BY cte_odr.customer_id
    ```
    Output:

   ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/1a02c99a-0522-4f39-a3f0-a389011c02c8)


   _Customer A purchase both sushi and curry on the last purchase before joining_

    
8. What is the total items and amount spent for each member before they became a member?

    _Note: With the assumption that on the date where a customer became a member, all of his/her order that day were before the time he/she became a member. Thus, instead of “s.order_date ≥ m.join_date”, it will be only “s.order_date > m.join_date”._

    SQL Statement (Subquery Approach):
    ```
    SELECT
    	last_purchases_before_join.customer_id,
    	COUNT(menu.product_id) AS total_items_before_joining,
    	SUM(menu.price) AS total_amount
    FROM menu
    INNER JOIN (
    SELECT
    	sales.customer_id,
    	sales.order_date,
    	sales.product_id,
    	members.join_date
    FROM sales
    INNER JOIN members ON sales.customer_id = members.customer_id
    WHERE sales.order_date < members.join_date
    ) AS last_purchases_before_join ON last_purchases_before_join.product_id = menu.product_id
    GROUP BY last_purchases_before_join.customer_id
    ORDER BY customer_id ASC
    ```
    SQL Statement (CTE Approach):
    ```
    WITH non_mem_purchase AS(
	SELECT
		s.customer_id
		,s.product_id
	FROM sales s
	INNER JOIN members mem ON s.customer_id = mem.customer_id
	WHERE s.order_date < mem.join_date
    )
    
    SELECT
    	nmp.customer_id
    	,COUNT(men.product_id) AS total_items_before_joining
    	,SUM(men.price) AS total_amount
    FROM non_mem_purchase nmp
    INNER JOIN menu men ON nmp.product_id = men.product_id
    GROUP BY nmp.customer_id
    ORDER BY nmp.customer_id
    ```
    
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/b866e6bf-b95c-45d3-b8af-ca28f32ea080)
    
    _Customer B purchase more products making him or her to spend more._


9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

    SQL Statement:
    ```
    --CTE to calculate the points for each purchase
    --10 points per dollar and 2x multiplier if the purchase is sushi
    WITH cte_points AS(
    	SELECT
    		s.customer_id
    		,s.order_date
    		,CASE
    			WHEN m.product_name = 'sushi' THEN m.price*10*2
    			ELSE price*10
    		END AS points
    	FROM sales s
    	INNER JOIN menu m ON s.product_id = m.product_id
    )
    
    --Access CTE then group by customer_id then calculate total points for each
    SELECT
    	cp.customer_id,
    	SUM(points) AS total_points
    FROM cte_points cp
    GROUP BY cp.customer_id
    ORDER BY cp.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/434ab353-b2ec-4c96-9f49-68d1dd49ae68)

    _Customer B had the highest points while customer C had the lowest._

    
10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
    Note: Purchases on the day customers became members are excluded assuming that multiplier bonus starts the day or date after a customer becomes a member.
    
    SQL Statement:
    ```
    --CTE to get the first week range for each customer w.r to their join_date
    --Assuming that the first week starts after the day or date the customer becomes a member
    WITH cte_dates AS(
    	SELECT
    		s.customer_id
    		,s.product_id
    		,s.order_date
    		,(mem.join_date + 1) start_first_week --start (remove "+1" if starting from the join_date itself - that is, day 1 of multiplier is the join_date)
    		,(mem.join_date + 7) AS end_first_week --end (change to "+6" if starting from the join_date itself - that is, day 1 of multiplier is the join_date)
    	FROM sales s
    	INNER JOIN members mem ON s.customer_id = mem.customer_id
    	WHERE EXTRACT(MONTH FROM s.order_date) = 1
    ),
    --CTE to calculate the points for each purchase
    --If order_date inside the first week range w.r to join_date, perform 2x multiplier.
    --Else, perform the classic pointing system (if order is sushi then 2x multiplier)
    --1USD = 10 points
    cte_points AS(
    	SELECT
    		cd.customer_id
    		,men.product_name
    		,cd.order_date
    		,cd.end_first_week
    		,cd.start_first_week
    		,men.price
    		--Nested CASE statement
    		--OUTER CASE: Determine if order_date inside the first week range
    		,CASE
    			WHEN (cd.order_date BETWEEN cd.start_first_week AND cd.end_first_week) THEN men.price*10*2
    			ELSE 
    				--INNER CASE (under OUTER CASE ELSE): Perform classic pointing system
    				CASE
    					WHEN men.product_name = 'sushi' THEN men.price*10*2
    					ELSE men.price*10
    				END
    		END AS points
    	FROM cte_dates AS cd
    	INNER JOIN menu men ON cd.product_id = men.product_id
    )
    
    --Access cte_points then group the results by customer_id then find the total_points for each
    SELECT
    	cp.customer_id
    	,SUM(points) AS total_points
    FROM cte_points AS cp`
    GROUP BY cp.customer_id
    ORDER BY cp.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/9521c291-a788-4d2f-8c0d-e3c5e710728e)

    _Customer A had higher points than customer B. It seems that the former bought more products (possibly more sushi) during his or her first week of being a member._

    ## Bonus Questions
    ### Join All The Things

    SQL Statement:
    ```
	SELECT
		s.customer_id
		,s.order_date
		,men.product_name
		,men.price
		,CASE
			WHEN s.order_date >= mem.join_date THEN 'Y'
			ELSE 'N'
		END AS member
	FROM sales s
	LEFT JOIN members mem ON s.customer_id = mem.customer_id
	INNER JOIN menu men ON s.product_id = men.product_id
	ORDER BY s.customer_id, s.order_date
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/95abbbba-2dc1-413f-a914-b014dbd723fd)



    
