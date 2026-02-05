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
##### 전체 사용자 및 호스트 조회 쿼리
```{mysql}SELECT user, host FROM mysql.user;```
##### 사용자의 모든 권한 보기
```{mysql}SHOW GRANTS FOR '[사용자]'@'[IP주소]';```


 `{mysql}`
