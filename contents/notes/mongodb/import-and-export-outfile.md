MongoDB를 통해 조사를 하거나 데이터를 다량으로 넣는 경우가 있었다.

메모해놓았던 기록이 있어서 블로그에 남겨 본다.

## 1\. 익스포트 Export

-   데이터베이스로부터 데이터를 `추출`한다.
-   `mongoexport`라고 하는 커맨드라인 클라이언트를 사용한다.
-   여러 가지 출력 형식을 정할 수 있다.
-   JSON 쿼리를 부여해 조건을 지정할 수 있다.

### 1) CSV 형식

```
$ mongoexport --db db_name
    --collection collection_name
    --fields field1,field2,field3 
    -q '{ field1: 1, field2: { $in: [ 2, 3, 4 ] } }' 
    --type=csv 
    --out outfile.csv
```

-   `--db`: 데이터를 출력할 `DB를 선택`한다.
-   `--collection`: 데이터를 출력할 `Collection을 선택`한다.
-   `--fields`: 출력할 필드를 지정한다.
-   `-q`, `--query`: JSON 형태로 구성된 쿼리를 넣어 `조건을 부여`한다.
-   `--type`: 데이터 `출력 형태`를 지정한다. csv, json 등이 선택 가능하다.
-   `--out`: 외부 파일의 이름을 지정한다.

### 2) JSON 형식

```
$ mongoexport --db db_name 
    --collection collection_name 
    --fields field1,field2 
    -q '{ docList: { $elemMatch: { type: 5 } } }' 
    --jsonArray --pretty
    --out outfile.json
```

-   `--jsonArray`: 데이터를 하나의 JSON 배열로 출력한다. 즉, `[ {}, {}, ... ]` 형태로 저장된다.
-   `--pretty`: 출력하는 JSON에 줄바꿈과 들여쓰기를 넣는다.

## 2\. 임포트 Import

-   데이터베이스로 외부 파일을 입력한다.
-   외부 파일은 JSON, CSV와 같은 파일이다.

### 1) CSV 형식

```
$ mongoimport --db db_name 
    --collection collection_name 
    --fields field1,field2
    --type=csv
    --file outfile.csv
```

-   `--fields`, `-f`: 헤더가 지정되어 있지 않은 CSV의 경우 필드를 넣어준다.
-   `--type`: CSV를 지정한다.

### 2) JSON 형식

```
$ mongoimport --db db_name 
    --collection collection_name 
    --type=json 
    --file outfile.json
```

-   `--type`: JSON을 지정한다.

## 3\. 참조

-   [mongodb:mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/)
-   [mongodb:mongoimport](https://docs.mongodb.com/manual/reference/program/mongoimport/)