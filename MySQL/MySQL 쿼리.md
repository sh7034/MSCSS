##### 쿼리: 테이블 내용 보기
```mysql
SELECT [필드이름] AS [field_alias]
  FROM [테이블이름] [table_alias]
  WHERE [조건]
  ORDER BY [필드] (DESC)
  LIMIT [보여줄 항목 개수] OFFSET [건너뛸 항목 개수];
```
###### 필드이름에 사용 가능한 함수들
```mysql
MIN()
MAX()
COUNT()
AVG()
SUM()
```
###### 조건
```mysql
=
>
<
>=
<=
<>
BETWEEN [] AND []
IN ([],[])
LIKE '[]'
IS NULL

NOT
AND
OR
NOT
```
```LIKE```에서 ```%```는 0개 이상의 문자, ```_```는 1개의 문자를 의미.

##### MySQL Joins
서로 다른 여러 테이블에서 내용을 가져와 합쳐서 표시한다.

```mysql
SELECT Orders.OrderID, Customers.CustomerName, Orders.OrderDate  
  FROM Orders  
  INNER JOIN Customers
    ON Orders.CustomerID=Customers.CustomerID;
```

```{mysql}FROM``` 테이블과 ```{mysql}JOIN``` 테이블을 합친다. 합치는 규칙은 ```{mysql}ON``` 뒤에 오는 조건을 따른다.

`{mysql}LEFT JOIN`: `{mysql}ON`조건과 일치하지 않아도 `{mysql}FROM` 테이블의 데이터는 모두 표시된다. 
`{mysql}RIGHT JOIN`: `{mysql}ON`조건과 일치하지 않아도 `{mysql}JOIN` 테이블의 데이터는 모두 표시된다. 
`{mysql}CROSS JOIN`: `{mysql}ON`조건과 일치하지 않아도 양쪽 테이블의 데이터가 모두 표시된다. `{mysql}ON`조건과 일치하면 중복 없이 합쳐서 표시된다.