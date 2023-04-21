***

## Git Hooks

Git도 다른 버전 관리 시스템처럼 어떤 이벤트가 생겼을 때 자동으로 특정 스크립트를 실행하도록 할 수 있다. 

이 훅은 클라이언트 훅과 서버 훅으로 나눌 수 있다. 

클라이언트 훅은 커밋이나 Merge 할 때 실행되고 서버 훅은 Push 할 때 서버에서 실행된다. 

### hook install??

Git hook은 별도로 설치할 필요가 없다. 

Git 저장소를 초기화할 때(`git init`을 실행할 때) 자동으로 생성되는 `.git/hooks` 폴더에 기본 훅 샘플들이 들어있다.

이 샘플들은 확장자가 `.sample`로 되어 있으며, 사용하려면 확장자를 제거하고 실행 가능한 상태로 변경해야 한다.

즉 `.sample` 확장자를 제거해주면 된다.

![git_hooks](https://user-images.githubusercontent.com/61622657/230907155-49dc6b71-52fd-4e23-8630-925725fb7092.png)


### Repository Clone시 hook 복사?

Repository를 Clone 해도 클라이언트 훅은 복사되지 않는다.

## 클라이언트 훅

클라이언트 훅은 매우 다양하다. \
지금은 commit workflow 에 관련된 hook을 먼저 알아 보자.

## Commit Workflow Hooks

커밋과 관련된 훅은 총 네 가지입니다.

### 1. `pre-commit` Hook

커밋하기 전에 가장 먼저 호출되는 훅으로, 커밋 메시지를 작성하기 전에 호출됨. 

이 훅에서 커밋하는 스냅샷을 검사함. 

누락된 항목이 없는지, 테스트를 확실히 수행했는지 등을 검사함. 

커밋할 때 반드시 확인해야 하는 항목이 있으면 이 훅으로 확인함. 

이 훅의 종료 코드가 0이 아니면 커밋이 취소됨.

**예제**

```bash
#!/bin/sh

# Java 파일을 찾습니다.
java_files=$(git diff --cached --name-only --diff-filter=ACMR | grep '\.java$')

# Java 파일에 System.out.println()이 있는지 검사합니다.
if [ -n "$java_files" ]; then
  for file in $java_files; do
    if grep -q 'System\.out\.println(' "$file"; then
      echo "Error: System.out.println() found in $file. Please remove it before committing."
      exit 1
    fi
  done
fi

# 문제가 없다면 정상 종료합니다.
exit 0
```

### 2. `prepare-commit-msg` Hook

Git이 커밋 메시지를 생성한 후 편집기를 실행하기 전에 실행됨.

이 훅은 사용자가 커밋 메시지를 수정하기 전에 먼저 프로그램으로 수정하고 싶을 때 사용함.

이 훅은 커밋 메시지가 들어 있는 파일의 경로, 커밋의 종류를 인자로 받음. 

그리고 최근 커밋을 수정할 때(Amending 커밋) SHA-1 값을 추가 인자로 더 받음.

아래 예제 코드에서는 $1, $2, $3 로 나타남.

이 훅은 일반 커밋에는 별로 필요 없고 커밋 메시지를 자동으로 생성하는 커밋에 좋음. 

커밋 메시지에 템플릿을 적용하거나, Merge 커밋, Squash 커밋, Amend 커밋일 때 유용함.

이 스크립트로 커밋 메시지 템플릿에 정보를 삽입할 수 있음.

**예제**

```bash
#!/bin/sh
COMMIT_MSG_FILE=$1
COMMIT_SOURCE=$2
SHA1=$3

if [ "$COMMIT_SOURCE" = "message" ]; then
  exit 0
fi

if [ -z "$SHA1" ]; then
  BRANCH_NAME=$(git symbolic-ref --short HEAD)
  TICKET_NUMBER=$(echo $BRANCH_NAME | grep -oE '[A-Z]{2,}-[0-9]+')
  if [ -n "$TICKET_NUMBER" ]; then
    sed -i.bak -e "1s/^/$TICKET_NUMBER /" $COMMIT_MSG_FILE
  fi
fi

```

### 3. `commit-msg` Hook

커밋 메시지가 들어 있는 임시 파일의 경로를 인자로 받음. 

그리고 이 스크립트가 0이 아닌 값을 반환하면 커밋되지 않음. 

이 훅에서 최종적으로 커밋이 완료되기 전에 프로젝트 상태나 커밋 메시지를 검증함.

**예제**

```bash
#!/bin/sh
COMMIT_MSG_FILE=$1

if grep -q 'FIXME' "$COMMIT_MSG_FILE"; then
  echo "ERROR: Commit message contains 'FIXME'"
  exit 1
fi

```


### 4. `post-commit` Hook

커밋이 완료되면 실행됨. 

이 훅은 인자를 전달받지 않지만, 커밋 해시 정보는 `git log -1 HEAD` 명령을 사용해 쉽게 가져올 수 있음.

일반적으로 이 스크립트는 커밋된 것을 누군가 혹은 다른 프로그램에게 알릴 때 사용함.

**예제**

```bash
#!/bin/sh
COMMIT_HASH=$(git log -1 HEAD --pretty=format:%H)

echo "커밋 완료: $COMMIT_HASH"

```

