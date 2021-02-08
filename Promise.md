# return 관련 코드와 관련해 Promise 에 대해 조사

## Promise 란?
 프로미스는 ES6에서 자바스크립트 **비동기 처리**를 다루기 위해 사용되는 객체다. 여기서 자바스크립트의 비동기 처리란 ‘특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성’을 의미한다.          
 프로미스가 생성될 때 꼭 알 수 있지는 않은 값을 위한 대리자로, 비동기 연산이 종료된 이후의 결과값이나 실패 이유를 처리하기 위한 처리기를 연결할 수 있도록 한다. 프로미스를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있다. 다만 최종 결과를 반환하지는 않고, 대신 프로미스를 반환해서 미래의 어떤 시점에 결과를 제공한다.       
             
## Promise 3가지 상태 
- 대기(pending): 비동기 처리 로직이 아직 완료되지 않은 상태 
- 이행(fulfilled): 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태 
- 거부(rejected): 비동기 처리가 실패하거나 오류가 발생한 상태

### 대기(Pending)
먼저 ```new Promise()``` 메서드를 호출하면 대기(Pending) 상태가 된다.                                  
                         
### 이행(fulfilled)
여기서 콜백 함수의 인자인 ```resolve()```실행하면 이행(Fulfilled) 상태가 된다.
이행 상태가 되면 ```then()```을 이용하여 처리 결과 값을 받을 수 있다.
                                                            
### 거부/실패(rejected)
여기서 콜백 함수의 인자인 ```reject()```실행하면 호출 거부/실패(rejected) 상태가 된다.
실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 ```catch()```로 받을 수 있다.
                
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
return getData()
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
return getData().then(function() {
  // ...
}, function(err) {
  throw err
});
```                
2.catch()를 이용하는 방법                 
```
return getData().then().catch(function(err) {
  throw err
});
```
 
## Promise와 api
보통 promise는 api요청을 하고 난 직후, 요청의 결과값으로 받는 경우가 많다. api요청의 경우, 서버에서 처리하고 데이터를 전달하기 까지 시간이 소요된다. 이런 시간 소요를 고려하여 이를 비동기적으로 처리하고자 api요청을 하면 요청의 결과값을 바로 주는게 아니라 Promise를 리턴한다. 요청하는 쪽에서는 이 Promise를 가지고 의사 결정을 할 수 있다. 

## Vuex에서 axios 사용하기 
![axios+vuex](https://user-images.githubusercontent.com/42309919/107175877-459f7b00-6a11-11eb-93a7-bccded871264.png)

### Axios란?
Axios는 브라우저, Node.js를 위한 **Promise API**를 활용하는 HTTP 비동기 통신 라이브러리로서 
Vuex actions는 promise를 리턴 할 수 있다. 

#### Vuex Action
```
import axios from 'axios';

