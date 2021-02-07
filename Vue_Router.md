![router](https://user-images.githubusercontent.com/42309919/106829010-a3a92700-66ce-11eb-9a9b-b9be1ab65f09.PNG)
                                
## 설치
### NPM
```npm install vue-router```

## 라우터 객체 생성
뷰 인스턴스 생성 객체에는 router 속성이 있다. 뷰 라우터를 사용하려면 이 속성으로 뷰 라우터 객체를 넘겨줘야 한다.
```
// main.js

import Vue from "vue"
import App from "./App.vue"
import router from "./router"

new Vue({
  el: "#app",
  render: h => h(App),

  // 라우터 객체를 넘겨준다
  router,
})
```
                            
뷰 라우터는 플러그인 형태이기 때문에 Vue.use() 함수를 이용해서 등록한다 
```
// router/index.js

import Vue from "vue"
import VueRouter from "vue-router"

// 뷰 어플리케이션에 라우터 플러그인을 추가한다.
Vue.use(VueRouter)
```
                                     
VueRouter 클래스 함수로 라우터 객체를 생성한다.        
```
// router/index.js

/* 생략 */
const router = new VueRouter({
  mode: "history",
  routes: [
    { path: "/", component: Home },
    { path: "*", component: NotFound },
  ],
})

export default router
```

## 라우터 특성
- Vue 라우터는 기본적으로 "해쉬뱅 모드"로 ```'루트 URL'/#/'라우터 이름'```의 구조로 되어 있다.                              
```example.com/#/user``` 여기서 ‘#’ 값을 제외하고 브라우저 history객체의 pushState() API를 사용하기 위해서는 "history"로 모드값을 설정한다.
```
new VueRouter({
  mode: "history"
});
```                               
- ```routes```는 경로와 컴포넌트로 이뤄진 컬렉션이다. 이 컬렉션은 순서가 중요하다. 정의한 순서대로 경로를 매칭하여 컴포넌트를 출력한다. 

## 라우터 뷰 
라우팅이 경로에 따라 컴포넌트를 바꿔치기해서 렌더링을한다. 여기서 렌더링을 해주는 부분이 ```<router-view>```태그 부분이다. 
```
<!-- App.vue -->

<template>
  <div>
    <!-- 여기에 라우터가 컴포넌트를 그린다 -->
    <router-view></router-view>
  </div>
</template>
```
                     
## 라우터 링크
```
<!-- App.vue -->

<template>
  <div>
    <!-- 라우터 링크를 추가 -->
    <router-link to="/">Home</router-link>
    <router-view></router-view>
  </div>
</template>
```
#### 라우터에 등록된 링크는 <a>태그 보다는 <router-link>태그를 사용하길 권장한다. 왜일까?
- 히스토리모드와 해쉬뱅 모드에서는 주소 체계가 달라서 ```<a>``` 태그를 사용할 경우 모드 변경시 주소값을 일일이 변경해 줘야한다. 하지만 ```<router-link>```는 변경할 필요가 없다.
- ```<a>``` 태그를 클릭하면 화면을 갱신하는데 ```<router-link>```는 이를 차단해준다. 갱신 없이 화면을 이동할 수 있다.
                           
## Vue router로 데이터 전달하기
#### 두 가지 방법 
vue router로 데이터를 전달하는 방법은 2가지가 있다.
- query
- param
       
#### query와 params 뭐가 다를까?
##### params
- name형태에서 파라미터를 전달할 때 사용 
- 주소에 포함된 변수 ex)http://sample/user/evan/post/123
- 어떤 resource를 식별하고 싶을때 사용                               
패턴: /user/:username/post/:post_id                      
일치하는패스: /user/evan/post/123                               
$route.params: {username: 'evan', post_id: '123}                 

##### query
- JSON 형태이며 query string 형태로 전달되는 파라미터 
- 주소 '?' 이후의 변수 ex)ex)http://sample?user=evan&post=123
- 정렬이나 필터링을 하고 싶을때 사용                        
패턴: ?user=:username&post=:post_id                                       
일치하는패스: ?user=evan&post=123                   
$route.query: {username: 'evan', post_id: '123}          
                                
#### 전달하기
query            
```{name: 'Query', query: {name: 'cat', age: 3}}```          
params             
```{name: 'Params', params: {name: 'dog', age:4}}```       
```
<template>
  <div>
    <span @click="clickList">Query</span>
    <router-link :to="{name: 'Query', query: {name: 'cat', age: 3}}">
      Query 선언적 방식
    </router-link>
    <span @click="clickParams">params</span>
    <router-link :to="{name: 'Params', params: {name: 'dog', age:4}}">
      params 선언적 방식
    </router-link>
  </div>
</template>

<script>
    export default {
        name: 'Main',
        methods: {
            clickList () {
                this.$router.push({name: 'Query', query: {name: 'cat', age: 3}})
            },
            clickParams () {
                this.$router.push({name: 'Params', params: {name: 'dog', age:4}})
            }
        }
    }
</script>
```     
#### 받기
query                                   
```{{ $route.query.name }}```                 
params                            
```{{ $route.parmas.name }}```             

## 프로그래밍 방식 네비게이션 (Programmatic Navigation)            
#### router.push(location, onComplete?, onAbort?)
- url 이동
- 이 메소드는 새로운 url을 히스토리 스택에 넣기 때문에 뒤로가기 버튼 동작시 이전 url 이동 
- ```<router-link :to="home">```을 클릭하면 ```router.push('home')```을 호출하는 것과 같다. 
- onComplete 탐색이 성공적으로 완료되었을때 호출 
- onAbort 탐색이 중단되었을때 호출 
```
// string
this.$router.push(`/b/${id}`)
// object
this.$router.push({path: 'home'})       
// 쿼리와 함께 사용, 결과는 /register?plan=private 
// 체크방법: this.$route.query 
router.push({ path: 'register', query: { plan: 'private' }})
```           
                          
#### router.replace(location)
- url 이동
- 단순히 현재 페이지를 전환하는 역할 
- 현재 url을 대체하기 때문에 히스토리 스택 쌓이지 않음
- ```<router-linkt :to="about" replcae>```을 클릭하면 ```this.$router.replace('about')```을 호출하는 것과 같다.       
                                                 
#### router.go(n)           
- 숫자만큼 히스토리 스택에서 뒤로가기 or 앞으로 가기(양수: forward, 음수: backward)          
- 해당 숫자의 url이 스택에 없으면 라우팅 실패     
```
// 한 단계 앞으로 간다. history.foward() 와 같다.
this.$router.go(1)
// 한 단계 뒤로 간다. history.back() 와 같다.
this.$router.go(-1)
```                                           

## 이름을 가지는 라우트 (Named Routes)  
#### 왜 이름을 가지는 라우트를 사용할까?
때로는 라우트에 연결하거나 탐색을 수핼 할 때 이름이 있는 라우트를 사용하는것 편리하다.               
예를 들어 이름을 가지는 라우트를 사용할 경우 현재 라우팅 name으로 페이지별 리스트를 가져올 수 있다.                                  
ex) ```this.$store.dispatch('FETCH_LIST', this.$route.name)```

