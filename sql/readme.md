		-- TOTAL ORDERS;
		SELECT 
		COUNT(order_id) AS total_no_of_orders
		FROM orders;
		-- UNIQUE CUSTOMERS PLACED ORDERS;
		SELECT
		COUNT(DISTINCT(customer_id)) AS Unique_customers
		FROM orders;
		-- NO OF ORDERS FOR EACH ORDER STATUS;
		SELECT
		order_status,
		COUNT(*) AS no_of_orders
		FROM orders
		GROUP BY order_status;  
		-- HIGHEST NO OF ORDERS IN CUSTOMER STATES;
		SELECT
		C.customer_state AS customer_state,
		COUNT(O.order_id) AS no_of_orders
		FROM customers AS C
		INNER JOIN ORDERS AS O ON C.customer_id = O.customer_id
		GROUP BY customer_state
		ORDER BY no_of_orders DESC LIMIT 1;
		-- MONTHLY TREND OF ORDERS;
		SELECT
		DATE_FORMAT(order_purchase_date,'%Y-%m') AS month,
		COUNT(order_id) AS no_of_orders
		FROM orders
		GROUP BY DATE_FORMAT(order_purchase_date,'%Y-%m')
		ORDER BY DATE_FORMAT(order_purchase_date,'%Y-%m');
		-- AVERAGE NO OF ORDERS PER MONTH;
		WITH DEMO AS (SELECT
		DATE_FORMAT(order_purchase_date,'%Y-%m') AS month_wise,
		COUNT(order_purchase_date) AS no_of_orders
		FROM orders
		GROUP BY month_wise)
		SELECT 
		AVG(no_of_orders) AS avg_no_of_orders
		FROM DEMO;
		-- Which year generated the highest number of orders;
		WITH DEMO AS (SELECT
		YEAR(order_purchase_date) AS year_wise,
		COUNT(order_id) AS no_of_orders
		FROM orders
		GROUP BY year_wise
		ORDER BY no_of_orders DESC LIMIT 1)
		SELECT
		year_wise
		FROM DEMO;
		-- What percentage of total orders were delivered;
		WITH DEMO AS (SELECT
		COUNT(order_id) AS delivered_no_of_orders
		FROM orders
		WHERE order_status = 'Delivered')
		SELECT 
		delivered_no_of_orders/(SELECT COUNT(order_id)FROM orders)*100 AS percentage_of_delivered_total_orders
		FROM DEMO;
		-- What is the total revenue generated from order items;
		SELECT
		SUM(price) AS total_revenue
		FROM order_items;
		-- What is the average order value (AOV);
		WITH DEMO AS (SELECT
		SUM(price) AS total_amount
		FROM order_items
		GROUP BY order_id)
		SELECT
		AVG(total_amount) AS aov
		FROM DEMO;
		-- Monthly revenue trend; 
		SELECT
		DATE_FORMAT(O.order_purchase_date,'%Y-%m') AS month_wise,
		SUM(OI.price) AS total_revenue
		FROM order_items AS OI
		LEFT JOIN orders AS O ON OI.order_id = O.order_id
		GROUP BY month_wise
		ORDER BY month_wise;
		-- Highest revenue generated month;
		SELECT
		DATE_FORMAT(O.order_purchase_date,'%Y-%m') AS month_wise,
		SUM(OI.price) AS total
		FROM order_items AS OI
		LEFT JOIN orders AS O ON OI.order_id = O.order_id
		GROUP BY month_wise
		ORDER BY total DESC LIMIT 1;
		-- Most commonly used payment type;
		SELECT
		payment_type,
		COUNT(payment_type) AS used_times
		FROM order_payments 
		GROUP BY payment_type
		ORDER BY used_times DESC LIMIT 1;
		-- The total payment value for each payment type ;
		SELECT
		payment_type,
		SUM(payment_value) AS total_amount
		FROM order_payments
		GROUP BY payment_type;
		-- The average number of installments for each payment type;
		SELECT
		payment_type,
		AVG(payment_installments) AS total_payment_installments
		FROM order_payments
		GROUP BY payment_type;
		-- Orders have the highest total payment value;
		SELECT
		order_id,
		SUM(payment_value) AS payment_value
		FROM order_payments
		GROUP BY order_id
		ORDER BY payment_value DESC LIMIT 1;
		-- product categories generate the highest revenue;
		SELECT
		P.product_category_name AS product_category,
		SUM(OI.PRICE) AS total_amount
		FROM order_items AS OI
		LEFT JOIN products AS P ON OI.product_id = P.product_id
		GROUP BY product_category
		ORDER BY total_amount DESC LIMIT 1;
		-- product categories have the highest number of items sold; 
		SELECT
		P.product_category_name AS product_category,
		COUNT(OI.order_id) AS no_of_items_sold
		FROM order_items AS OI
		LEFT JOIN products AS P ON OI.product_id = P.product_id
		GROUP BY product_category
		ORDER BY no_of_items_sold DESC LIMIT 1;
		-- top 10 products by total revenue; 
		SELECT
		P.product_id,
		SUM(OI.price) AS total_amount
		FROM order_items AS OI
		LEFT JOIN products AS P ON OI.product_id = P.product_id
		GROUP BY P.product_id
		ORDER BY total_amount DESC LIMIT 10;
		-- top 10 products by number of items sold;
		SELECT
		product_id,
		COUNT(product_id) AS no_of_items_sold
		FROM order_items
		GROUP BY product_id
		ORDER BY no_of_items_sold DESC LIMIT 10;
		-- product categories have the highest average product price;
		SELECT
		P.product_category_name AS product_category,
		AVG(OI.price) AS average_product_price
		FROM products AS P
		LEFT JOIN order_items AS OI ON P.product_id = OI.product_id
		GROUP BY P.product_category_name
		ORDER BY average_product_price DESC LIMIT 1;
		-- Top 10 customers by total spending;
		SELECT
		C.customer_id,
		SUM(OI.price) AS total_amount
		FROM customers AS C
		LEFT JOIN orders AS O ON C.customer_id = O.customer_id
		LEFT JOIN order_items AS OI ON O.order_id = OI.order_id
		GROUP BY C.customer_id
		ORDER BY total_amount DESC LIMIT 10;
		-- States with highest customer revenue;
		SELECT
		C.customer_state,
		SUM(OI.price) AS revenue
		FROM customers AS C
		LEFT JOIN orders AS O ON C.customer_id = O.customer_id
		LEFT JOIN order_items AS OI ON O.order_id = OI.order_id
		GROUP BY C.customer_state
		ORDER BY revenue DESC LIMIT 1;
		-- sellers generate the highest revenue;
		SELECT
		seller_id,
		SUM(price) AS revenue
		FROM order_items
		GROUP BY seller_id
		ORDER BY revenue DESC LIMIT 1;
		-- sellers have sold the highest number of items;
		SELECT
		seller_id,
		COUNT(order_item_id) AS no_of_items
		FROM order_items
		GROUP BY seller_id
		ORDER BY no_of_items DESC LIMIT 1;
		-- average order value (AOV) by customer state;
		WITH DEMO AS (SELECT
		C.customer_state AS customer_state,
		SUM(OI.price) AS price
		FROM customers AS C
		LEFT JOIN orders AS O ON C.customer_id = O.customer_id
		LEFT JOIN order_items AS OI ON O.order_id = OI.order_id
		GROUP BY customer_state,O.order_id)
		SELECT
		customer_state,
		AVG(price)
		FROM DEMO
		GROUP BY customer_state;
		-- Which seller states generated the highest revenue;
		SELECT
		S.seller_state AS seller_state,
		SUM(OI.price) AS revenue
		FROM sellers AS S
		LEFT JOIN order_items AS OI ON S.seller_id = OI.seller_id
		GROUP BY S.seller_state
		ORDER BY revenue DESC LIMIT 1;
		-- relationship between review score and delivery time;
		WITH DEMO AS (SELECT
		R.review_score AS review_score,
		DATEDIFF(O.order_delivered_date, O.order_purchase_date) AS delivery_days
		FROM orders AS O
		JOIN order_reviews AS R
		ON O.order_id = R.order_id)
		SELECT
		AVG(delivery_days) AS avg_delivery_days,
		review_score
		FROM DEMO
		GROUP BY review_score
		ORDER BY avg_delivery_days;
		-- late deliveries receive lower review scores;
		WITH DEMO AS(SELECT
		R.review_score,
		CASE
		WHEN O.order_delivered_date > O.order_estimated_delivery_date
		THEN 'Late'
		ELSE 'On-time'
		END AS delivery_status
		FROM orders AS O
		LEFT JOIN order_reviews AS R ON O.order_id = R.order_id)
		SELECT
		delivery_status,
		AVG(review_score) AS avg_review_score
		FROM DEMO
		GROUP BY delivery_status;







		 





		 
