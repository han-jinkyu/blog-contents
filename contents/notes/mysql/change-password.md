MySQL 비밀번호 변경은 환경 구축할 때 뿐이라 그런지 매번 잊어버리게 된다.

비밀번호 변경법이 참 많은 것으로 알고 있지만 일하면서 얻은 경험을 메모한다.

추후 필요한 내용이 생기면 또 갱신하려 한다.

## 1\. 호스트(Host) 확인

-   바꾸기 전에 유저를 확인해본다.
-   mysql.user 테이블에 있는 Host는 해당 User가 `어느 호스트로부터 접속 가능한지`를 나타낸다.

```
SELECT Host, User FROM mysql.user WHERE User LIKE 'myuser%';
```

```
+-----------+--------+
| Host      | User   |
+-----------+--------+
| 192.168.% | myuser |
+-----------+--------+
1 row in set (0.00 sec)
```

-   결과를 보면 `myuser`라는 유저는 `192.168.*.*`로부터 접속 가능한 유저다. (`%`는 와일드카드를 의미)

## 2\. 패스워드 변경 방법

### 1) 5.7 이하 방식

```
SET PASSWORD FOR 'myuser'@'192.168.%' = PASSWORD('새로운 패스워드');
```

> SET PASSWORD ... = PASSWORD('auth\_string') syntax is deprecated as of MySQL 5.7.6 and will be removed in a future MySQL release.

-   [문서](https://dev.mysql.com/doc/refman/5.7/en/set-password.html)에 따르면 5.7.6보다 미래엔 삭제될 수도 있다 한다.

### 2) MySQL 권장 방식

```
ALTER USER myuser IDENTIFIED BY '새로운 패스워드'
```

> SET PASSWORD ... = 'auth\_string' syntax is not deprecated, but ALTER USER is the preferred statement for account alterations, including assigning passwords.

-   문서에 따르면 위 방식이 선호된다고 한다.

## 3\. 참조

-   [MySQL 5.7 Reference](https://dev.mysql.com/doc/refman/5.7/en/set-password.html)
-   [MySQL 8.0 Reference](https://dev.mysql.com/doc/refman/8.0/en/set-password.html)
