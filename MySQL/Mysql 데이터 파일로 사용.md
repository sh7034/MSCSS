##### .sql 파일을 불러와 적재하기

###### [[mysql]] 안에서
`source [파일주소]`
foreign_key 문제가 있다면 `set foreign_key_checks = 0;` 사용
###### bash에서
`mysql -u root -p w3schools < 04_products_insert.sql
`
##### .tsv 파일을 불러와 적재하기
```
LOAD DATA LOCAL INFILE '/home/nova/Work/MySQL/DATA/orderdetails.tsv'
  INTO TABLE OrderDetails
  CHARACTER SET utf8mb4
  FIELDS TERMINATED BY '\t' #필드구분문자는 \t이다.
  LINES TERMINATED BY '\n' #라인구분문자는 \n이다.
  IGNORE 1 LINES
  (OrderDetailID, OrderID, ProductID, Quantity);
```