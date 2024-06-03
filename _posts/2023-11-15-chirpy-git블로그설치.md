---
title: Chirpy 테마를 이용한 블로그 만들기
date: 2023-11-15 10:49:33 +0900
autor: 의현's
categories: [github, chirpy]
tags: [jekyll, chirpy, blog]
description: jekyll, chirpy, blog, 의현
toc: true
toc_sticky: true
toc_label: 목차
math: true
mermaid: true
---

> Jekyll Chirpy테마를 이용하여 깃 블로그 만들기에 대하여 알아보겠습니다. \
> 제가 직접 하며 느꼈던 에러 또한 정리했습니다. 


<br>

# Jekyll Chirpy Theme 가져오기 
1. [Chirpy github page](https://github.com/cotes2020/jekyll-theme-chirpy/releases)에서 zip파일을 다운하거나
2. [Chirpy github page](https://github.com/cotes2020/jekyll-theme-chirpy/releases)에서 fork하는 방법이 있습니다. 

필자는 첫 번째 방법을 이용했습니다. 

## 사전 준비물 
1. [Ruby 사이트](https://rubyinstaller.org/downloads/)에서 버전 3이상을 다운받아 줍니다.  
윈도우 환경에서는 Start Command Prompt with Ruby 프롬프트 창을 이용해주세요. 
```shell
brew install rbenv
ruby -v
```
2. [Node.js](https://nodejs.org/en/download)를 설치해줍니다. 
```shell
brew install node
node -v 
```


## 1. zip파일 다운로드한 경우 

1. Github 자신의 repository를 생성합니다. 
    - Repository 이름은 `<자신의 github 계정명>.github.io`로 생성합니다. 
    - public으로 설정합니다. 
    - **Add a Readme file**은 체크 해제하여 branch를 생성하지 않도록 합니다. 
![Desktop View](https://github.com/Euihyunee/euihyunee.github.io/blob/main/assets/img/git/github-new-repository.png?raw=true)


2. local에서 원하는 경로에 zip파일을 압축 해제합니다. 
3. 터미널로 해당 폴더에 접속합니다. 
4. `.gitignore` 파일에 제외할 파일을 추가해줍니다. 
```bash
echo "Gemfile.lock" >> .gitignore
```
5. 해당 폴더를 git 저장소와 원격 연결합니다.  
```bash
git init
git remote add origin https://<token>@github.com/<git계정명>/<Repository이름>.git
```  
> token을 이용하는 이유는 더 이상 github에서 https로 연결을 지원하지 않기 때문입니다. \
> token을 생성하는 방법은 [여기](https://potato98.tistory.com/82)를 참조해주세요.
{: .prompt-info }

6. `main` branch를 생성하고 폴더의 내용을 commit/push 합니다. 
```bash
git branch -M main
git add .
git commit -m "commit first"
git push -u origin main
```

<br>

## 2. fork한 경우 

### chirpy 테마 fork

우선 [Chirpy github page](https://github.com/cotes2020/jekyll-theme-chirpy/releases)에서 fork부터 진행합니다.  
fork할 때 Repository이름은 `<자신의 github 계정명>.github.io`로 생성합니다.  
![Desktop View](https://github.com/Euihyunee/euihyunee.github.io/blob/main/assets/img/git/git-fork.png?raw=true)

### branch 변경 
github 페이지에서 branch를 master에서 main으로 변경합니다. 

### clone 받기 

로컬에서 원하는 폴더에 clone해줍니다. 
```shell
git clone https://<token>@github.com/<git계정명>/<Repository이름>.git
```
> token을 이용하는 이유는 더 이상 github에서 https로 연결을 지원하지 않기 때문입니다. \
> token을 생성하는 방법은 [여기](https://potato98.tistory.com/82)를 참조해주세요.
{: .prompt-info }

### chirpy 초기화하기

터미널 내 clone한 폴더에서 아래 명령어를 사용하여 chirpy 초기화를 진행합니다. 
```shell
tools/init
[INFO] Initialization successful! <-- 성공 메세지입니다. 
```
위의 명령어 수행 시 다음 파일들이 삭제됩니다. 
- .travis.yml
- _posts 폴더 하위의 폴더들
- docs 폴더 

<br>

## 3. GitHub Action 설정 

1. 위의 내용을 마쳤다면 github 페이지로 들어가 자신의 `Repostory - Settings - Pages - Build and Deployment`에서  
GitHub Actions로 변경합니다.  
![Desktop View](https://github.com/Euihyunee/euihyunee.github.io/blob/main/assets/img/git/git-action-1.png?raw=true)

2. configure 클릭 
3. commit 클릭 
4. 이후 도메인에 'https://<자신의 깃허브 아이디>.github.io'를 입력하여 페이지가 배포된지 확인합니다.  
![Desktop View](https://github.com/Euihyunee/euihyunee.github.io/blob/main/assets/img/git/git-init-page.png?raw=true)


> Github repository의 Action 탭에서 실행이 끝나야 들어가집니다. \
> 노란불이면 실행 중, 초록불이면 완료, 빨간불이면 에러입니다. 
{: .prompt-warning }

### Workflow permissions 변경 

`Setting - Actions - general`에서 `workflow permissions`를 
Read and write permissions로 변경하고 save를 눌러줍니다.  
![Desktop View](https://github.com/Euihyunee/euihyunee.github.io/blob/main/assets/img/git/git-workflow.png?raw=true)

위의 과정을 마치고 배포가 성공적으로 되었다면 로컬에서 pull을 통해 바뀐 내용을 가져옵니다. 
```shell
$ git pull
```

<br>

## 4. 로컬 실행 환경 세팅 

1. Jekyll 실행을 위해 필요한 모듈 설치 
```shell
$ bundle
```
2. webrick 설치 
```shell
# Ruby 3.0 이상부터 webrick을 설치해줘야 에러가 안 납니다.
gem install webrick
```
3. Node.js 모듈 설치 
```bash
$ npm install && npm run build
```
> node.js 모듈을 설치하지 않으면 `assets/js/dist/*min.js Not Found` 에러 발생과 함께 블로그 기능이 정상적으로 동작하지 않습니다.
{: .prompt-warning }
> 윈도우에서는  \
    '&&' 토큰은 이 버전에서 올바른 문 구분 기호가 아닙니다.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : InvalidEndOfLine
    이런 에러가 나온다면 그냥 한 줄씩 입력하면 됩니다.
{: .prompt-info }

3. 로컬에서 Jekyll을 실행합니다. 
```shell
$ jekyll serve 
```

4. 웹 브라우저에서 [htps://127.0.0.1:4000](https://127.0.0.1:4000) 또는 [https://localhost:4000](https://localhost:4000)에서 동작이 잘 되는지 확인합니다. 

<br>




