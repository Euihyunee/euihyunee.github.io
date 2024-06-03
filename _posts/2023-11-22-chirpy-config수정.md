---
title: Chirpy 테마 커스터마이징 
date: 2023-11-22 14:21 +0900
autor: 의현's
categories: [Github, Chirpy]
tags: [jekyll, chirpy, blog]
description: jekyll, chirpy, blog, 의현
toc: true
toc_sticky: true
toc_label : 목차
math: true
mermaid: true
---

> 이전 글을 통해 깃허브 블로그를 만들었습니다. \
> 본 글에서는 블로그 커스터마이징을 다루겠습니다. 

# _config.yml 파일 수정 

`_config.yml` 파일은 깃허브 페이지나 다른 정적 사이트 생성기를 사용하여 만든 블로그나 웹사이트의 설정 파일입니다. 이 파일은 해당 사이트의 설정과 구성 요소들을 정의하여 사이트의 title, language, avatar 등을 바꿀 수 있습니다. 

## 항목 

|항목      |값                 |설명                      |
|:-------|:------------------|:------------------------|
|lang|ko-KO 또는 en|언어를 설정하는 곳입니다. 기본값은 en이며 한글로 변경하고 싶으시다면 ko-KR로 수정하시면 됩니다. |
|timezone|Asia/Seoul|서울 표준시로 설정합니다. |
|title| |프로필 아래 타이틀입니다.|
|tagline| |타이틀 아래 부연설명입니다.|
|description| |SEO를 위한 키워드들을 입력합니다.|
|url|https://euihyunee.github.io|블로그 url을 입력해줍니다.|
|github|`githubID`|본인의 깃허브 아이디를 입력합니다.|
|twitter.username|`twwiter id`|트위터 아이디를 입력합니다. 생략 가능|
|social.name|이름|블로그 글에 표시될 나의 이름을 입력합니다.|
|social.email|이메일|이메일 계정을 입력합니다.|
|social.links|소셜 링크들|사용하는 소셜 서비스의 홈 url을 입력합니다.|
|theme_mode|light or dark|원하는 테마 스킨을 사용합니다.|
|avatar|이미지 경로|블로그 프로필의 이미지를 설정합니다.|
|toc|true|글에서 목차를 나타냅니다.|
|paginate|10|한 목록에 몇 개의 글을 표시해줄 것인지 설정합니다.|

## lang 바꾸기

기본값이 en입니다. 이를 변경하게 된다면 layout, tabs, search, panel, copyright, meta, not_found, notification, post, data time format, categories page가 변경됩니다. 어떻게 변경되는지는 `_data/locales`에 있습니다. 영어 버전이 좋다면 en을 그대로 유지하시고, 한국어 버전으로 바꾸고 싶으시다면 ko-KR로 변경해주시면 됩니다. 만약 한국어 버전에서 특정 부분만 영어로 바꾸시고 싶으시다면 `_data/locales/ko-KR.yml`파일에서 특정 부분만 en.yml 파일과 동일하게 변경해주시면 됩니다. 

## avatar 바꾸기

avatar 항목에 이미지 경로를 입력해줍니다. 
저는 `/assets/img/profile/profile.png`로 설정했습니다. 

> 만약 이미지 업로드가 안 되신다면 깃허브 리포지토리에 있는 이미지 주소를 넣어주시면 됩니다. 
{: .prompt-tip }

## 

# 폰트 색상/폰트 변경

# 왼쪽 사이드바 백그라운드 이미지 넣기 

# 사이드바 아래 아이콘 및 링크 넣기 

# Footer 설정 

# About 만들기 

# favicon 변경하기 

# 