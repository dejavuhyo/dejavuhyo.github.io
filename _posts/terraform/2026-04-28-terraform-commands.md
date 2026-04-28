---
title: Terraform 명령어
author: dejavuhyo
date: 2026-04-28 13:40:00 +0900
categories: [DevOps, Terraform]
tags: [terraform, terraform-commands, terraform-init, terraform-plan, terraform-apply, terraform-destroy, 테라폼, 테라폼-명령어]
---

![terraform-logo](/assets/img/2026-04-28-terraform-commands/terraform-logo.png)

## 1. terraform init
이 명령어를 실행하면 테라폼은 현재 디렉토리의 구성 파일(`.tf`)을 읽고 다음의 3가지 주요 작업을 수행한다.

### 1) 핵심 작업
#### (1) 프로바이더(Provider) 설치
테라폼은 AWS, Azure, GCP 같은 외부 플랫폼과 통신하기 위해 '프로바이더'라는 플러그인이 필요하다.

* 코드에 작성된 프로바이더 구문을 확인한다.

* 필요한 플러그인을 테라폼 레지스트리에서 다운로드하여 `.terraform` 폴더에 저장한다.

#### (2) 백엔드(Backend) 초기화
테라폼은 인프라의 상태를 기록하는 `terraform.tfstate` 파일을 관리한다.

* 상태 파일을 로컬에 저장할지, 아니면 원격(S3, Terraform Cloud 등)에 저장할지 설정한다.

* 원격 백엔드 설정이 변경되었다면, 이 과정을 통해 새로운 위치로 상태 데이터를 연결한다.

#### (3) 모듈(Module) 초기화
코드 내에서 외부 모듈이나 로컬 모듈을 사용하고 있다면, 해당 모듈의 소스 코드를 가져와서 사용할 수 있도록 준비한다.

### 2) 생성되는 파일 및 디렉토리
명령어 실행이 성공하면 작업 디렉토리에 다음과 같은 변화가 생긴다.

* `.terraform/` 폴더: 다운로드된 프로바이더 플러그인과 모듈 복사본이 저장되는 숨김 폴더이다.

* `.terraform.lock.hcl` (종속성 잠금 파일): 사용된 프로바이더의 정확한 버전과 체크섬을 기록한다. 이는 여러 개발자가 동일한 환경을 유지할 수 있게 돕는 파일이다.

### 3)다시 실행 시점
아래와 같은 변화가 생기면 다시 `terraform init`을 실행해야 한다.

* 새로운 프로바이더를 코드에 추가했을 때

* 새로운 모듈을 추가했을 때

* 백엔드 설정(예: S3 버킷 변경 등)을 수정했을 때

* 다른 팀원이 업데이트한 코드를 새로 내려받았을 때

> `terraform init`은 여러 번 실행해도 안전하다. 구성에 변화가 없다면 테라폼이 이를 감지하고 아무 작업도 하지 않거나 필요한 부분만 업데이트한다.

## 2. terraform plan
테라폼 워크플로우에서 '실행 전 시뮬레이션' 단계를 담당하는 명령어이다. 코드를 실제 인프라에 적용하기 전에, 어떤 변화가 일어날지 미리 검토하는 '드라이 런(Dry Run)' 역할을 한다.

### 1) 주요 역할
이 명령어를 실행하면 테라폼은 다음 세 가지를 대조하여 변경 사항을 계산한다.

* 현재 코드 (`.tf` 파일): 사용자가 원하는 최종 상태(Desired State)

* 상태 파일 (`terraform.tfstate`): 테라폼이 마지막으로 파악한 인프라 상태

* 실제 인프라 (Cloud): 현재 클라우드에 실제로 생성되어 있는 자원의 실시간 상태

### 2) 실행 결과 읽는 방법
`terraform plan`을 실행하면 터미널에 리소스 앞에 기호가 표시된다. 이 기호들을 통해 어떤 작업이 수행될지 알 수 있다.

* `+` (create): 새로운 리소스를 생성한다.

* `-` (destroy): 더 이상 코드에 정의되지 않은 리소스를 삭제한다.

* `~` (update in place): 기존 리소스를 삭제하지 않고 설정값만 변경한다.

* `+/-` (replace): 수정이 불가능한 항목을 변경할 때, 기존 것을 삭제하고 새로 생성한다. (주의 깊게 봐야 한다.)

결과 예시:

