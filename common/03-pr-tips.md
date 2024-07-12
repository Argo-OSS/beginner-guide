# PULL REQUEST TIPS

## 목차 
- [PULL REQUEST TIPS](#pull-request-tips)
  - [목차](#목차)
  - [개요](#개요)
  - [Code Contribution Guide](#code-contribution-guide)
  - [GPG 및 DCO](#gpg-및-dco)
  - [코드를 수정하기 전에 해야 하는 것](#코드를-수정하기-전에-해야-하는-것)
  - [코드를 수정 후 커밋하기 전에 해야 하는 것](#코드를-수정-후-커밋하기-전에-해야-하는-것)
  - [PR 을 올리는 법](#pr-을-올리는-법)
  - [이미 올린 PR 을 수정하는 방법](#이미-올린-pr-을-수정하는-방법)
  - [PR 절차 중 Conflict 가 발생했을 시 해결 방법](#pr-절차-중-conflict-가-발생했을-시-해결-방법)

## 개요
Argo 오픈소스에 Pull Request 를 하기 위해서 알아두어야 할 기본적인 내용을 설명합니다.

## Code Contribution Guide
Argo CD 의 [컨트리뷰션 가이드](https://argo-cd.readthedocs.io/en/stable/developer-guide/code-contributions/)를 한 번 읽어보도록 합니다.  
이 규칙은 Argo CD 에 컨트리뷰션 시 따라야 할 규칙입니다.

## GPG 및 DCO
Argo 프로젝트의 Repos 들은 GPG 를 요구합니다. 그리고 추가적으로 유저 서명인 DCO 를 요구합니다.

아래 내용을 참조하여 내 로컬에 세팅하도록 합니다. (GPG 는 별도 문서 가이드가 포함)

1. [GPG 서명](./02-gpg.md)
2. [DCO](https://github.com/apps/dco)

## 코드를 수정하기 전에 해야 하는 것

1. 현재 내 Local main 이 Origin main, Upstream main 과 일치하는 상태인지 확인합니다.
   1. 불일치해도 방법은 있지만 나중에 귀찮아집니다.
2. 반드시 main 브랜치에서 feature 브랜치를 분기합니다.
   1. 코드 수정은 feature 브랜치에서 수정되어서 feature -> main 으로 pull request 를 보내야 합니다.

## 코드를 수정 후 커밋하기 전에 해야 하는 것

1. 커밋 컨벤션을 따라야 합니다.
   1. [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/)
   2. 다른 사람들이 올린 PR 명, 브랜치 명, 각 커밋을 살펴보고 따라하는 것이 쉽습니다.
2. 로컬 테스트 실행
   1. PR 이후 각 자동 Check 단계에서 실패가 나지 않으려면 로컬에서 테스트 가능한 것은 미리 테스트해보는 것이 좋습니다.
   2. make 파일에 이미 만들어져 있는 [테스트 명령어](https://argo-cd.readthedocs.io/en/stable/developer-guide/toolchain-guide/#automated-builds-tests)가 있으며, 터미널에서 실행해볼 수 있습니다. 아래 테스트가 쓸만합니다.
      1. make test (go test)
      2. make lint (go파일 lint 검사)
      3. make lint-ui (ui lint 검사)
      4. make codegen (API, grpc 및 gateway 영역 수정, 또는 자동화 문서 생성 시 필요한 명령어)

## PR 을 올리는 법
로컬 커밋 이후 Origin 으로 Push.  
Origin Repository 에서 Upstream 으로 Pull Request 를 요청할 수 있습니다.
상세 내용은 생략.

## 이미 올린 PR 을 수정하는 방법
이미 나의 PR 이 (운이 좋게도) 다른 사람의 커밋이 위에 없는 최신이라 rebase 가 필요 없는 상황일 경우 아래와 같습니다.

1. Review 받은 내용 수정 후 git add.
2. Git commit —amend를 통해 최신 commit 덮어 쓰기
3. Git push -f origin {branch-name}

origin 에 push 가 되면 자동으로 연결된 PR 이 업데이트됩니다.

## PR 절차 중 Conflict 가 발생했을 시 해결 방법
주로 위에서 운이 나쁜 경우로, 먼저 반영된 다른 커밋에 의해 나의 소스코드가 영향 받는 경우에 발생합니다.  
이미 remote 세팅이 origin 과 upstream 이 설정되어 있다고 가정합니다.  
([github upstream 설정하기](https://nochoco-lee.tistory.com/6))

* 용어 설명
  * upstream : 원격 원본 Repository (argoproj/Argo CD)
  * origin : Fork 한 나의 원격 Repository (aaaa/Argo CD)
  * local : origin 을 clone 했던 나의 로컬 PC 에 있는 소스 코드

* **main 브랜치 동기화**
    * 먼저 반영된 다른 사람들의 commit sync 를 맞추기 위해 upstream 을 fetch 합니다.
      * `git fetch upstream`
    * 이제 main 을 동기화합니다. PR 요청 중인 브랜치를 놔두고 main 브랜치로 돌아옵니다.
        * `git checkout main`
    * 그리고 upstream 의 main 을 나의 main 으로 merge 합니다.
        * `git merge upstream/main`
    * 나의 원격 repo 의 main 브랜치에 push 합니다.
        * `git push origin main`
    * 여기까지의 작업으로 local - origin - upstream 의 main 브랜치가 동기화 되었습니다.
    * (팁) upstream - origin 간 main 브랜치 동기화는 Github 사이트에서 UI 로도 가능합니다. (차이 발생 시 자동으로 버튼이 생김)
* **feature 브랜치 동기화**
    * 이제 PR 진행 중인 feature 브랜치의 sync 를 맞춥니다.
    * 나의 main 과 feature branch 를 비교할 때 main 이 feature 에 비해 앞서 있습니다. (다른 사람의 commit 이 main 에 들어가 있으므로)
    * 따라서 rebase 로 branch 의 base 를 다시 main 과 맞춰줍니다.
    * feature 브랜치로 이동
        * `git checkout feature/aaa`
    * rebase to main
        * `git rebase main`
    * conflict 사항 해결
        * 기존 반영 사항과 충돌 발생 시 (같은 파일 작업 등) 직접 수정합니다.
        * IDE 에서 오픈해보면 충돌 지점을 수정할 수 있습니다.
        * 모든 내용을 수정 후 모든 변경 사항을 add 합니다.
            * `git add .`
    * 이제 충돌이 해결되었으니 rebase 를 속행합니다.
        * `git rebase —continue`
    * 터미널에서 commit 로그가 적힌 파일이 vim 등 편집기로 열립니다.
        * rebase 시 대상 commit 이력을 수정할 수 있습니다.
        * 현재 PR 중인 commit 이므로 취향껏 수정
    * 편집기 저장 (:wq) 하여 닫으면 rebase 계속 진행됩니다. 추가 오류가 발생할 수 있으며, 될 때까지 계속 수정해야 합니다.
    * 오류가 없다면 나의 로컬 feature 가 잘 rebase 가 된 것입니다.
    * `git status` 및 `git log —-oneline` 으로 현재 커밋 상태를 한 번 확인해주고 원격 feature 로 push 진행
        * 주의 : 기존의 commit 이력을 수정 하는 것이므로 반드시 force-push 가 필요합니다.
        * `git push -f origin feature/aaa`
* 원격 feature 브랜치로 적용되면서 자동으로 PR 에 적용됩니다.
* 완료!
