
### 데이터 베이스 테이블을 JSON 값으로 반환

예를 들어, `customers`이라는 테이블을 JSON으로 추출하려면 
다음과 같은 쿼리를 사용할 수 있음.

```sql
SELECT json_agg(row_to_json(t))
FROM (
  SELECT * FROM customers
) t;
```

위 쿼리는 PostgreSQL에서 JSON 형식으로 데이터를 변환하는 데 사용되는 내장 함수 `row_to_json()`와 `json_agg()`를 사용함.

`row_to_json()` 함수는 테이블의 각 행을 JSON 객체로 변환함.

이 함수는 각 행의 컬럼 이름을 JSON 키로 사용하고, 해당 컬럼의 값을 JSON 값으로 사용함.

이렇게 생성된 JSON 객체는 원본 행이 표현된 값임.

`json_agg()` 함수는 입력되는 JSON 객체들을 하나의 JSON 배열로 집계함. 

이 함수는 모든 행에 대해 `row_to_json()` 함수가 반환한 JSON 객체를 JSON 배열로 결합함.

FROM 절의 서브쿼리는 원하는 테이블에서 모든 행을 선택. 

이 경우에는 `customers` 테이블. 

그리고 메인 쿼리의 `row_to_json()` 함수가 이 서브쿼리 결과를 사용하여 각 행을 JSON 객체로 변환한 다음, `json_agg()` 함수로 결과 JSON 객체들을 하나의 JSON 배열로 집계함.

### 임시테이블을 생성하는가?

위 쿼리는 메모리에서 작업을 수행. 

결과 JSON 배열은 클라이언트에 반환되고, 데이터베이스 내부에는 저장되지 않음.

따라서 이 쿼리는 임시 테이블을 생성하지 않음.

다른 작업을 수행하더라도 결과 데이터는 이미 반환되었기 때문에 영향을 받지 않음.

### Json 파일로 저장하고 싶은경우

`psql` 과 쉘에서 사용하는 `>` 리다이렉션을 활용하여 저장 할 수 있다.

매번 치기 귀찮기 때문에 윈도우 배치 쉘 스크립트로 작성해서 cmd 실행하기만 하는 방식으로 추출 하겠다.

```bash
@echo off

::postgres setting
set OUTPUTDIR=%~dp0
set PGUSER=${youruser}
set PGPASSWORD=${yourpassword}
set PGDATABASE=${yourdatabase}
set PGHOST=${yourhost}
set PGPORT=${yourport}

set NEW_PSQL="C:\Program Files\PostgreSQL\14\bin\psql.exe" //ex 사용자 postgresql psql 경로

set PSQL=%NEW_PSQL% -U %PGUSER% -d %PGDATABASE% -h %PGHOST% -p %PGPORT% --tuples-only -A -c 

:: Query to export data as JSON
set QUERY="SELECT json_agg(row_to_json(t)) FROM (SELECT * FROM metric_classmetric) t;"

:: Ask for the output directory
set OUTPUT_PATH="%OUTPUTDIR%"

:: Export data as JSON to the specified directory
%PSQL% %QUERY% > "%OUTPUT_PATH%\test_output.json"

echo Data exported successfully to %OUTPUT_PATH%\output.json

```

여기서 포인트는

`--tuples-only` 옵션과 `-A` 옵션이다.

`--tuples-only` 또는 `-t`

이 옵션은 결과 출력 시 컬럼 이름과 테이블의 구분선을 숨김.

해당 옵션을 적용하지 않으면 json 데이터 앞뒤로 불필요한 선, 공백 등의 데이터가 같이 출력됨.

옵션을 적용하면 데이터 튜플(행)만 출력됨. 

이 옵션을 사용하면 결과가 더 깔끔하게 보이며, 특히 파일로 내보낼 때 유용.

`--no-align` 또는 `-A`

이 옵션은 출력 결과의 정렬을 해제함. 

기본적으로 `psql`은 출력 결과를 정렬하여 표 형식으로 보여주지만, 이 옵션을 사용하면 각 필드가 공백 없이 서로 붙어서 출력됨.