```text
Plan: 2 to add, 1 to change, 0 to destro
```

### 3) 주요 옵션
상황에 따라 `plan` 명령어를 더 정밀하게 제어할 수 있는 유용한 옵션이다.

* `-out=path` (계획 저장): 생성된 실행 계획을 파일로 저장한다.
  - plan 결과를 저장해 두었다가 `terraform apply "tfplan"`으로 실행하면, 계획했던 내용과 실제 적용 내용이 100% 일치함을 보장할 수 있다. (협업 및 CI/CD 환경에서 필수)

* `-target=resource` (특정 리소스 지정): 전체 코드가 아닌 특정 리소스에 대해서만 계획을 확인한다.
  - 예시: `terraform plan -target=aws_instance.web`
  - 주의: 리소스 간 의존성 때문에 예상치 못한 결과가 나올 수 있으므로 디버깅 용도로만 권장한다.

* `-var 'key=value'` (변수 즉시 주입): 코드 내부의 변수(variable) 값을 명령줄에서 바로 입력한다.

* `-destroy` (삭제 계획 확인): 현재 구성된 모든 리소스를 삭제할 때 어떤 일이 벌어질지 확인한다.

## 3. terraform apply
실제로 인프라를 생성, 수정, 삭제하는 '실행' 단계이다. `plan`에서 제안된 변경 사항을 실제 클라우드 환경(AWS, Azure, GCP 등)에 반영하는 명령어이다.

### 1) 작동 과정
이 명령어를 실행하면 테라폼은 내부적으로 다음과 같은 단계를 거친다.

* Implicit Plan (암시적 계획): 별도의 `plan` 파일을 지정하지 않고 `apply`를 실행하면, 테라폼은 자동으로 `plan`을 먼저 수행하여 변경 사항을 화면에 보여준다.

* User Confirmation (사용자 승인): 변경 사항을 적용할지 묻는 프롬프트(Do you want to perform these actions?)가 뜬다. 여기서 `yes`를 입력해야 실행된다.

* Execution (실행): 코드에 정의된 리소스를 순서에 맞게 생성/수정/삭제 한다.

* State Update (상태 업데이트): 작업이 끝나면 실제 리소스의 상태를 `terraform.tfstate` 파일에 기록한다.

### 2) 주요 옵션

* `-auto-approve` (승인 절차 생략): yes를 입력하는 확인 절차를 건너뛰고 바로 실행한다.
  - 용도: CI/CD 파이프라인(Jenkins, GitHub Actions 등)에서 자동 배포를 구성할 때 필수적으로 사용된다.

* plan 파일 지정 실행
  - 용도: 이전에 `terraform plan -out=tfplan`으로 저장해둔 실행 계획을 그대로 실행한다.
  - 장점: `plan`을 수행했던 시점의 계획을 그대로 반영하므로, 그 사이 발생할 수 있는 변수 변경이나 상태 변화로 인한 사고를 방지한다. 이 경우 별도의 승인 프롬프트가 뜨지 않는다.

* `-var` 및 `-var-file`
  - `plan`과 마찬가지로 실행 시점에 변수 값을 직접 주입하거나 변수 파일(`.tfvars`)을 지정할 수 있다.

* `-parallelism=n` (병렬 처리 제어): 동시에 생성하거나 수정할 리소스의 개수를 제한한다. (기본값은 10)
  - 용도: API 요청 제한(Rate Limit)이 엄격한 클라우드 환경에서 작업할 때 숫자를 낮추어 에러를 방지할 수 있다.

### 3) 결과 메시지
실행이 완료되면 터미널 하단에 요약 메시지가 출력된다.

```text
Apply complete! Resources: 5 added, 2 changed, 1 destroyed.
```

* added: 새롭게 생성된 리소스 수

* changed: 기존 리소스 중 설정이 변경된 수

* destroyed: 코드에서 삭제되어 실제 인프라에서도 제거된 수

### 4) 주의사항

* 반드시 plan 결과 확인: `apply`는 되돌리기(Rollback) 기능이 따로 없다. 삭제 후 재생성(`replace`)되는 리소스가 있는지 반드시 확인한다.

* State 파일 관리: `apply`가 완료되면 상태 파일이 업데이트된다. 팀 협업 시에는 S3나 Terraform Cloud 같은 원격 백엔드(Remote Backend)를 사용하여 상태 파일이 충돌하지 않도록 관리해야 한다.

