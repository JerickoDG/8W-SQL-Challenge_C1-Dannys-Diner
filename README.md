# 8W-SQL-Challenge_C1-Dannys-Diner

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
    	customer_id, 
    	COUNT(sales.order_date) UNIQUE
    FROM sales
    INNER JOIN menu on menu.product_id = sales.product_id
    GROUP BY sales.customer_id
    ORDER BY sales.customer_id ASC
    ```
    Output:

    ![image](https://github.com/JerickoDG/8W-SQL-Challenge_C1-Dannys-Diner/assets/60811658/5dd187d0-3bf5-4305-8ff5-85c56b9e5b06)

   
5. What was the first item from the menu purchased by each customer?
6. What is the most purchased item on the menu and how many times was it purchased by all customers?

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
14. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
15. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
