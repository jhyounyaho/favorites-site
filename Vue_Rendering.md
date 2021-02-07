# Vue 렌더링 성능 개선하기

## 렌더링 성능 문제 발생
### 기존 DOM을 다루는 법의 한계 
예전과 같이 단순히 html을 서버에 받아서 표현하는게 아니라 요즘은 SPA 같은 것들이 생겨나면서, DOM을 수시로 변경해달라고 브라우저에게 요청한다.                    
노드가 수천개의 DOM트리 중 원하는 노드를 찾은 후 css를 변경하고, 다시 css 트리를 만들어 합치고, 화면에 스케치하고 색을 채운다. 이러한 과정들은 큰 비용을 투자하게 되고 그 결과 성능 저하로 인한 페이지 속도 지연이 발생한다.                       
**브라우저는 실시간 DOM 수정 요청으로 그림을 다시 그리는데 효율적이지 못하다.** 이를 해결 할 방법이 뭐가 있을까?                              

***

## 해결 방법
### 1. 가상돔(Virtual DOM)을 사용하자  
- Vue.js, React.js 등 가상돔을 사용하는 라이브러리 등장 
- Vue.js 2.0버전 이후로 가상돔을 사용하기 때문에 빠르고 효과적으로 html 화면을 렌더링한다. 
- Vue.js는 javascript 객체 형태의 Virtual DOM을 사용하여 빠르고 효과적으로 html 화면을 렌더링하여, 어플리케이션의 성능을 향상시킨다. (javascript virtual DOM처리가 실제 DOM 보다 빠르다)                      
- 컴포넌트와 가상돔을 이용한 개발은 개발속도를 빠르게하고 유지보수를 용이하게 도와준다. 나사, 어도비, 닌텐도, 깃랩, 페이스북, 넷플릭스, 샤오미, 알리바 등에서 이런 이점으로 인해 Vue.js를 사용하여 개발하고 있다.

#### Virtual DOM 작동 방식
- React.js와 Vue.js 모두 가상돔을 다룬다. **이미지는 React이나, Vue가 다루는 가상돔과 같은 원리이다.**                   
- Virtual DOM이라는 기존의 DOM을 추상화해서 업데이트 되는 UI를 반영하고, 업데이트가 완료되면 이 Virtual DOM을 이전의 Virtual DOM과 비교해서 달라진 부분만 실제 DOM에 반영하는 방식으로 레이아웃 계산을 최소화 시킴으로써 성능을 향상시켜준다.             
![virtualDom](https://user-images.githubusercontent.com/42309919/106535182-f8b33480-6538-11eb-8751-6ce51d5bc621.jpg)        

#### 참고 Virtual DOM의 여부에 따른 차이를 보여주는 애니메이션 영상
https://www.youtube.com/watch?v=BYbgopx44vo

### 2. Vue 렌더링 성능을 개선하자
#### 함수형 컴포넌트 사용하기 
- 기존의 props만 가지고 있는 컴포넌트에 functional 선언을 통해 앱 성능을 향상시킬 수 있다.
- 일반 컴포넌트에 비해 가볍고 성능이 좋다. 
- 단순한 함수이기 때문에 렌더링 비용이 훨씬 적다. 
```
Vue.component('my-component', {
  functional: true,
  render: function (createElement, context) {
    // ...
  },
  props: {
    // ...
  }
})
```
                
#### v-once로 한 번만 렌더링하기
- 첫 렌더링 이후 변경되지 않는 요소는 v-once를 사용해 한 번만 렌더링 한 후 상태를 캐싱시켜 성능을 향상시킬 수 있다.          
**v-once를 과도하게 사용하다보면 업데이트가 안되는 상황에서 디버깅이 어려울 수 있다.**
```
<user-avatar v-once :user-obj="userObj />
```
             
#### v-if와 v-show 구분하기
- ```v-if```는 컴포넌트가 토글되는 상황에서 DOM이 제거되고 다시 만들어진다. 초기 렌더링 시 거짓인 경우 만들어지지 않음
- ```v-show```는 초기 조건과 관계 없이 렌더링되고, CSS 속성만 변경된다. 초기 렌더링이 오래 걸리더라도 지속적으로 토글되는 경우 v-show를 사용하는 것이 더욱 효율적이다.     
```
// v-if
<template v-if="display">
	<h1>Welcome</h1>
    <p>Hi there!</p>
</template>
// v-show
<h1 v-show="ok">Hello!</h1>
```

#### computed와 watch를 구분해서 사용하기
```computed``` 와 ```watch```를 구분하여 필요한 상황에 맞게 써야한다. ```watch```는 비동기 처리 로직이나 매번 호출해야 하는 상황에 쓰고, 캐싱이 필요한 상황에는 ```computed```를 써야한다. 

#### v-for에서 key를 사용해야 하는 이유
Vue에서 개별 DOM 노드들을 추적하고 기존 엘리먼트를 재사용, 재정렬하기 위해서 v-for의 각 항목들에 고유한 key 속성을 제공해야 한다. key에 대한 이상적인 값은 각 항목을 식별할 수 있는 고유한 ID 이다.

#### 코드 분할(Code Splitting)
코드를 분할하여 사용자가 원하는 시점에 파일을 로딩하여 애플리케이션 초기 로딩 속도를 개선하는 방법이다. Vue는 컴포넌트 정의를 비동기 방식으로 처리할 수 있는 방법을 제공한다.

### 3. Request 를 줄이자

#### 이미지 지연 로딩(Lazy Loading)
웹사이트에서 비동기적으로 이미지를 로딩하는 것을 의미한다. 만약 사용자가 스크롤을 페이지 하단까지 하지 않는다면, 이미지들은 페이지 하단에 배치된 이미지가 로드 되지 않는다.       

#### 이미지 스프라이트 기법(Image Sprite)
여러 이미지 파일을 일일히 불러오는 것이 아니라, 한 이미지 파일로 통합한 후 Background-Position을 이용하여 사용자에게 실제로 보여지는 위치를 다르게 하는 방법
![imageSprite](https://user-images.githubusercontent.com/42309919/106552592-f661d200-655a-11eb-8fca-e54fbc05aca0.PNG)

#### Webpack
사용자가 웹을 이용 할 때 화면이 바로 보이지 않고 로딩이 3-5초 이상 걸리면 떠나버리는 경우가 많다. 웹팩 모듈 번들러를 사용하면, 종속성(dependency)이 있는 여러개의 모듈들을 하나의 파일로 묶어준다. 이로인해 http 요청 횟수가 기존보다 줄어들게 되어 오버헤드가 상당히 감소하는 효과를 얻는다.

*** 
## 참고
#### Vue.js : 새롭게 추가된 Virtual DOM               
https://kkodu.tistory.com/1                    
#### 가상돔(Virtual DOM)?
https://www.hanumoka.net/2018/08/15/web-20180815-web-virtual-dom/
#### Vue 렌더링 성능 개선하기
https://velog.io/@kyusung/vue-rendering-optimization
#### [Vue] Vue.js 성능 개선 방법들
https://curious-notes.netlify.app/posts/vue-optimization       
#### Vue 번들 크기 줄이기
https://velog.io/@kyusung/reduce-vue-bundle-size
#### Lazy Loading & Image Sprite
https://pewww.tistory.com/10
