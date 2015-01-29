---
layout: post
title: "oh-my-zsh mistake"
date: 2015-01-29 18:17:04
tags:
 - oh-my-zsh
---
맥북에서 bash 대신 oh-my-zsh을 사용 중인데, 갑자기 경로가 표시돼야 할 곳에 `RVM_PROJECT_PATH` 라는 문자열이기 보이기 시작했다.

검색해보니 [해당이슈](https://github.com/robbyrussell/oh-my-zsh/issues/3238)가 올라와 있는데 이미 몇달 전에 해결된 이슈였다.
하지만 이상한 것은 내 로컬의 oh-my-zsh은 최신버전이었단 사실.

도무지 이유를 찾을 수 없을 것 같았는데 수동으로 업데이트 스크립트를 실행해보다가 pull 하는 github 리파지토리가 원본 oh-my-zsh이 아니라 다른사람의 forked repository라는 사실을 발견했다.
remote를 바꿔서 잘 업데이트는 완료했는데, 과거의 내가 왜 포크 버전을 설치했는지는 미스테리다. 쨌든 일단 해결!
