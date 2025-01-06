---
title: 📌 Jekyll Build Error
date: 2025-01-06 14:13 +0900
categories: [Github, Chirpy]
tags: [Github Blog, Jekyll, Chirpy]
---

## 깃허브 블로그 Error!!

GitBlog를 작성하다가 Push한 게시물이 나오지 않아 git build를 확인했습니다.  

![git_build_error.png](https://github.com/Euihyunee/euihyunee.github.io/blob/main/_posts/img/git_build_error.png?raw=true)


Error 부분을 해석해보면 

Error: The current runner (ubuntu-24.04-x64) was detected as self-hosted because the platform does not match a GitHub-hosted runner image (or that image is deprecated and no longer supported).

오류: 현재 러너(ubuntu-24.04-x64)는 플랫폼이 GitHub에서 호스팅하는 러너 이미지와 일치하지 않거나 해당 이미지가 더 이상 지원되지 않기 때문에 자체 호스팅으로 감지되었습니다.

이에 관한 방법을 찾아본 결과 2가지 방법이 나왔습니다.

## 방법1. runner 이미지 업데이트 

워크플로우 파일('.github/workflows/jekyll.yml')에서 'runs-on' 설정을 최신 버전으로 변경하는 방법이 있습니다. 

```
jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
```

제 블로그의 `yml`파일은 이미 'ubuntu-latest'를 사용하고 있습니다.  
이를 사용하면 GitHub에서 지원하는 최신 Ubuntu 버전을 자동으로 사용합니다. 

## 방법2. Ruby 설정 업데이트 

워크플로우 파일('.github/workflows/jekyll.yml')에서 'ruby/setup-ruby' 액션을 최신 버전으로 업데이트하는 방법입니다. 

```yml
# 수정전 
- name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: '3.1' 
          bundler-cache: true 
          cache-version: 0

# 수정후
- name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42
        with:
          ruby-version: '3.1' 
          bundler-cache: true 
          cache-version: 0
```

저는 두 번째 방법을 사용하여 `workflow/jekyll.yml`을 수정하여 해결하였습니다. 

