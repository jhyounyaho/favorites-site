# Vuex 란?  
- Vuex는 Vue.js 애플리케이션에 대한 상태 관리 패턴 + 라이브러리     
- 어플리케이션 : 저장소 = 1 : 1     
- 무수히 많은 컴포넌트의 데이터를 관리하기 위한 상태 관리 패턴이자 라이브러리 React의 Flux 패턴에서 기인함.

## Vuex로 해결할 수 있는 문제
- MVC 패턴에서 발생하는 구조적 오류
- 컴포넌트 간 데이터 전달 명시
- 여러 개의 컴포넌트에서 같은 데이터를 업데이트 할 때 동기화 문제
          
## Vuex 기술 요소
- state: 여러 컴포넌트에 공유되는 데이터 data       
- getters: 연산된 state 값을 접근하는 속성 computed     
- mutations: state 값을 변경하는 이벤트 로직 / 메서드 methods        
- actions: 비동기 처리 로직을 선언하는 메서드 async methods         
       
## Getters VS Mutations
- Getters: computed에 등록         
- Mutations: methods에 등록, 인자를 받아 Vuex에 넘겨줄 수 있다.          
        
## Mutations VS Actions 
- Mutations: 동기적 로직 정의                
- Actions: 비동기적 로직 정의        

## 왜 처리 로직의 성격에 따라 Mutations와 Actions 로 나눠 등록해야 할까?
Mutations의 역할 자체가 State 관리에 주안점을 두고 있다. 상태관리 자체가 한 데이터에 대해 여러 개의 컴포넌트가 관여하는 것을 효율적으로 관리하기 위함이다. Mutations에 비동기 처리 로직들이 포함되면 같은 값에 대해 여러 개의 컴포넌트에서 변경을 요청했을때, 그 변경 순서 파악이 어렵기 때문이다.      
                  
