***

```sql
COPY ( select * from ${테이블} 
	  where &{조건}= ${조건값} 
	  order by &{정렬값} 
	  ) TO ${postgres server 떨어지길 원하는 file path} 
with csv header DELIMITER ',';
```

Copy 명령어를 활용하여 DB 원하는 Table Data를 postgres 서버 path에 csv 확장자로 다운로드 받을 수 있다.