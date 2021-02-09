## LocalStorage vs sessionstorage
#### LocalStorage
#### sessionstorage

## LocalStorage vs Vuex-persist in Vue.js
#### LocalStorage
LocalStorage를 사용하면 만료 날짜 없이 브라우저 저장소에 많은 양의 데이터를 저장 할 수 있다. 즉, **페이지를 새로 고침해도 데이터가 그대로 유지** 된다.                 
Vue.js 에서 LocalStorage에 Vuex 상태를 저장 할 수 있다. 그러나 몇가지 단점이 있는데 가장 큰 것은 **수동** 으로 저장하지 않는 한 Vuex 상태의 일부만 LocalStorage에 저장되었다는 것이다. 

#### Vuex-persist in Vue.js
vuex-persistedstate 라는 npm 이고 vuex에 저장되는 값들을 사용하는 웹브라우저의 localstorage에 저장하며, 새로고침시 그 값이 있다면 localstorage의 값을 가져와 사용한다는 원리이다.
**Vuex 상태를 수동 및 부분적으로 저장했다면, Vuex-persist를 사용해보자.**   

## vuex-persist vs vuex-persistedstate
#### 다운로드 비교 
https://www.npmtrends.com/vuex-persist-vs-vuex-persistedstate

***
## 참고
#### LocalStorage vs Vuex-persist in Vue.js
https://renatello.com/localstorage-vs-vuex-persist/

#### vuex-persistedstate 덕분에 Vue.js 화면을 새로고침 해도 안전해요.
https://uxgjs.tistory.com/207
