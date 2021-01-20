## 놀러가는데 하루 1커밋은 어쩌지?

평일만이지만 하루 1커밋을 열심히 하고 있다. 덕분에 평일엔 왠지 모를 압박감을 느끼곤 한다. 지인을 만나거나 밖을 나가야 되는 일이 생기면 전날 미리 작성해놓고 당일 커밋하고 약속을 나가야 되기 때문이다.

하루, 이틀은 크게 문제가 없지만 '이틀을 넘어가게 되면 어쩌지?' 생각은 하면서 그냥 지내었는데, 작년 말에 평일 3일동안 집을 떠나게 되었다. 컴퓨터을 들고 가는 건 무겁고 오버인 듯 하여 개발자답게 자동화를 해야겠다 생각했다.


## 사전 깃 테스트

슬슬 생각한 방법은 간단한 자동화를 위해선 일단 깃에서 미리 커밋을 해둘 수 있는지 테스트해보았다.
테스트는 다음과 같았다.

1. 테스트 폴더를 생성하여 깃 리포지터리를 로컬에 생성
    ```
    $ mkdir test && cd test
    $ git init
    ```
2. 대충 파일 하나 만들고 첫 커밋
    ```
    $ touch file1.txt
    $ git add .
    $ git commit -m "first commit"
    ```
3. 서브 브랜치를 나눠서 변경점을 만들고 커밋
    ```
    $ git branch test
    $ git checkout test
    $ vi file1.txt

    (...변경점을 많들기 위한 파일 수정...)

    $ git add .
    $ git commit -m "second commit"
    ```
4. 다시 메인 브랜치로 돌아와서 서브 브랜치에서 만든 커밋을 체리픽
    ```
    $ git log --oneline         // 로그 찾기

        515a632 (HEAD -> test) second commit
        af850dc (master) first commit

    $ git checkout master
    $ git cherry-pick 515a632   // 두번째 커밋을 체리픽
    $ git log --oneline

        2a8277b (HEAD -> master) second commit
        af850dc first commit

    ```
5. 체리픽한 뒤 리셋해서 변경점을 언스테이지로 되돌리기
    ```
    $ git reset af850dc         // 두 번재 커밋을 리셋

        Unstaged changes after reset:
        M	file1.txt

    ```
6. 다시 전부 스테이지에 올린 뒤 새로운 커밋 생성
    ```
    $ git add .
    $ git commit -m "new commit"
    ```

## 생각보다 잘 되는 것 같은데?

생각보다는 체리픽과 리셋을 이용한 커밋은 나름 잘 되는 듯 했다. 그래서 이를 토대로 다음과 같은 시나리오를 생각했다.

1. 미리 만든 커밋 해쉬를 적어놓은 날짜별 파일을 읽고 당일 커밋을 만들어내는 스크립트를 작성
2. 서버에 스크립트 배치하고 crontab을 이용해 시간을 설정
3. 미리 브랜치를 하나 열어서 미리 커밋
4. 3번에서 작성한 커밋 해쉬를 서버 내에 파일로 작성해놓기
5. 설정해놓은 날 스크립트가 커밋! (완료)