#### 사용 방법 
- 라우트에 name을 추가 ex) ```routes: [{ path: '/', name: 'home', component: Home }]``` 
- 이름을 가진 라우트에 링크 ex) ```<router-link :to="{ name: 'home' }">home</router-link>``` 
- ```this.$route.name```으로 현재 라우팅 name을 알 수 있다. 
                       
#### 예제 코드 
https://github.com/vuejs/vue-router/blob/dev/examples/named-routes/app.js

## 이름을 가지는 뷰 (Named Views)
#### 왜 이름을 가지는 뷰를 사용할까?
특정 url로 이동했을 때 여러 개의 컴포넌트를 중첩하지 않고 동시에 표시할 수 있는 방법이다.
                                      
#### 사용방법
- 이름이 없는 ```router-view```는 이름으로 ```default```가 주어진다.
```
<div id="app">
  <router-view name="appHeader"></router-view>
  <router-view></router-view>
  <router-view name="appFooter"></router-view>
</div>
```      
```
{
  path : '/home',
  // Named Router
  components: {
    appHeader: AppHeader,
    default: Body,
    appFooter: AppFooter
  }
},
```    

#### 결과화면 
![namedviews](https://user-images.githubusercontent.com/42309919/106849755-a91a6780-66f6-11eb-9cbb-119762f696f1.PNG)                       

                                       
#### 예제코드 
https://jsfiddle.net/posva/6du90epg/ 

## 중첩된 라우트 (Nested Routes)
#### 왜 중첩된 라우터를 사용할까?
라우터를 기준으로 화면을 이동할 때 Nested Router를 이용해서 지정된 하위 컴포넌트를 표시할 수 있다. 물론 컴포넌트의 구조는 Parent - Child 구조여야 한다.
                         
#### 사용방법 
중첩된 라우트는 ```children``` 속성으로 하위 라우트를 정의할 수 있다.          
```
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```
```
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // /user/:id/profile 과 일치 할 때
          // UserProfile은 User의 <router-view> 내에 렌더링 됩니다.
          path: 'profile',
          component: UserProfile
        },
        {
          // /user/:id/posts 과 일치 할 때
          // UserPosts가 User의 <router-view> 내에 렌더링 됩니다.
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```
#### 결과화면 
![routing](https://user-images.githubusercontent.com/42309919/106849201-9f443480-66f5-11eb-9de5-2d3fd0c9c956.PNG)
                         
#### 예제코드   
http://jsfiddle.net/yyx990803/L7hscd8h/
                      
## Nested Routes vs Named Views
- 특정 URL에 지정된 1개의 컴포넌트가 여러 개의 하위 컴포넌트를 갖는 것을 Nested Router
- 특정 URL에 여러 개의 컴포넌트를 영역 별로 지정하여 렌더링 하는 것을 Named View
![named view](https://user-images.githubusercontent.com/42309919/106830361-4cf11c80-66d1-11eb-8b9f-db9992f77f14.PNG)


                                            
***
## 참고
#### Vue router 공홈              
https://router.vuejs.org/kr/
#### Vue.js 입문서 - 프론트엔드 개발자를 위한
https://joshua1988.github.io/web-development/vuejs/vuejs-tutorial-for-beginner/
#### vue router로 데이터 전달하기
https://velog.io/@skyepodium/vue-router%EB%A1%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A0%84%EB%8B%AC%ED%95%98%EA%B8%B0-eskrsmr3
#### Vue-Router 살펴보기
https://jeonghwan-kim.github.io/2018/04/07/vue-router.html
#### Path Variable과 Query Parameter는 언제 사용해야 할까?
https://ryan-han.com/post/translated/pathvariable_queryparam/
