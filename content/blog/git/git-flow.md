---
title: "Git flow branch strategy"
date: 2019-12-24 12:00:00
category: git
thumbnail: { thumbnailSrc }
draft: false
---

*참조* 

[우아한 형제들 블로그](http://woowabros.github.io/experience/2017/10/30/baemin-mobile-git-branch-strategy.html)  

![git-flow](https://user-images.githubusercontent.com/58495926/71458155-c9ce7900-27e4-11ea-9c89-da561042e42b.gif)

git을 사용함에 있어서 브랜치전략으로 여러가지의 전략들이 존재하는데,
e.g. `git-flow`, `github-flow`, `gitlab-flow` `bitbucket-flow` 등 여러 전략중 `git-flow`에 대해
집중적으로 다뤄보고자 한다. 

## git-flow?

git-flow에는 5가지 종류의 브랜치로 나누어지게 됩니다.

`master`, `develop`, `feature`, `release`, `hotfix`

- master : 배포 단계 브랜치
- develop : 개발 브랜치
- feature : 기능 추가/개발 브랜치
- release : 출시될 기능 사항이 모두 포함된 브랜치
- hotfix : 릴리즈 브랜치에서 발생한 버그픽스 브랜치

## flow

깃플로우의 흐름을 설명하자면  
- 모든 핫픽스는 `master`에서 브랜치가 따지고 머지된다.  
- 신규기능개발 브랜치는 `develop` 에서 따진다.  
  - `milestone` 브랜치를 하나 딴후 거기서 `feature` 브랜치를 해당브랜치에 머지하는 방식도 유용하다.
  - 모든 기능이 끝난 브랜치들은 `milestone` 혹은 `develop` 에 pull request 를 보낸다.
- 릴리즈될 기능이 포함된 `develop` 브랜치는 `release` 브랜치를 생성한다.
  - QA 진행은 `release` 브랜치에서 진행한다.  
- QA가 완료된 release 는 `develop` 및 `master` 에 머지된다.
- master 브랜치에 버전 `commit` 및 `tag`를 붙이고 배포한다.

이렇게하면 `master` 와 `develop` 은 최신화 된 기능을 포함한 브랜치이고,
모든 배포는 반드시 `master` 브랜치에서 배포된다.