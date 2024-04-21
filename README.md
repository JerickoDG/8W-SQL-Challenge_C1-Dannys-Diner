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
7. Which item was the most popular for each customer?
8. Which item was purchased first by the customer after they became a member?
9. Which item was purchased just before the customer became a member?
10. What is the total items and amount spent for each member before they became a member?
11. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
12. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
