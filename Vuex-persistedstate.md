## LocalStorage vs sessionstorage
#### LocalStorage
#### sessionstorage

## LocalStorage vs Vuex-persist in Vue.js
#### LocalStorage
LocalStorage를 사용하면 만료 날짜 없이 브라우저 저장소에 많은 양의 데이터를 저장 할 수 있다. 즉, **페이지를 새로 고침해도 데이터가 그대로 유지** 된다.                 
Vue.js 에서 LocalStorage에 Vuex 상태를 저장 할 수 있다. 그러나 몇가지 단점이 있는데 가장 큰 것은 **수동** 으로 저장하지 않는 한 Vuex 상태의 일부만 LocalStorage에 저장되었다는 것이다. 

#### Vuex-persist in Vue.js
Vuex-persist는 지속형 저장소에 Vuex 상태를 저장하는데 사용되는 npm 패키지이다. LocalStorage or 쿠키와 매우 유사하다.
store의 mutations에서 자동으로 저장하고 영구적으로 만든다. 
Vuex-persist를 사용하여 쿠키, localStorage, sessionStorage 에 저장 할 수 있다.
**Vuex 상태를 수동 및 부분적으로 저장했다면, Vuex-persist를 사용해보자.**   



***
## 참고
#### LocalStorage vs Vuex-persist in Vue.js
https://renatello.com/localstorage-vs-vuex-persist/

#### vuex-persistedstate 덕분에 Vue.js 화면을 새로고침 해도 안전해요.
https://uxgjs.tistory.com/207
