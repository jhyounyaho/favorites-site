추가적으로 봐야 할 것 
- poke-api return 없앴을 경우 console에 어떤게 찍히는지 확인
- 그것을 통해 해당 return 은 어떤 역할인지 확인
- promise에서의 return 확인 
- return 꼭 있어야하나, 없애도 되나? 등을 확인하면서 내용 추가하기 

return 관련 코드와 관련해 Promise 에 대해 조사

## javascript
자바스크립트는 싱글스레드 언어입니다. 싱글스레드는 말 그대로 한번에 하나의 작업만 수행할 수 있음을 의미합니다.             
자바스크립트는 비동기로 동작하기 때문에 싱글스레드에도 불구하고 동시에 많은 작업을 수행합니다. 

## 비동기 처리란? 
자바스크립트의 비동기 처리란 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는 자바스크립트의 특성의 의미합니다. 

## javascript 비동기 처리
- 콜백함수
- promise(ES6)
- Asnyc / Await(ES7)

## Promise 란?
 프로미스는 ES6에서 자바스크립트 **비동기 처리**를 다루기 위해 사용되는 객체입니다. 여기서 자바스크립트의 비동기 처리란 ‘특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성’을 의미합니다.          
 프로미스가 생성될 때 꼭 알 수 있지는 않은 값을 위한 대리자로, 비동기 연산이 종료된 이후의 결과값이나 실패 이유를 처리하기 위한 처리기를 연결할 수 있도록 합니다. 프로미스를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있습니다. 다만 최종 결과를 반환하지는 않고, 대신 프로미스를 반환해서 미래의 어떤 시점에 결과를 제공합니다.       
             
## Promise 3가지 상태 
- 대기(pending): 비동기 처리 로직이 아직 완료되지 않은 상태 
- 이행(fulfilled): 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태 
- 거부(rejected): 비동기 처리가 실패하거나 오류가 발생한 상태

### 대기(Pending)
먼저 ```new Promise()``` 메서드를 호출하면 대기(Pending) 상태가 됩니다.                                  
                         
### 이행(fulfilled)
여기서 콜백 함수의 인자인 ```resolve()```실행하면 이행(Fulfilled) 상태가 됩니다.
이행 상태가 되면 ```then()```을 이용하여 처리 결과 값을 받을 수 있습니다.
                                                            
### 거부/실패(rejected)
여기서 콜백 함수의 인자인 ```reject()```실행하면 호출 거부/실패(rejected) 상태가 됩니다.
실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 ```catch()```로 받을 수 있습니다.
                
### 프로미스 코드 예제
```
function getData() {
  return new Promise(function(resolve, reject) {
    $.get('url 주소/products/1', function(response) {
      if (response) {
        resolve(response); // 이행
      }
      reject(new Error("Request is failed")); // 거부
    });
  });
}

// 위 $.get() 호출 결과에 따라 'response' 또는 'Error' 출력
getData()
  .then(data => console.log(data)) // 이행 response 값 출력
  .catch(err => console.error(err)) // 거부 Error 출력
```
         
## 여러개의 프로미스 연결하기 (Promise Chaining)
```
getData(userInfo)
  .then(parseValue)
  .then(auth)
  .then(diaplay);
```

## 프로미스의 에러 처리 방법 
아래 2가지 방법 모두 프로미스의 reject() 메서드가 호출되어 실패 상태가 된 경우에 실행된다.             
**더 많은 예외 처리 상황을 위해 프로미스 catch()로 예외 처리 하는 것을 권장한다.**              
1.then()의 두 번째 인자로 에러를 처리하는 방법        
```
getData().then(function() {
  // ...
}, function(err) {
  console.log(err);
});
```                
2.catch()를 이용하는 방법                 
```
getData().then().catch(function(err) {
  console.log(err);
});
```
 

                   

## Promise와 api
보통 promise는 api요청을 하고 난 직후, 요청의 결과값으로 받는 경우가 많다. api요청의 경우, 서버에서 처리하고 데이터를 전달하기 까지 시간이 소요된다. 이런 시간 소요를 고려하여 이를 비동기적으로 처리하고자 api요청을 하면 요청의 결과값을 바로 주는게 아니라 Promise를 리턴한다. 요청하는 쪽에서는 이 Promise를 가지고 의사 결정을 할 수 있다. 

### Axios란?
Axios는 브라우저, Node.js를 위한 **Promise API**를 활용하는 HTTP 비동기 통신 라이브러리
```
import axios from 'axios';

var apiPromise = axios.get('https://jsonplaceholder.typicode.com/posts');

apiPromise; // // 보류(pending)

apiPromise
  .then(data => console.log(data)); // 이행
  .catch(err => console.log(err)); // 거부
```
axios.get 함수는 위에서 ```new Promise(function...)``` 형태로 만든 Promise 객체를 반환한다. api 요청이 성공했을 때와 실패했을 때의 구현은 axios가 하게 됨으로 사용자 입장에서는 ```then()```을 사용하여 Promise의 콜백을 수행하고, reject가 되었을 때를 대비하여 ```catch()``` 등을 사용하면 된다. 



## fetch / axios 

*** 
## 예제로 알아보자!
##### src/api/index.js 
```
import axios from "axios";

const config = {
    baseUrl : 'https://pokeapi.co/api/v2/pokemon',
}

// 포켓몬 정보 API
function fetchPokeInfo(id) {
    return axios.get(`${config.baseUrl}/${id}`)
}

export {
    fetchPokeInfo,
}
```
##### src/store/actions.js
```
import {
    fetchPokeInfo,
} from '../api/index.js'

export default {
    // 포켓몬 정보
    FETCH_POKE_INFO({ commit }, id) {
        fetchPokeInfo(id)
            .then(({ data }) => {
                commit('GET_POKE_INFO', data)
                return data;
            })
            .catch(err => console.log(err))
    },
}
```

***
## 참고
### [번역] async/await 를 사용하기 전에 promise를 이해하기
https://velog.io/@change/JavaScript-asyncawait%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C
### 자바스크립트 Promise 쉽게 이해하기
https://joshua1988.github.io/web-development/javascript/promise-for-beginners/
### Returning data from Axios API
https://stackoverflow.com/questions/48980380/returning-data-from-axios-api
### 자바스크립트 Promise 이해하기
https://zzossig.io/posts/javascript/what_is_the_promise/
