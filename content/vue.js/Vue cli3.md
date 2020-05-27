---
title: "Vue CLI"
weight: 2
pre: "<b>2. </b>"
tags: ["Vue","Vue.js"]
---

# Vue CLI3

### 설치

----

> [공식 문서](https://cli.vuejs.org/guide/)

- 설치 순서 (Windows10)

  ```bash
  $ npm install -g @vue/cli
  $ vue --version # 버젼확인
  ```

### 프로젝트 생성 및 실행

----

```bash
$ vue create <프로젝트 명>
$ cd <프로젝트명> # 프로젝트 폴더로 이동
$ npm run serve
```

### 프로젝트 정리

----

- `src/App.vue`에서 필요없는 기본코드 삭제(아래처럼)

  ```vue
  <template>
    <div id="app">
    </div>
  </template>
  <script>
  export default {
    name: 'App',
    components: {},
    //여기에 data, methods 등 넣으면 됨
      
  }
  </script>
  
  <style>
  </style>
  ```

### 컴포넌트 사용 방법

----

1. 컴포넌트 생성 : components폴더에 `.vue`만들기(PascalCase로 이름만들기)
2. 불러오기 : `import 컴포넌트이름 from 경로`
3. 등록하기 : components 속성에 등록
4. 사용하기 : `<template>` 내부에 html 태그처럼 추가

### 컴포넌트 기초 만들기

----

1. `.vue`파일 만들고 `vue + tab` 또는 `< + tab`으로 기초 작성

2. `<template>` 태그 안에 `<div>`안에 넣어줘야함

3. `App.vue`에서 연결

   ```vue
   <template>
     <div id="app">
       <Index/>
     </div>
   </template>
   
   <script>
   import Index from './components/Index.vue'
   
   export default {
     name: 'App',
     components: {
       Index,
     }
   }
   </script>
   
   <style>
   </style>
   ```

4. components에서 data 작성 시  `data: function () {}`으로 작성

5. lodash 사용하여 샘플링 기능 추가

   ```bash
   $ npm i lodash
   ```

## Vue-Router

> [공식 문서](https://router.vuejs.org/installation.html) 참조

### 설치

----

```shell
$ vue add router
```

### 컴포넌트 사용하기

----

1. 컴포넌트를 만든다.
2. 컴포넌트를 가져온다.
3. 컴포넌트를 등록.
4. 컴포넌트 사용.
5. views폴더에 `.vue`파일 작성

### router 폴더

----

> index를 만들었을 때

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'
import Index from '../views/Index.vue'

Vue.use(VueRouter)

  const routes = [
    {
      path: '/',
      name: 'Index',
      components: Index
    },
]

const router = new VueRouter({
  mode: 'history',
  base: process.env.BASE_URL,
  routes
})

export default router
```

### History Mode

----

- SPA의 단점은 주소가 바뀌지 않는다는 것. 주소가 바뀌지 않는다는 것은 사용자가 어떤 페이지에 요청을 보내 응답을 받았는지에 대한 history 관리를 할 수 없다는 뜻.
- 초창기에는 주소 뒤에 #(해쉬)또는 #!(해쉬뱅)을 붙이고 뒤에 하위 주소를 넣었다 (history mode off 하면 확인 가능)
- 브라우저에서 제공하는 주소 API를 사용해 주소를 바꾼다. 이를 위해 사용하는 것이 history API다.