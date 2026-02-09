### 1. 주문이 한 번도 없는 고객을 조회하시오
```mysql
SELECT c.CustomerID, c.CustomerName
  FROM Customers c
  WHERE NOT EXISTS (
    SELECT 1
    FROM Orders o
    WHERE o.CustomerID = c.CustomerID
  );
```
### 2. 주문별 총 금액을 계산하시오
```MYSQL
SELECT od.OrderID, SUM(p.Price*od.Quantity) AS `Sum`
  FROM OrderDetails od
  JOIN Products p
    ON p.ProductID = od.ProductID
  GROUP BY od.OrderID;
```
### 3. 가장 비싼 상품을 판매한 주문을 조회하시오
```MYSQL
SELECT od.OrderID, p.Price
  FROM OrderDetails od
  JOIN Products p
    ON p.ProductID = od.ProductID
  WHERE Price >= ALL (
    SELECT Price FROM Products p);
```
### 4. 국가별 고객 수를 구하시오
```MYSQL
SELECT Country, COUNT(CustomerID) AS `Number`
  FROM Customers
  GROUP BY Country;
```
### 5. 가격대별 상품 개수를 CASE 문으로 분류하시오
```MYSQL
SELECT
  COUNT(CASE WHEN Price >= 100 THEN 1 END) AS high_count,
  COUNT(CASE WHEN Price >= 50 AND Price < 100 THEN 1 END) AS mid_count,
  COUNT(CASE WHEN Price < 50 THEN 1 END) AS low_count
FROM Products;
```
### 6. Customers와 Suppliers의 국가 목록을 중복 없이 출력하시오 (UNION 전용)
```MYSQL
SELECT CustomerID, CustomerName FROM Customers
  JOIN 
```
### 7. Customers와 Employees의 이름 목록을 하나로 출력하시오  (UNION 전용)
```MYSQL
SELECT CustomerID, CustomerName FROM Customers
  JOIN 
```
### 8. UNION과 UNION ALL 결과 행 개수를 비교하시오 (UNION 전용)
```MYSQL
SELECT CustomerID, CustomerName FROM Customers
  JOIN 
```
### 9. 독일(Germany)에 속한 모든 주체를 하나의 결과로 출력하시오 (UNION 전용)
```MYSQL
SELECT CustomerID, CustomerName FROM Customers
  JOIN 
```
### 10. 고객/공급업체를 Type 컬럼으로 구분하여 출력하시오 (UNION 전용)
```MYSQL
SELECT CustomerID, CustomerName FROM Customers
  JOIN 
```
