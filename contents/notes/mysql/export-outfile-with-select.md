MySQL 클라이언트 내부에서 쿼리를 실행하여 외부 파일로 출력하고 싶은 경우가 종종 있다.

그럴 때 사용할 수 있는 방법을 메모해놓은 게 있어서 블로그에 남겨 놓는다.

## SELECT하여 외부로 파일 보내기

```
SELECT * FROM table_name WHERE col_name = 'hello' 
INTO OUTFILE '/where/to/save/file.csv' 
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
```

-   `INTO OUTFILE 'path'`(2번째 행)
    -   **어디에 저장**할지 표현한다.
    -   실행 계정이 **쓰기 권한**이 없으면 안 되는 경우가 있다! (안 되는 경우에 사용한 건 다른 포스트에...)
-   `FIELDS TERMINATED BY ','`(3번째 행)
    -   **각 필드(컬럼)이 어떻게 마무리(TERMINATED)되는가**를 나타낸다.
    -   즉 필드 뒤에 어떤 문자를 출력할 것인가를 나타낸다.
    -   지금 내용은 **',(콤마)'로 각 필드를 구분** 짓는다는 내용이다.
-   `LINES TERMINATED BY '\n'`(4번째 행)
    -   **각 라인이 어떻게 마무리(TERMINATED)되는가**를 나타낸다.
    -   즉 라인 뒤에 어떤 문자를 출력할 것인가를 나타낸다.
    -   지금 내용은 **'\\n(개행문자)'로 각 라인을 구분** 짓는다는 내용이다.

위에 적은 쿼리는 **CSV 형식으로 출력**된다는 의미가 된다.

## AWS S3로 파일 보내기 (Aurora MySQL)

**Aurora MySQL은 S3로 파일을 내보낼 수 있는 기능**을 가지고 있다.

권한 문제는 아직 직접 설정해보지 않아서 적지 않고 제일 하단에 참고 링크로 대체한다.

```
SELECT * FROM table_name WHERE col_name = 'hello' 
INTO OUTFILE S3 '/where/to/save/file.csv' 
FIELDS TERMINATED BY ',' 
LINES TERMINATED BY '\n'
```

-   `INTO OUTFILE S3 'path'`(2번째 행)
    -   `S3`라는 키워드가 중요하다.
    -   로컬에 저장하고 다시 S3에 복사하지 않고도 바로 파일을 S3에 저장할 수 있다.

## 참고

상세 내용은 아래를 참고하세요.

-   [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/select-into.html)
-   [MySQL 8.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/select-into.html)
-   [Amazon Aurora MySQL DB 클러스터에서 Amazon S3 버킷의 텍스트 파일로 데이터 저장](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Integrating.SaveIntoS3.html)
