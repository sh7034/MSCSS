##### tar
여러 파일을 한 데 묶는 아카이빙(archiving) 도구
```bash

-c # 파일 create
-x # 파일 extract
-t # 파일 list
-r # 파일 append (단순 추가)
-u # 파일 update (변경내용 검색하여 추가)
-f # 대상 파일 지정
# -f는 만드시 파일 이름 바로 앞에 와야 함
tar -cvf backup.tar

-z # gzip 압축
-j # bzip2 압축
-J # xz 압축
# 압축 옵션들은 c,x,t와는 함께 쓸 수 있지만 r,u와는 함께 쓸 수 없다.


#/tmp 디렉토리에 압축해제
tar xvf ab.tar -C /tmp
```
##### rsync
증분백업