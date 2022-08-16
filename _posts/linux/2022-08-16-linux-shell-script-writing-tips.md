---
title: 리눅스 쉘스크립트 작성 Tip
author: dejavuhyo
date: 2022-08-10 11:00:00 +0900
categories: [DevOps, Linux]
tags: [linux-shell-script, shell-script, shell-script-tips, shell-script-writing, shell-script, 리눅스-쉘스크립트, 쉘스크립트-작성, 쉘스크립트-팁, 쉘스크립트]
---

## 1. 쉘스크립트 작성 Tip

### 1) 쉘스크립트 실행 중 오류가 발생시 멈추기
쉘스크립트는 실행 중 오류가 발생해도 멈추지 않고 다음 작업을 진행한다. 명령어 실행 중 오류가 발생하면 그 시점에 멈추게 하기 위해서 `set -e` 옵션을 이용한다.

```shell
#!/bin/bash

set -e

echo "hello"
aaa  # set -e 옵션이 설정되면 이 시점에 스크립트 종료 
echo "world"
```

### 2) 공통함수 이용하기
쉘 스크립트에서 공통적으로 사용하는 유틸리티성 함수는 따로 스크립트로 작성후 여러 곳에서 함께 이용할 수 있다. `source` 명령으로 적용할 수 있다.

```shell
-- util.sh
#!/bin/bash

# 로그 프린트
function fncPrintLog() {
    echo "["`date -u +\%Y\%m\%d" "\%H":"\%M":"\%S`"] "$*
}

-- script.sh
#!/bin/bash

source util.sh

fncPrintLog "공통함수 이용하기!!"
```

### 3) .lock 파일 생성하여 동시 작업 막기
같은 스크립트가 동시에 작업되지 않도록 막기 위해서 `.lock` 파일을 생성하고, 이 파일의 존재 여부를 체크하여 작업이 동시에 동작하는 것을 막을 수 있다.

```shell
#!/bin/bash

if [[ -f .lock ]]; then
    echo "already running..."
else
    touch .lock
    echo "running"
    rm .lock
fi
```

## [출처 및 참고]
* <https://wikidocs.net/50694>
