
### 1. sungjuk 테이블의 모든 학생 성적을 조회하시오.
```MYSQL
SELECT * FROM sungjuk;
```
### 2. 국어 점수가 80점 이상인 학생의 번호, 이름, 국어를 조회하시오.
```MYSQL
SELECT
  `번호`,
  `이름`,
  `국어`
  FROM sungjuk
  WHERE `국어` >= 80;
```
### 3. 영어 점수가 60~90점 사이인 학생을 조회하시오.
```MYSQL
SELECT
  `이름`
  FROM sungjuk
  WHERE `영어` BETWEEN 60 AND 90;
```
### 4. 수학 점수가 NULL 인 학생을 조회하시오.

```MYSQL
SELECT
  `이름`
  FROM sungjuk
  WHERE `수학` IS NULL;
```
### 5. 총점이 250점 이상인 학생의 번호, 이름, 총점을 조회하시오.
```MYSQL
SELECT
  `이름`,
  `번호`,
  (`국어` + `영어` + `수학`) AS `총점`
  FROM sungjuk
  WHERE (`국어` + `영어` + `수학`) >= 250;
```

### 6. 평균 점수가 높은 순서대로 학생을 조회하시오.
```MYSQL
SELECT
  `이름`,
  (`국어` + `영어` + `수학`)/3 AS `평균`
  FROM sungjuk
  ORDER BY `평균` DESC, `이름`;
```
### 7. 평균 상위 3명의 학생을 조회하시오.
```MYSQL
SELECT
  `이름`,
  (`국어` + `영어` + `수학`)/3 AS `평균`
  FROM sungjuk
  ORDER BY `평균` DESC, `이름`
  LIMIT 3;
```
### 8. 각 학생의 평균 점수를 소수 둘째 자리까지 표시하시오.
```MYSQL
SELECT
  `이름`,
  ROUND((`국어` + `영어` + `수학`)/3, 2) AS `평균`
  FROM sungjuk
  ORDER BY `평균` DESC, `이름`;
```
### 9. 이름이 ‘김’으로 시작하는 학생을 조회하시오.
```MYSQL
SELECT
  `이름`
  FROM sungjuk
  WHERE `이름` LIKE '김%';
```
### 10. 학생 수(행 개수)를 조회하시오.
```MYSQL
SELECT
  COUNT(`번호`)
  FROM sungjuk;
```

### 11. sungjuk과 jusorok을 JOIN 하여 `번호, 이름, 국어, 영어, 수학, 주소`를 조회하시오.
```MYSQL
SELECT
  s.`번호`,
  s.`이름`,
  s.`국어`,
  s.`영어`,
  s.`수학`,
  j.`주소`
  FROM sungjuk s
  JOIN jusorok j
    ON s.`번호` = j.`번호`;
```
### 12. 주소록에는 있으나 성적이 없는 학생을 조회하시오.
```MYSQL
SELECT
  j.`이름`
  FROM jusorok j
  LEFT JOIN sungjuk s
    ON s.`번호` = j.`번호`
  WHERE s.`국어` IS NULL OR s.`수학` IS NULL OR s.`영어` IS NULL;
```
### 13. 성적은 있으나 주소가 없는 학생을 조회하시오.
```MYSQL
SELECT
  j.`이름`
  FROM jusorok j
  RIGHT JOIN sungjuk s
    ON s.`번호` = j.`번호`
  WHERE j.`주소` IS NULL;
```
### 14. 학생별 총점과 평균을 계산하여 주소와 함께 조회하시오.
```MYSQL
SELECT
  j.`번호`,
  j.`이름`,
  s.`국어` + s.`영어` + s.`수학` AS `총점`,
  ROUND((s.`국어` + s.`영어` + s.`수학`)/3, 2) AS `평균`,
  j.`주소`
  FROM sungjuk s
  JOIN jusorok j
    ON s.`번호` = j.`번호`;
```
### 15. 번호 = 5 학생을 sungjuk에서 삭제할 때 `jusorok` 데이터도 함께 삭제되도록 FK를 생성하시오.
```MYSQL
ALTER TABLE jusorok
  ADD CONSTRAINT fk_jusorok_sungjuk
  FOREIGN KEY (`번호`)
  REFERENCES sungjuk(`번호`)
  ON DELETE CASCADE;
```

