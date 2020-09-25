---
title: "AWS amplify deploy with github actions"
date: 2020-04-12 19:00:00
category: git
thumbnail: { thumbnailSrc }
draft: false
---

## Github-action

깃헙액션을 사용한 경험에 대해 추가로 작성해보고자 한다.  
[지난 깃헙액션 첫번째 글](https://juunone.github.io/github-action/)  

지난번 github-actions의 yml 설정에 한계에 부딪혀 배포자동화를 못하고 있던중,  
추가 수정과 삽질을 통해 드디어 github-actions를 통해 CI/CD 가 완성됐다.

**AS-IS**: `sh`을 통한 `AWS amplify CLI` 배포.  
**TO-BE**: github-actions를 통한 `push` 트리거시 `AWS amplify CLI github-actions`를 통해 배포.

결론적으로 수정된 파일을 단 2개, `workflow yml` 과 `package.json`  

## Actions

먼저 [github actions marketplace](https://github.com/marketplace/actions) 에서 2개의 액션들을 사용했다.

첫번째
- (AWS amplify actions)[https://github.com/marketplace/actions/amplify-cli-action]   

기존에 사용하고있던 `aws amplify`를 그대로 계승해서 사용하길 원했고, S3 및 Cloudfront 까지 배포를 해주기 때문에
매우 편리한 서비스다.  
`amplify CLI`를 사용할수 있는 액션이 마켓에 존재했고, 해당 모듈을 사용해서 액션을 만들수 있었다.
여기서 한가지 문제점이 있었는데, 

`amplify-cli-action@0.2.0` 버전에서는 arguments 옵션이 지원되지 않아
Cloudfront까지 배포가 안된다는 점이였다. ㅠㅠ

다른 모듈은 마땅히 사용할만한게 존재하지 않았고, 결국 해당 repo에 feature request로 issue를 생성했다.  
개발자가 대응이 빨라 `amplify-cli-action@0.2.1`에서 바로 사용 가능하게 배포됐다.

두번째
- (Slack notification actions)[https://github.com/marketplace/actions/slack-notify]

빌드 및 배포가 종료되고 마지막에 사용하고있는 슬랙에 배포 알림이 오면 좀더 손쉽게 확인할 수 있으므로,  
`job`이 성공/취소/실패 모든 상황에 항상 슬랙 알림이 뜨도록 위 슬랙 액션을 사용했다.

액션 `job`에는 `always, success, cancelled, failure` 총 4가지 상태가 존재한다.

나는 아래와 같이 1개의 잡에 스텝들을 나눴으므로, 각각의 스텝에 `if` 필드를 통해 조건을 걸었다.

```yml
- name: Slack Notification Success
if: success()
uses: rtCamp/action-slack-notify@v2.0.1
env:
  SLACK_WEBHOOK: "${{secrets.SLACK_WEBHOOK_URL}}"
  SLACK_COLOR: "#02ff0a"
  SLACK_TITLE: ":white_check_mark: ${{job.status}} Github Actions"
  SLACK_MESSAGE: "• URL: ${{env.action_state}} \n• Repo: <https://github.com/${{github.repository}}|${{github.repository}}> \n• Commit: <${{github.event.head_commit.URL}}|${{github.event.head_commit.id}}>"
  SLACK_USERNAME: ${{github.actor}}
```

> 위 처럼 yml에 추가 하고 슬랙의 알림이 오는걸 보면 아래와 같은 아웃풋이 나온다.
![slack noti](https://user-images.githubusercontent.com/35126809/79085365-390bb100-7d73-11ea-89f7-dd13dd139619.png)
출처: https://github.com/marketplace/actions/slack-notify

## Action yml

아래와 같이 yml에서 `if` 키에 들어가있는 조건은 github branch가 `develop`일 때만 해당 스텝을 실행한다.  
또한 `${{ secrets.AWS_ACCESS_KEY_ID }}` 같은 변수는 깃헙 레포의 `secrets` 메뉴에서 등록된 변수이다.  
보안이 필요한 키 혹은 웹훅 url같은건 `secrets` 메뉴에서 변수를 등록해 사용하는게 좋다.

```yml
- name: Configure amplify prod
  if: contains(github.ref, 'develop')  
  uses: ambientlight/amplify-cli-action@0.2.1
  with:
    amplify_cli_version: '3.17.0'
    amplify_env: dev
    amplify_command: configure
  env:
    AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
    AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    AWS_REGION: ap-northeast-2

- name: Build and amplify cli publish to cloud front to prod
  if: contains(github.ref, 'develop')  
  uses: ambientlight/amplify-cli-action@0.2.1
  with:
    frontend: javascript
    framework: 'react'
    amplify_cli_version: '3.17.0'
    amplify_env: dev
    amplify_command: publish
    amplify_arguments: '-c'
    build_command: 'yarn build'
  env:
    CI: ""
    AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
    AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    AWS_REGION: ap-northeast-2
```

## Debug

아래와 같이 `github` 객체를 조회해서 필요한 정보들을 슬랙에 노티를 보내는데 활용할수도있고,  
`job` 의 상태들을 불러와 각 상태에 대한 정보도 구분이 가능하다.

`toJson`으로 감싸주면 `json` 타입으로 결과를 받을 수 있다.

```yml
steps:
- uses: actions/checkout@v1

- name: Dump GitHub context
  env:
    GITHUB_CONTEXT: ${{ github }}
  run: echo "$GITHUB_CONTEXT"

- name: Dump job context
  env:
    JOB_CONTEXT: ${{ job }}
  run: echo "$JOB_CONTEXT"  
```






