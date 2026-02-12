##### sql파일로 백업
```mysql
mysqldump -u root -p student > student_full.sql

mysqldump -u root -p --database student > student_full-db.sql

mysqldump -u root -p student sungjuk > student_sungjuk.sql

mysqldump -h 192.168.200.22 -u webapp -p student > student_remote.sql
```

##### .sql 파일을 불러와 적재하기

###### [[mysql]] 안에서
`{mysql}SOURCE [파일주소]`
foreign_key 문제가 있다면 `{mysql}SET FOREIGN_KEY_CHECKS = 0;` 사용
###### bash에서
`mysql -u root -p w3schools < 04_products_insert.sql
`
##### .tsv 파일을 불러와 적재하기
```mysql
LOAD DATA LOCAL INFILE '/home/nova/Work/MySQL/DATA/orderdetails.tsv'
  INTO TABLE OrderDetails
  CHARACTER SET utf8mb4
  FIELDS TERMINATED BY '\t' #필드구분문자는 \t이다.
  LINES TERMINATED BY '\n' #라인구분문자는 \n이다.
  IGNORE 1 LINES
  (OrderDetailID, OrderID, ProductID, Quantity);
```