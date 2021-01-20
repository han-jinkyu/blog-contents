MySQL 클라이언트 상에서 쿼리를 통해 외부 파일로 출력하는 기능을 [이전 포스트](https://han-jinkyu.tistory.com/5)에 적었다.

하지만 일을 하다 보니 권한이 없어서 외부 파일로 출력하지 못 하는 경우가 존재했다.

이를 대체할 내용을 메모해 놓은 것이 있어 블로그에 적어 놓는다.

## 1\. 외부 쿼리 파일을 이용하여 외부로 파일 출력

이 방법은 **데이터를 조사하여 출력**할 때 사용하면 좋다. (SELECT)

```
$ mysql -N -u [user_name] -p -h [host_name] [db_name] < queries.sql | tr "\t" "," > outfile.csv
```

-   `--skip-column-names`, `-N`
    -   컬럼명을 출력하지 않는다.
-   `< queries.sql`
    -   `queries.sql`이란 파일을 표준 입력으로 MySQL 클라이언트에 입력한다.
-   `[commands] | tr "\t" ","`
    -   `tr`: 표준 입력을 받아 **문자를 변경(translate)**한다.
    -   `\t(탭 문자)`를 `,(콤마)`로 변경한다.
-   `[commands] > outfile.csv`
    -   표준 입력을 받아 파일로 저장한다.

## 2\. 외부 쿼리 파일을 이용하여 데이터 조작

이 방법은 **데이터 추가 혹은 변경에 사용**하면 좋다. (INSERT, UPDATE, DELETE)

```
$ mysql -v -v -v -u [user_name] -p -h [host_name] [db_name] < queries.sql | tee outfile.log
```

-   `-v -v -v`
    -   `--verbose`, `-v`: **상세(verbose) 내용을 출력**하는 옵션. **여러 번 적용 가능**.
    -   테이블 형식으로 자세하게 출력하게 하기 위해서 3번 옵션을 적용하였다.
-   `< queries.sql`
    -   `queries.sql`이란 **파일을 표준 입력으로 MySQL 클라이언트에 입력**한다.
-   `[commands] | tee [filename]`
    -   `tee`: 표준 입력을 읽어 **표준 출력과 파일을 동시에 쓴다.**
    -   즉 commands로부터 나오는 표준 출력을 입력으로 받아, 화면(표준 출력)과 파일에 동시에 출력한다.

### 2.1 상세 모드(verbose mode)

상세 모드는 여러 번 적용이 가능하여 옵션 개수에 따라 출력 내용이 달라진다고 한다.

따라서 아래와 같이 실험해 보았다.

**1\. 적용 X**

```
$ echo "SELECT NOW()" | mysql
NOW()
2020-06-26 12:32:15
```

**2\. 한 번 적용**

```
$ echo "SELECT NOW()" | mysql -v
--------------
SELECT NOW()
--------------

NOW()
2020-06-26 12:32:23
```

**3\. 두 번 적용**

```
$ echo "SELECT NOW()" | mysql -v -v
--------------
SELECT NOW()
--------------

NOW()
2020-06-26 12:32:32
1 row in set

Bye
```

**4\. 세 번 적용**

```
$ echo "SELECT NOW()" | mysql -v -v -v
--------------
SELECT NOW()
--------------

+---------------------+
| NOW()               |
+---------------------+
| 2020-06-26 12:32:36 |
+---------------------+
1 row in set (0.00 sec)

Bye
```

## 3\. 성공 여부 확인하기

1번에 소개한 커맨드를 실행해 놓으면 화면에 막 출력이 되는 모습을 볼 수 있다.

파일에 저장된 수많은 쿼리를 실행하다 보면 이것이 언제 종료될지 알 수 없는 경우가 많다.

그럴 경우 `tee`를 통해 보존하는 로그 파일을 통해 어디까지 실행했는지 확인해볼 수 있다.

```
$ grep "Query OK" outfile.log | wc -l
```

-   `grep [pattern] [filename]`
    -   패턴을 이용해 파일 내부를 검색하고 출력한다.
-   `wc -l`
    -   문자를 센다(word count)
    -   `-l`, `--lines`: 라인을 센다는 뜻.

## 4\. 참고

-   [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/mysql-command-options.html)
-   [Controlling mysql’s Verbosity Level](https://www.oreilly.com/library/view/mysql-cookbook/0596001452/ch01s29.html)
-   [tr manual](https://linux.die.net/man/1/tr)
-   [tee manual](https://linux.die.net/man/1/tee)
-   [grep manual](https://linux.die.net/man/1/grep)
-   [wc manual](https://linux.die.net/man/1/wc)