actions: {
    myAction() {
        // Returns a promise object to the calling function (ex. 'dispatch()')
        return axios.get('https://jsonplaceholder.typicode.com/posts')  
        .then(response => {
            // Axios promise is resolved 
            return response
        }).catch(error => {
            // Axios promise is rejected  
            return Promise.reject(error)   
        })
    }
}
```

#### Dispatch
```
store.dispatch('myAction')
.then(response => {
    // Axios returned Promise.resolve(response)
    console.log(response)
}).catch(error => {
    // Axios returned Promise.reject(error)
    // For example, the desired API endpoint was not found (i.e. 404 response code) 
    console.log(error)
}
```

axios.get 함수는 위에서 ```new Promise(function...)``` 형태로 만든 **Promise 객체를 반환(return)** 한다.                               
api 요청이 성공했을 때와 실패했을 때의 구현은 axios가 하게 됨으로 사용자 입장에서는 반환된 Promise객체를 가지고 ```then()```을 사용하여 Promise의 콜백을 수행하고, reject가 되었을 때를 대비하여 ```catch()``` 등을 사용하면 된다.              

#### Using Axios in Vuex Actions
Vuex actions에서 Axios를 사용법 예제       
https://mclassdesigns.gitbook.io/js-journal/


*** 
## 예제로 알아보자! 
Vuex actions에서 Axios를 사용하여 비동기 작업 할 경우         
                             
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
        return fetchPokeInfo(id) // 1. promise를 return해야 할까?
            .then(res => {
                // 2. then, catch 내부에 return이 꼭 있어야 할까? 
                // 3. 성공시 return하게 된다면 디스패치 호출에서는 어디서 응답을 반환할까? 
                return res
                res.status === 200 && commit('SET_POKE_INFO', res.data)
             }) 
            .catch(err => { 
                // 4. 실패시 return err를 하게 된다면 어떻게 될까? 
                console.log(err)
                throw err 
            })
    },
}
```
#### components/PokeSearch.vue 
```
     this.FETCH_POKE_INFO(this.pokeId)
          .catch(err => { 
            if (err.response.status === 404) {
              this.FailMessage = '해당 숫자에 맞는 포켓몬이 없습니다.';
            } else {
              this.FailMessage = '잠시 후 다시 시도해 주세요.';
            }
            this.showModal = true
            return false
          })
```
                                 
## 1. promise를 return해야 할까?  
#### return 해 줄 경우
에러 핸들링에서 필요한 내용이 리턴된 것을 확인 할 수 있다.         
![return_catch](https://user-images.githubusercontent.com/42309919/107148494-bce3f900-6996-11eb-97b0-52060aa35931.PNG)

#### return 안 해 줄 경우 
에러 핸들링에서 필요한 내용이 리턴되지 않은 것을 확인 할 수 있다. 
![noreturn_catch](https://user-images.githubusercontent.com/42309919/107148492-bc4b6280-6996-11eb-92a7-1432f41b1a3b.PNG)  
                
#### 결론
axios는 promise 기반이다. **따라서, Vuex actions내에서 return으로 axios 요청을하여 promise객체를 반환해야한다.**

## 2. then, catch 내부에 return이 있어야 할까? 
#### 현재 상황 
FETCH_POKE_INFO 에서 리턴된 err를 가지고 유효성 체크를 하고있다.(ex 404일 경우 경고창 노출)        
따라서 return 으로 promise를 반환하여 err를 전달해야한다.  

then 에서는 FETCH_POKE_INFO를 호출한 컴포넌트에서 성공시 추가 작업을 해주고 있지 않기 때문에 return 하고 있지 않다.  
catch 에서는 FETCH_POKE_INFO를 호출한 컴포넌트에서 실패시 에러핸들링을 해주고 있기 때문에 throw err를 해주고 있다. 
#### return || throw 해 줄 경우
정상적으로 체크되는 것을 확인 할 수 있다.                 
err.response.status === 404 를 체크하여 경고창을 노출 하였다.               
![return_err](https://user-images.githubusercontent.com/42309919/107149715-852c7f80-699d-11eb-9af7-5c608284e782.PNG)                    
![return_catch2](https://user-images.githubusercontent.com/42309919/107148607-7cd14600-6997-11eb-8b79-dde7a7216af2.PNG)                              
#### return 안 해 줄 경우
데이터가 넘어오지 않기 때문에 console.log가 찍히지 않는다.                             
#### 결론        
Axios 핸들러에서 디스패치 호출로 response, error를 반환하여 정의되지 않은 문제를 해결할 수 있다.                                       
비동기 성공, 실패시 리턴된 값을 변수에 저장하거나 UI에 뿌려줘야 할때, 유효성 체크 등 추가 작업이 필요할 경우 return, throw 등을 해주자!           

## 3. 성공시 return하게 된다면 디스패치 호출에서는 어디서 응답을 반환할까?
응답 성공시 디스패치 호출의 then() 핸들러에 대한 응답을 반환한다.   

## 4. 실패시 return err를 하게 된다면 어떻게 될까? 
#### return err 로 err 넘겨 줄 경우          
디스패치 호출의 catch() 핸들러에서 오류를 반환하지 않는다.          
대신 response, error 모두 then()에 의해 처리 된다.     

#### 디스패치 호출의 catch()에서 오류를 반환하고 싶은데 어떻게 변경 해줘야 할까?
```return Promise.reject(error) or throw err ```                      
로 변경해주면 디스패치 호출의 catch()에서 오류를 반환 할 수 있다. 
- Promise.reject(reason): 주어진 이유(reason)로 거부된 Promise 객체를 반환하여 catch로 전달된다. return 처럼 제어 흐름을 종료하지 않는다.                      
- throw reaseon: 사용자 정의 예외를 던질 수 있다. catch로 전달된다. return 처럼 제어 흐름을 종료한다. 
```          
actions: {
    myAction() {
        return axios.get('/api').then(response => {
            return Promise.resolve(response)
        }).catch(error => {
            return Promise.reject(error)
            or 
            throw err
        })
    }
}
```
#### 왜 이런 현상이 나타날까
Axios 의 err가 catch()가 아닌 then() 메서드에 의해 처리되는 이유는 Axios의 promise는 resolve 되었다고 믿고 있기 때문이다.                      
이건 javascript Promise의 기본 동작이며 Axios의 전용 기능이 아니다. 

                                
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
#### Returning an Axios Promise from function
https://stackoverflow.com/questions/43463989/returning-an-axios-promise-from-function
#### Vuex dispatch action - Is this correct?
https://forum.vuejs.org/t/vuex-dispatch-action-is-this-correct/24824    
