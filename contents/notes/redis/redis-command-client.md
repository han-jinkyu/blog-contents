얼마 전, 게임 데이터를 만드는 팀에서 전투를 예상보다 쉽게 만들어서 문제가 생겼다.

결국 데이터 팀에서 전투 데이터를 상향하기로 했고, 나는 랭킹 데이터를 손봐야 했다.

그 때 사용하였던 복수 커맨드 실행 방법을 메모해 놓는다.

## 복수 커맨드 실행

```
$ redis-cli -h [host_name] -p [port] -n [db_index] < redis-commands.file
```

-   `-h`: 호스트를 적는다. 생략하면 로컬.
-   `-p`: 포트를 적는다. 생략하면 기본 포트인 **6379**.
-   `-n`: DB 번호를 적는다. 생략하면 1번.
-   `< redis-commands.file`
    -   Redis의 여러 커맨드를 담은 파일이다.
    -   개행문자를 기준으로 각 행에 커맨드를 적는다.
    -   이름 및 확장자는 맘대로 해도 된다.

## 참고

-   [Stack Overflow: Executing batches of commands using redis cli](https://stackoverflow.com/questions/10822877/executing-batches-of-commands-using-redis-cli/10824224)
-   [redis-cli, the Redis command line interface](https://redis.io/topics/rediscli)