## Vuex 구조
Vue Component -> 비동기 로직(Actions) -> 동기 로직(Mutations) -> 상태(State)                          
![vuex](https://user-images.githubusercontent.com/42309919/106402866-09977380-646f-11eb-8f02-bdb718d5fa39.png)

## Helper 
Store에 있는 아래 4가지 속성들을 간편하게 코딩하는 방법
- state -> mapState                
- getters -> mapGetters
- mutations -> mapMutations
- actions -> mapActions

## Vuex 설치 
npm install vuex --save                       

***
# 예시코드와 함께 Vuex 알아보기            
                                               
## store/index.js 
store 모듈화 
```
import Vue from 'vue'
import Vuex from 'vuex'
import state from './state.js'
import getters from './getters.js'
import mutations from './mutations.js'
import actions from './actions.js'

Vue.use(Vuex)

const store = new Vuex.Store({
  state,
  getters,
  mutations,
  actions,     
})

const { token } = localStorage
store.commit('LOGIN', token)

export default store
```

## api/index.js 
```
export const board = {
  fetch() {
    return request('get', '/boards')
  },
  create(title) {
    return request('post', '/boards', {title})
  }
}
```

## 상태(State)
- 여러 컴포넌트 간에 공유 할 데이터
- Vue의 ```data```와 같은 역할            
                        
#### store/state.js
```
const state = {
  token: null,
  isAddBoard: false,
  boards: [],
}

export default state;
```

#### 1. Vuex 상태를 Vue 컴포넌트에서 접근
```
  computed: {
    count () {
      return this.$store.state.token
    }
  }
```

#### 2. mapState 헬퍼 접근     
- Vuex에 선언한 state 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼           
store/state.js 에 저장된 state 사용
```
<script>
import {mapState} from 'vuex
export default {
  computed: {
    ...mapState({
      isAddBoard: 'isAddBoard',  
      boards: 'boards'
    })
  }
}
</script>
```

## getters
- 연산된 state 값을 접근하는 속성 
- vue의 ```computed()``` 처럼 미리 연산된 값을 접근하는 속성 

#### store/getters.js
```
const getters = {
  isAuth(state) {
    return !!state.token
  },
}

export default getters
```

#### 속성 유형 접근 
```
{{ this.$store.getters.isAuth }}
```

#### mapGetters 헬퍼 접근         
- Vuex에 선언한 getters 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼        
```
<script>
import {mapGetters} from 'vuex'
export default {
  computed: {
    ...mapGetters([
      'isAuth'
    ]),
  },
}
</script>
```

## 변이(Mutaions)
- state 값을 변경하는 **유일한 방법** 이벤트 로직 / 메서드 methods
- 변이는 무조건 **동기적**이어야 한다. **Mutations의 성격상 안에 정의한 로직들이 순차적으로 일어나야 각 컴포넌ㅌ의 반영 여부를 제대로 추적할 수가 있기 때문이다.** 
- 첫번째 인자값 상태(state)
- 두번째 인자값(payload) 전달값 (2개 이상일 경우 {a: 'a', b: 'b'} 형태) 

#### state는 왜 직접 변경하지 않고 mutations로 변경할까?
- 여러 개의 컴포넌트에서 state 값을 변경하는 경우 **특정 시점에 어떤 컴포넌트에서 해당 state를 접근하여 변경했는지 추적하기 어렵다.** 따라서, mutations를 통해 state 값을 변경해줘야한다.          
                       
#### state 변경 잘못된 안티패턴                        
```
methods: {
  this.$store.state.isAddBoard = !this.$store.state.isAddBoard
}
```

#### store/mutations.js
```
import { setAuthInHeader } from '../api'

const mutations = {
  SET_IS_ADD_BOARD (state, toggle) {
    state.isAddBoard = toggle
  },
}

export default mutations
```

#### 1. this.$store.commit 사용 
```
<template>
  <a class="new-board-btn" href="" @click.prevent="addBoard">use helper</a>
</template>
<script>
export default {
  methods: {
    addBoard() {
      this.$store.commit('SET_IS_ADD_BOARD', false)
      this.$store.commit('SET_BOARD', {a: 1, b: 2})
      this.$store.commit('PRINT_NUM')
    }
  }
}
</script>
``` 

#### 2. mapMutations 사용        
- Vuex에 선언한 mutations 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼   
```
<template>
  <a class="new-board-btn" href="" @click.prevent="SET_IS_ADD_BOARD(true)">use helper</a>
</template>
<script>
export default {
  methods: {
    ...mapMutations([
      'SET_IS_ADD_BOARD',
      'SET_BOARD',
      'PRINT_NUM',
    ]),
    addBoard() {
      this.SET_IS_ADD_BOARD(false)
      this.SET_BOARD({a: 1, b: 2})
      this.PRINT_NUM()
    }
  }
}
</script>
```

## 액션(actions)
- **비동기 처리 로직**을 선언하는 메서드 
- 변이(Mutaions)와 유사하다.
- Promise, ES6 async, setTimeout(), 서버와의 http 통신 처리같이 결과를 받아올 타이밍이 예측되지 않는 **비동기 처리**는 모두 actions에 선언 
- ```store.dispatch```가 Promise를 반환한다.
- state를 변경하지 않고, mutations의 함수를 호출한다.

#### 비동기 처리 로직은 왜 actiosns에 선언해야 할까?
- 언제 어느 컴포넌트에서 해당 state를 호출하고, 변경했는지 확인하기가 어렵다. 
- 여러개의 컴포넌트에서 mutaions를 시간 차를 두고 state를 변경하는 경우, state 값의 변화를 추적하기 어렵기 때문에 mutations 속성에는 동기 처리 로직만 넣어야 한다.
![actions](https://user-images.githubusercontent.com/42309919/106404022-942ea180-6474-11eb-8a39-5f24562e8ea8.PNG)

#### store/actions.js
```
import { board } from '../api'

const actions = {
  ADD_BOARD (_, title) {
    return board.create(title)
      .then(({item}) => item.id)
  },
}

export default actions;
```

#### 1. this.$store.dispatch 사용 
컴포넌트에서 액션을 디스패치 
```
<script>
export default {
  methods: {
    addBoard() {
      this.$emit('submit')
      this.$store.dispatch('ADD_BOARD', {title: this.input})
    }
  }
}
</script>
```

#### 2. mapActions 사용        
- Vuex에 선언한 actions 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼      
컴포넌트 메소드를 store.dispatch 호출에 매핑 
```
<script>
import {mapActions} from 'vuex
export default {
  methods: {
    ...mapActions([
      'ADD_BOARD',
    ]),
    addBoard() {
      this.ADD_BOARD({title: this.input})
    }
  }
}
</script>
```
***
# Q&A
### 아래와 같은 로직은 mutaions vs actions 중 어디에 선언되어야 할까?                  
로직 === 비동기                          
? actions                       
: mutations                           
```
    // GET 스크랩 포켓몬 리스트
    GET_SCRAP_POKE_LIST(state, { offset, limit }) {
        const scrapList = localStorage.scraplist ? JSON.parse(localStorage.scraplist) : [];
        const sliceScrapList = scrapList.slice(offset, offset + limit)
        if (sliceScrapList.length > 0) {
            state.list = [...state.list, ...sliceScrapList];
        }
    },
```
=> mutations은 state를 변경해주는 로직을 선언해야하는 곳이다.                
따라서, 함수명을 GET 이 아닌 SET으로 네이밍 되었어야 했다. 수정해서 반영 예정       
*** 
# 참고
### Vue.js 공홈
https://vuex.vuejs.org/kr/             

### 캡틴판교 Vuex 시작하기 1 - Vuex와 State
https://joshua1988.github.io/web-development/vuejs/vuex-start/           
         
### 캡틴판교 Vuex 시작하기 2 - Getters 와 Mutations
https://joshua1988.github.io/web-development/vuejs/vuex-getters-mutations/          

### 캡틴판교 액션 
https://vuex.vuejs.org/kr/guide/actions.html        
                           
