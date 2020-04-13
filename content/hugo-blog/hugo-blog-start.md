---
title: "Hugo를 이용해 Blog 만들기"
date: 2020-04-13T22:13:54+09:00
tags: ["hugo","blog"]
---

> 설치 및 실행은 모두 Windows 10에서 함.

## 1. Hugo 설치 및  Theme 정하기

- 나는 [hugo github](https://github.com/gohugoio/hugo/releases)에서 직접 [hugo_0.69.0_Windows-64bit.zip](https://github.com/gohugoio/hugo/releases/download/v0.69.0/hugo_0.69.0_Windows-64bit.zip)를 다운 받아 사용했다.
- 다운 받은 후 압축을 풀어 C:\Hugo\bin 을 만들어 그 안에 넣어주었고, 시스템 환경 변수에서 Path에 C:\Hugo\bin을 추가했다.
- 그리고 사용할 테마를 선택하며([여기](https://themes.gohugo.io/)) `$ hugo new site 폴더 이름`으로 사용할 폴더를 만들었다. 
- github에서 두개의 repo를 만들었다. (하나는 <username>.github.io 로, 하나는 hugo 관련 소스파일 등과 같은 것을 포함할 <pjt name>(위에서 만든 폴더와 동일하게, 나는 myblog라고 함))
-  `$ git submodule add https://github.com/matcornic/hugo-theme-learn.git theme/hugo-theme-learn `으로 사용할 테마를 가져옴(clone으로도 가능)

## 2. Git 과 연동시키기

- C:\Hugo\myblog로 이동하여 bash를 실행시켜 `$ git init` 후 `$ git remote add origin https://github.com/DongyeopGu/myblog.git` 으로 remote 등록함
- 만든 github.io를 submodule로 만들기 위해 `$ git submodule add http://github.com/DongyeopGu/dongyeopgu.github.io.git public`  타이핑.
- C:\Hugo\myblog에 public폴더가 생김

## 3. 자동으로 업로드 해주는 쉘 작성하기

- [여기](https://gohugo.io/hosting-and-deployment/hosting-on-github/)에 쉘 스크립트 작성 법이 나와 있지만, [여기](https://github.com/Integerous/Integerous.github.io)를 따라 쉘 스크립트를 작성. 파일 이름은 deploy.sh

  ```shell
  #!/bin/bash
  
  echo -e "\033[0;32mDeploying updates to GitHub...\033[0m"
  
  # Build the project.
  hugo -t hugo-theme-learn
  
  # Go To Public folder
  cd public
  # Add changes to git.
  git add .
  
  # Commit changes.
  msg="rebuilding site `date`"
  if [ $# -eq 1 ]
    then msg="$1"
  fi
  git commit -m "$msg"
  
  # Push source and build repos.
  git push origin master
  
  # Come Back up to the Project Root
  cd ..
  
  
  # blog 저장소 Commit & Push
  git add .
  
  msg="rebuilding site `date`"
  if [ $# -eq 1 ]
    then msg="$1"
  fi
  git commit -m "$msg"
  
  git push origin master
  ```

## 4. 블로그 글 작성하기

- hugo theme의 Demo와 Homepage에 작성 법이 나와 있어서 그걸 참고해서 작성함.

- 글을 작성하면서 댓글 기능도 넣고 싶어짐.

## 5. 댓글 기능 넣기

- disqus를 이용하는 방법이 [공식 문서](https://gohugo.io/content-management/comments/#add-disqus)에 나와 있기에 따라서 만들었음.
- C:\Hugo\myblog\config.toml에서 `disqusShortname = "disqus username"`을 추가하고 C:\Hugo\myblog\layouts\partials에 필요한 html을 넣었음.(Learn 테마의 경우 custom-comments.html과 disqus.html)
- disqus.html의 경우 disqus 홈페이지에서 알려주는대로 복사, 붙여넣기 했음.



### 중간에 수정한 것들

---

- github에 push를 할 때 계속 다시 로그인을 해야했기에 Personal Access Token 발급받아 이용.
- 테마를 정할 때 몇 개를 간단하게 사용해 보며 hugo-extended가 필요해 extended 다운 받았음. 