* 의존성(Dependency): 테라폼은 리소스 간의 의존성을 자동으로 계산하지만, 가끔 명시적으로 `depends_on`을 써줘야 하는 경우도 있다.

## 4. terraform destroy
테라폼으로 관리하던 모든 인프라 리소스를 안전하게 삭제하는 명령어이다. 단순히 리소스를 지우는 것을 넘어, 테라폼의 상태 파일(`tfstate`)을 기준으로 리소스 간의 의존성을 파악해 올바른 순서로 삭제를 진행한다.

### 1) 작동 원리
이 명령어는 내부적으로 `terraform plan -destroy`를 실행한 후, 그 결과를 실제 인프라에 적용하는 것과 같다.

* 의존성 분석: 예를 들어, VPC 안에 EC2 인스턴스가 있다면 EC2를 먼저 삭제하고 그 다음 VPC를 삭제하는 식으로 순서를 자동으로 계산한다.

* 상태 파일 기준: 코드(`*.tf`)에 정의되어 있지 않더라도, 현재 `terraform.tfstate` 파일에 기록되어 있는 리소스를 찾아 삭제한다.

* 확인 절차: 실행 시 변경 사항을 보여주며, 정말 삭제할 것인지 묻는 프롬프트(`Do you really want to destroy all resources?`)가 뜬다.

### 2)주요 옵션

* `-auto-approve` (자동 승인): yes 입력 절차 없이 즉시 삭제를 시작한다.
  - 주의: 한 번 삭제된 인프라는 복구할 수 없으므로, CI/CD 파이프라인이나 테스트 환경이 아닌 실제 운영 환경에서는 절대 주의해서 사용해야 한다.

* `-target=resource` (특정 리소스만 삭제): 전체 인프라가 아닌 특정 리소스 하나만 골라서 삭제하고 싶을 때 사용한다.
  - 예시: `terraform destroy -target=aws_instance.test_server`
  - 참고: 현대 테라폼에서는 특정 리소스를 지우고 싶을 때 코드를 삭제하고 `terraform apply`를 하는 방식을 더 권장하지만, 급한 상황에서는 이 옵션이 유용하다.

* `-refresh=false`
  - 실제 인프라 상태를 다시 확인(Refresh)하지 않고, 현재 가지고 있는 상태 파일(`tfstate`) 내용만으로 삭제를 진행한다. 인프라 규모가 커서 리프레시 시간이 오래 걸릴 때 속도를 높이기 위해 사용한다.

### 3) 리소스 삭제를 막는 방법
실수로 `destroy` 명령어를 날려 중요한 데이터베이스나 운영 서버가 삭제되는 것을 방지하기 위해 코드 레벨에서 보호 장치를 걸 수 있다.

```terraform
resource "aws_instance" "important_server" {
  # ... 설정 ...

  lifecycle {
    prevent_destroy = true
  }
}
```

* `prevent_destroy = true`가 설정된 리소스가 포함되어 있다면, `terraform destroy` 명령어 실행 시 오류가 발생하며 전체 프로세스가 중단된다.

### 4) destroy 후의 상태

* 클라우드 콘솔에서 해당 리소스들이 제거된다.

* `terraform.tfstate` 파일 내의 리소스 정보가 비워진다.

* 코드(`.tf` 파일) 자체는 그대로 남아 있다. 다시 생성하고 싶다면 `terraform apply`를 실행하면 된다.

## [출처 및 참고]
* [https://developer.hashicorp.com/terraform/cli/commands/plan](https://developer.hashicorp.com/terraform/cli/commands/plan)
* [https://developer.hashicorp.com/terraform/tutorials/cli/plan](https://developer.hashicorp.com/terraform/tutorials/cli/plan)
* [https://developer.hashicorp.com/terraform/internals](https://developer.hashicorp.com/terraform/internals)
* [https://developer.hashicorp.com/terraform/cli/commands/apply](https://developer.hashicorp.com/terraform/cli/commands/apply)
* [https://developer.hashicorp.com/terraform/tutorials/cli/apply](https://developer.hashicorp.com/terraform/tutorials/cli/apply)
* [https://developer.hashicorp.com/terraform/cli/commands/destroy](https://developer.hashicorp.com/terraform/cli/commands/destroy)
* [https://velog.io/@gentledev10/install-terraform-and-commands](https://velog.io/@gentledev10/install-terraform-and-commands)