### 16. 성적 + 주소 + 총점 + 평균을 포함하는 VIEW `v_student_profile`을 생성하시오.
```MYSQL
CREATE VIEW v_student_profile AS
  SELECT
    s.`번호`,
    s.`이름`,
    COALESCE(s.`국어`,0) AS `국어`,
    COALESCE(s.`영어`,0) AS `영어`,
    COALESCE(s.`수학`,0) AS `수학`,
    (COALESCE(s.`국어`,0) + COALESCE(s.`영어`,0) + COALESCE(s.`수학`,0)) AS `총점`,
    ROUND((COALESCE(s.`국어`,0) + COALESCE(s.`영어`,0) + COALESCE(s.`수학`,0)) / 3, 2) AS `평균`,
    j.`주소`
FROM sungjuk s
JOIN jusorok j
  ON s.`번호` = j.`번호`;
```
### 17. `v_student_profile`에서 번호 = 1 학생의 정보를 조회하시오.
```MYSQL
SELECT *
  FROM v_student_profile
  WHERE `번호` = 3;
```
### 18. 다음 쿼리의 실행 계획을 확인하시오.
```MYSQL
EXPLAIN SELECT * FROM sungjuk WHERE 번호 = 3;
```
```{mysql}type=const```
- DB가 데이터를 찾는 방식이 상수(const)이다. WHERE문으로 프라이머리 키를 사용하여 검색하였으므로 조건을 만족하는 행이 단 1건뿐임을 DB가 알고 있음.
```{mysql}key=PRIMARY```
- 데이터를 찾기 위해 실제로 사용된 인덱스가 프라이머리 키이다. 별도의 보조 인덱스를 통하지 않고 실제 데이터에 가장 빠르게 접근하여 조회했다.
```{mysql}rows=1```
- 예상 검사 대상 행의 수가 1행이다. 이 쿼리를 처리하기 위해 조회한 행의 수가 1개뿐이다.
### 19. PRIMARY KEY가 없는 테이블과 있는 테이블의`EXPLAIN` 결과 차이를 설명하시오.
```MYSQL
EXPLAIN SELECT * FROM sungjuk WHERE `이름` = '장하늘';
```
```{mysql}type=ALL```
- DB가 데이터를 찾는 방식이 모두(ALL)이다. WHERE문으로 프라이머리 키가 아닌 다른 인덱스를 사용하여 검색하였으므로 조건을 만족하는 행을 찾기 위해 전체 데이터를 스캔한다.
```{mysql}key=NULL```
- 데이터를 찾기 위해 사용할 수 있는 인덱스가 없다.
```{mysql}rows=9```
- 예상 검사 대상 행의 수가 9행이다. 이 쿼리를 처리하기 위해 테이블의 9개 행을 모두 조회했다.
### 20. JOIN이 포함된 VIEW에서 UPDATE가 실패하는 이유를 설명하시오.
- VIEW는 특정 조건을 만족하면 읽기 전용이 아니게 되어 UPDATE가 가능하다. 그런데 VIEW는 DB상에 실제로 존재하는 테이블이 아닌, 가상 테이블처럼 취급된다. 조건을 만족하여서 자료를 UPDATE할 수 있지만 VIEW의 자료가 아닌 VIEW가 지정하고 있는 실 테이블의 자료를 업데이트한다.
- MySQL의 JOIN문은 Nested Loop Join이란 알고리즘을 통해 일치하는 행 한 쌍을 찾을 때마다 바로바로 클라이언트에게 스트리밍하는 방식을 사용한다.
- VIEW에 JOIN문이 있으면 UPDATE를 사용하고 싶어도 지정하고 있는 실 테이블이 없으므로 테이블의 데이터를 수정 불가능하다.
```mysql

```