Let's now write the SQL queries based on these tables.

### Basic Queries:

1. **Retrieve the total number of orders placed:**
   ```sql
   SELECT COUNT(DISTINCT order_id) AS total_orders
   FROM order_details;
   ```

2. **Calculate the total revenue generated from pizza sales:**
   ```sql
   SELECT SUM(od.quantity * p.price) AS total_revenue
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id;
   ```

3. **Identify the highest-priced pizza:**
   ```sql
   SELECT pizza_id, MAX(price) AS highest_price
   FROM pizzas;
   ```

4. **Identify the most common pizza size ordered:**
   ```sql
   SELECT p.size, COUNT(*) AS order_count
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   GROUP BY p.size
   ORDER BY order_count DESC
   LIMIT 1;
   ```

5. **List the top 5 most ordered pizza types along with their quantities:**
   ```sql
   SELECT p.pizza_id, SUM(od.quantity) AS total_quantity
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   GROUP BY p.pizza_id
   ORDER BY total_quantity DESC
   LIMIT 5;
   ```

### Intermediate Queries:

1. **Join the necessary tables to find the total quantity of each pizza category ordered:**
   ```sql
   SELECT pt.category, SUM(od.quantity) AS total_quantity
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
   GROUP BY pt.category;
   ```

2. **Determine the distribution of orders by hour of the day:**
   ```sql
   SELECT HOUR(o.time) AS order_hour, COUNT(*) AS order_count
   FROM orders o
   GROUP BY order_hour
   ORDER BY order_hour;
   ```

3. **Join relevant tables to find the category-wise distribution of pizzas:**
   ```sql
   SELECT pt.category, COUNT(*) AS pizza_count
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
   GROUP BY pt.category;
   ```

4. **Group the orders by date and calculate the average number of pizzas ordered per day:**
   ```sql
   SELECT o.date, AVG(od.quantity) AS avg_pizzas_per_day
   FROM orders o
   JOIN order_details od ON o.order_id = od.order_id
   GROUP BY o.date;
   ```

5. **Determine the top 3 most ordered pizza types based on revenue:**
   ```sql
   SELECT p.pizza_id, SUM(od.quantity * p.price) AS revenue
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   GROUP BY p.pizza_id
   ORDER BY revenue DESC
   LIMIT 3;
   ```

### Advanced Queries:

1. **Calculate the percentage contribution of each pizza type to total revenue:**
   ```sql
   WITH total_revenue AS (
     SELECT SUM(od.quantity * p.price) AS total_revenue
     FROM order_details od
     JOIN pizzas p ON od.pizza_id = p.pizza_id
   )
   SELECT p.pizza_id, 
          SUM(od.quantity * p.price) / (SELECT total_revenue FROM total_revenue) * 100 AS revenue_percentage
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   GROUP BY p.pizza_id;
   ```

2. **Analyze the cumulative revenue generated over time:**
   ```sql
   SELECT o.date, SUM(od.quantity * p.price) AS daily_revenue, 
          SUM(SUM(od.quantity * p.price)) OVER (ORDER BY o.date) AS cumulative_revenue
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   JOIN orders o ON od.order_id = o.order_id
   GROUP BY o.date
   ORDER BY o.date;
   ```

3. **Determine the top 3 most ordered pizza types based on revenue for each pizza category:**
   ```sql
   SELECT pt.category, p.pizza_id, SUM(od.quantity * p.price) AS revenue
   FROM order_details od
   JOIN pizzas p ON od.pizza_id = p.pizza_id
   JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
   GROUP BY pt.category, p.pizza_id
   ORDER BY pt.category, revenue DESC
   LIMIT 3;
   ```

These SQL queries can be used to retrieve the desired insights from the pizza sales data.
