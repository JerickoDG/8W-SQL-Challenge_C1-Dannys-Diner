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
      SUM(price)
      FROM sales
    INNER JOIN menu on menu.product_id = sales.product_id
    GROUP BY sales.customer_id
    ORDER BY sales.customer_id ASC
    ```
    Output:
    
    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/af9bd36d-a8c5-4c61-b1fe-bcee4da4f906)

   
3. How many days has each customer visited the restaurant?

    SQL Statement:
    ```
    SELECT
    	s.customer_id,
    	COUNT(DISTINCT(s.order_date))
    FROM sales s
    GROUP BY s.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/ced6b871-1a31-4665-894d-185ef8e9dffd)


   
5. What was the first item from the menu purchased by each customer?

    SQL Statement:
    ```
    SELECT 
    	sales.customer_id,
    	first_purchase_tbl.first_purchase AS first_purchase_date,
    	menu.product_name
    FROM (
    	SELECT 
    		sales.customer_id,
    		MIN(sales.order_date) AS first_purchase
    	FROM sales
    	GROUP BY sales.customer_id
    ) AS first_purchase_tbl
    INNER JOIN sales ON (sales.customer_id = first_purchase_tbl.customer_id) AND (sales.order_date = first_purchase_tbl.first_purchase)
    INNER JOIN menu ON menu.product_id = sales.product_id
    ORDER BY sales.customer_id, first_purchase_tbl.first_purchase
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/3e4f7564-14e9-4f51-856f-e16c544b61ce)

    _Customer A and C had two transactions (i.e., bought products) on the same first day they had made purchase._

7. What is the most purchased item on the menu and how many times was it purchased by all customers?

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


8. Which item was the most popular for each customer?
9. Which item was purchased first by the customer after they became a member?

    SQL Statement:
    ```
    SELECT
        sales.customer_id,
        menu.product_name,
        first_purchase.first_purchase_date_after_join
    FROM sales
    INNER JOIN (
        SELECT
            sales.customer_id,
            MIN(order_date) AS first_purchase_date_after_join
        FROM sales
        INNER JOIN members ON members.customer_id = sales.customer_id
        WHERE order_date > members.join_date
        GROUP BY sales.customer_id
    ) AS first_purchase ON first_purchase.customer_id = sales.customer_id
    INNER JOIN menu ON menu.product_id = sales.product_id
    WHERE sales.order_date = first_purchase.first_purchase_date_after_join
    ORDER BY sales.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/89fcb1ca-cfa4-4f0b-8a89-94870e61ec7d)
    
11. Which item was purchased just before the customer became a member?
    
    SQL Statement:
    ```
    SELECT
        sales.customer_id,
        menu.product_name,
        last_purchase.last_purchase_date_before_join
    FROM sales
    INNER JOIN (
        SELECT
            sales.customer_id,
            MAX(order_date) AS last_purchase_date_before_join
        FROM sales
        INNER JOIN members ON members.customer_id = sales.customer_id
        WHERE order_date < members.join_date
        GROUP BY sales.customer_id
    ) AS last_purchase ON last_purchase.customer_id = sales.customer_id
    INNER JOIN menu ON menu.product_id = sales.product_id
    WHERE sales.order_date = last_purchase.last_purchase_date_before_join
    ORDER BY sales.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/caec8a48-83b9-44fc-bcbc-f9967e16259e)

    _It is likely that customer A purchased two products on the same date._
    
13. What is the total items and amount spent for each member before they became a member?

    SQL Statement:
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
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/b9894f41-4543-4312-905d-2dd359f88835)


15. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?

    SQL Statement:
    ```
    SELECT
    	transact_points.customer_id,
    	SUM(transact_points.points) AS total_points
    FROM (
    	SELECT
    		*,
    		CASE
    			WHEN menu.product_name = 'sushi' THEN menu.price * 2
    			ELSE menu.price
    		END AS points
    	FROM sales
    	INNER JOIN menu on menu.product_id = sales.product_id
    ) AS transact_points
    GROUP BY transact_points.customer_id
    ORDER BY transact_points.customer_id ASC
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/d9bc63a5-c3fa-4628-a5b0-1d3ace0ac52e)
    
17. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?

    SQL Statement:
    ```
    SELECT
    	transact_points.customer_id,
    	SUM(transact_points.points) AS total_points
    FROM (
    	SELECT
    		members_sales.customer_id,
    		members_sales.product_id,
    		members_sales.order_date,
    		menu.product_name,
    		menu.price,
    		members_sales.join_date,
    		CASE
    			WHEN (members_sales.order_date BETWEEN members_sales.join_date 
    				  AND (members_sales.join_date + 7)) AND (EXTRACT(MONTH FROM members_sales.order_date) = 1) 
    					THEN menu.price*2
    			ELSE menu.price
    		END AS points
    	FROM menu
    	INNER JOIN (
    		SELECT
    			sales.customer_id,
    			sales.product_id,
    			sales.order_date,
    			members.join_date
    		FROM sales
    		INNER JOIN members ON members.customer_id = sales.customer_id
    	) AS members_sales ON members_sales.product_id = menu.product_id
    ) AS transact_points
    WHERE EXTRACT(MONTH FROM transact_points.order_date) = 1
    GROUP BY transact_points.customer_id
    ORDER BY transact_points.customer_id
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/b7b4e7d1-e133-4cbb-8fd0-48278da0be06)

