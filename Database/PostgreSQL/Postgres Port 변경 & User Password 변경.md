***

### Postgres Port 변경

PostgreSQL의 포트 변경은 주로 `postgresql.conf` 파일을 수정함으로써 이루어짐.

1.  `postgresql.conf` 파일 찾기
   PostgreSQL 설치 디렉토리의 `data` 폴더 내에 이 파일이 위치해 있음. 
   리눅스에서는 `/var/lib/pgsql/data/` 또는 `/usr/local/pgsql/data/` 경로에 
   윈도우에서는 `C:\Program Files\PostgreSQL\<버전>\data\`에 위치해 있음.
    
2.  `postgresql.conf` 파일 열기
   파일을 열어 `port` 설정을 찾기. 

```bash
port = 5432
```

3.  PostgreSQL 서버 재시작
   변경한 포트 번호로 작동하도록 PostgreSQL 서버를 재시작. 
   리눅스에서는 `systemctl restart postgresql` 명령을 사용.
   윈도우에서는 서비스로 이동후  "PostgreSQL <버전> 서비스"를 재시작.

### Postgres User Password 변경 

SQL Shell 을 실행해서 사용자 계정에 접속하여 Password 재설정 쿼리 실행

```sql
ALTER USER <사용자명> WITH PASSWORD '<새비밀번호>';
```

 **반드시 쿼리 끝에 ';' 세미 콜론을 붙여서 ALTER_ROLE 결과를 확인해야 적용 된다.**
 