##### 접속하기
```{mysql} mysql -u root -p```

##### 버전확인
```{mysql}SELECT VERSION();```

```mysql
+-----------+
| VERSION() |
+-----------+
| 8.4.8     |
+-----------+
1 row in set (0.00 sec)
```
##### 데이터베이스 확인
```{mysql}SHOW DATABASES;```
```mysql
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)
```
##### 데이터베이스에 들어가기
```{mysql}use [db이름];```
##### 선택한 데이터베이스 속 테이블 목록 보기
```{mysql}show tables;```
##### 테이블 내용 전체 보기 
```{mysql}select * from [테이블이름];```
##### 테이블 내 특정 필드들만 보기 
```select [필드이름1],[필드이름2],... from [테이블이름];```
##### 데이터베이스 추가
```{mysql}create database [db이름];```
##### 데이터베이스에 테이블 추가
```{mysql}create table [테이블이름] ([칼럼이름1] [변수타입1], [필드이름2] [변수타입2],...);```
##### 테이블에 내용 추가
```{mysql}insert into [테이블이름] ([필드이름1], [필드이름2], ...) values ([필드1값], [필드2값], ...);```
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

##### 테이블 내용 수정
```mysql
UPDATE [테이블이름]
  SET [필드이름1] = [필드1값], ...
  WHERE [조건];
```

```mysql
CREAT TABLE [db이름].[테이블이름] 
  LIKE [db이름].[테이블이름];
```

```mysql
INSERT INTO [DB이름].[테이블이름] SELECT [필드이름] FROM [DB이름].[테이블이름];
```
##### mysql사용자 계정 생성
```mysql
CREATE USER '[사용자]'@'[IP주소]'
  IDENTIFIED BY '[패스워드]';
```
##### 계정에 권한 부여
```mysql
GRANT [ALL/SELECT/INSERT/UPDATE...] PRIVILEGES ON [DB].[테이블]
  TO '[사용자]'@'[IP주소]';
FLUSH PRIVILEGES;
```

```{mysql}SELECT user, host FROM mysql.user;```

```{mysql}SHOW GRANTS FOR '[사용자]'@'[IP주소]';```

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
 `{mysql}`
