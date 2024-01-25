# java-DB-aggregate-function

1. Select the total number of Orders.
```sql
SELECT
  COUNT(*) AS total_number_orders
FROM
  orders;
```
3. Select number of orders that have ship region.
```sql
SELECT
  COUNT(*) AS number_orders_with_ship_region
FROM
  orders
WHERE
  ship_region IS NOT NULL;
```
4. Select the most expensive product.
```sql
SELECT
  product_name,
  unit_price
FROM
  products
ORDER BY
  unit_price DESC
LIMIT 1;

-- OR

SELECT
  product_name,
  unit_price
FROM
  products
WHERE
  unit_price = (SELECT MAX(unit_price) FROM products);
```
5. Select total price of order with id = 10258;
```sql
SELECT
  order_id,
  ROUND(SUM(unit_price * quantity * (1-discount)) :: NUMERIC,2 ) AS total_price
FROM
  order_details
WHERE
  order_id = 10258
GROUP BY
  order_id;
```
6. Select the least expensive product from order with id = 10263
```sql
SELECT 
  (SELECT product_name FROM products p WHERE o.product_id = p.product_id), 
  unit_price 
FROM 
  order_details o 
WHERE 
  order_id = 10263 
ORDER BY
  unit_price ASC LIMIT 1;

-- OR

SELECT 
  (SELECT product_name FROM products p WHERE p.product_id = o.product_id), 
  unit_price 
FROM 
  order_details o 
WHERE 
  o.order_id = 10263
AND
o.unit_price = (SELECT MIN(unit_price) FROM order_details WHERE order_id = o.order_id) 
```
7. Select all products that have price which is above the average price.
```sql
SELECT
  ROUND(AVG(unit_price):: NUMERIC,2)
FROM
  products

-- average price = 28.83

SELECT
  product_name,
  unit_price
FROM
  products
WHERE
  unit_price > (SELECT AVG(unit_price) FROM products)
```
8. Calculate number of products from category Seafood.
```sql
SELECT
  COUNT(*) AS total_Seafood_products
FROM
  products p
WHERE
  (SELECT category_name FROM categories c WHERE c.category_id = p.category_id) = 'Seafood';
```
9. Sumarize total value of products in orders made in 1996 (before discount).
```sql
SELECT 
  ROUND(SUM(unit_price * quantity) :: NUMERIC,2) AS total_value_1996 
FROM 
  order_details od
WHERE
  EXTRACT(YEAR FROM(SELECT order_date FROM orders o WHERE od.order_id = o.order_id)) = 1996;
```
