## 스크립트를 작성해볼까?

[이전 글](https://han-jinkyu.tistory.com/21)에서 깃의 체리픽과 리셋을 이용해 미리 커밋해놓은 내용을 당일 커밋으로 변경하는 내용이 가능한지 테스트했다. 테스트를 토대로 쉘스크립트를 작성해보았다.

### 1\. 파일 읽기

먼저 외부에 저장해놓은 파일을 읽기로 한다. 파일은 날짜(예. 20200101)로 저장해놓은 텍스트 파일이다. 안에는 **미리 만들어놓은 커밋의 해쉬**와 **다시 커밋을 만들 때 사용할 커밋 메시지**가 여러 줄 존재한다.

```bash
#! /bin/bash

# 먼저 상수를 지정한다
PROJECT_NAME="$1"                       # 프로젝트 이름은 외부에서
GIT_FOLDER="/(...)/$PROJECT_NAME"       # 깃 폴더 지정
SCHEDULE_FOLDER="/(...)/$PROJECT_NAME"  # 스케쥴 폴더 지정

### ...프로젝트 이름, 폴더 존재 여부 확인하는 스크립트 생략... ###

# 커밋 관련 파일을 읽는다
filename=$"$(date +'%Y%m%d').txt"               # 날짜 => '20200101.txt'
schedule_filepath="$SCHEDULE_FOLDER/$filename"  # 스케쥴 폴더에서 파일 지정

### ...파일 존재 여부 체크 생략... ###

# 파일을 읽어서 변수에 저장한다
mapfile -t commit_lines < $schedule_filepath
```

### 2\. 깃 레포지터리 갱신

파일을 읽었으면 레포지터리를 갱신하고 최신 커밋을 하나 읽어서 해쉬만 골라둔다. 이유는 다음에 나올 반복문에서 이전 커밋으로 리셋을 하기 위해서다.

```bash
# 깃 레포지터리를 갱신한다
git fetch
git checkout master
git pull

# 최신 로그메시지 한 줄을 읽은 뒤 커밋 해쉬를 변수에 저장한다
log_message=$(git log --oneline -1)
IFS=' '
read -ra split_log_message <<< "$log_message"
prev_commit="${split_log_message[0]}"
```

### 3\. 새로운 커밋 만들기

이제 파일에 저장한 커밋 해쉬와 메시지를 한 줄씩 읽어가며 반복하여 체리픽-리셋을 진행한다.

```bash
# 체리픽-리셋을 반복한다
for commit_line in "${commit_lines[@]}"; do
  # 파일로부터 읽은 커밋 내용을 콤마로 쪼갠다
  IFS=','
  read -ra split_commit_line <<< "$commit_line"

  # 첫 번째는 커밋 해쉬, 두 번째는 메시지로 만들어서 저장했다
  # ex) a1b2c3e,새로운 커밋 메시지입니다
  commit="${split_commit_line[0]}"
  message="${split_commit_line[1]}"

  # 커밋 해쉬를 체리피킹한다
  git cherry-pick $commit

  # 그리고 이전 커밋으로 리셋한다
  git reset $prev_commit

  # 리셋하여 나온 체리피킹한 파일을 추가하고 커밋한다
  git add -A
  git commit -m "$message"

  # 그리고 새로 만들어진 커밋의 해쉬를 취한다
  log_message=$(git log --oneline -1)
  IFS=' '
  read -ra split_log_message <<< "$log_message"
  prev_commit="${split_log_message[0]}"
done
```

### 4\. 정리하기

이제 열심히 만든 커밋을 리모트로 푸쉬하고 스케쥴 파일을 정리한다.

```bash
# 열심히 만든 커밋을 리모트로 푸쉬
git push origin master

# 그리고 필요 없어진 스케쥴 파일 옮기기
past_schedule_folder="$SCHEDULE_FOLDER/past"

### ...폴더 체크 생략... ###

# 파일 옮기기
mv $schedule_filepath "$past_schedule_folder/"
```

## 스크립트는 완성!

지금 만든 스크립트는 최종 완성이었고 사실 실제 3일 떠나는 동안 이틀 실패했다. 실패할 때를 대비해 스마트폰으로 서버 접속해서 고치느라 시간을 보내곤 했다. 다행히 그렇게 고친 끝에 마지막 날에는 성공했고 요새도 가끔 사용할 정도로 맘에 들게 동작한다.

이제 이 스크립트를 서버에 배치하고 동작시키면 된다!
