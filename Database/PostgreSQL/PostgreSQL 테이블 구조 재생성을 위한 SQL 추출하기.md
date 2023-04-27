
pg_dump 명령어와 다양한 옵션들을 활용하면 PostgreSQL 데이터베이스의 테이블과 관련된 DDL SQL 명령어를 추출할 수 있다 
이를 통해 테이블의 시퀀스, 제약 조건 및 인덱스를 포함하여 재생성할 수 있다.

테이블 재생성에 필요한 SQL을 추출하기 위해서는 주로 `-t` 또는 `--schema-only` 플래그를 사용함.
 `-t` 옵션은 특정 테이블을 지정하는데 사용되며, `--schema-only` 옵션은 데이터를 제외하고 스키마 정보만 추출하겠다는 것을 의미함.

다음과 같은 명령어를 실행하여 테이블 구조에 관한 SQL 명령어를 추출할 수 있음.

```bash
$ pg_dump -t 'users' --schema-only my_database > users.schema.sql
```

위 명령어를 실행하면 `users.schema.sql` 파일이 생성되며, 이 파일에는 테이블을 재생성하는데 필요한 SQL 명령어들이 포함됨.

이를 통해 다음 작업들을 수행할 수 있음.

1.  테이블 생성: 컬럼 정보, 기본값, NOT NULL 제약 조건 등 포함
2.  시퀀스 생성 및 설정: 시리얼 ID 컬럼에 대한 시퀀스 생성
3.  외래 키(Foreign Key) 제약 조건 추가
4.  인덱스 생성

테이블을 재생성하려면 추출한 SQL 파일을 `pg_restore` 명령어에 전달하거나, 수동으로 SQL 명령어를 실행할 수 있음.

pg_dump 명령어에는 다양한 옵션이 존재하므로, 필요에 따라 여러 가지 추가 옵션을 사용하여 원하는 형태의 SQL을 추출할 수 있음. 

### 배치 파일 을 통해서 일괄 처리하기


```shell
@echo off
set PG_PORT=${postgres_port}
set DUMP_DIR=%~dp0\schema_dump
set NEW_PG_DUMP="${pg_dump.exe 경로}"
set DATABASE_NAME=${postgres_databaseName}
set PGPASSWORD=${postgres_DB_PW}
set DUMP_SCHEMA=${추출할Schema list ','로 구분지어서 나열해서 입력}

::경로는 임의로 해당 배치파일이 있는 동일 경로에 schema_dump 라는 폴더를 만들어서 안에 저장
if not exist "%DUMP_DIR%" mkdir "%DUMP_DIR%"

set PG_DUMP_EXEC=%NEW_PG_DUMP% -Upostgres --port=%PG_PORT% --schema-only --dbname=%DATABASE_NAME%

for %%t in (%DUMP_SCHEMA%) do (
    %PG_DUMP_EXEC% -t "%%t" > "%DUMP_DIR%\%%t.schema.sql"
)

```

위와 같이 배치파일을 생성해서 해당 Database 정보만 입력하고 추출할 Schema 명만 입력해주면 일괄로 원하는 FilePath 에 SQL 파일로 저장 할 수 있다.