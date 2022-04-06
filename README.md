#### 사용 라이브러리

`react`,`redux`, `react-redux`, `redux-actions`, `redux-logger`, `redux-thunk`, `axios`, `redux-saga`

---

## 미들웨어란?

- 리덕스 미들웨어는 액션을 디스패치 했을 때 리듀서에서 이를 처리하기에 앞서 사전에 지정된 작업들을 실행
- 미들웨어는 액션과 리듀서 사이의 중간자라고 볼 수 있음
- [액션] => [미들웨어] => [리듀서] => [스토어]
- 전달받은 액션을 단순히 콘솔에 기록하거나, 전달받은 액션 정보를 기반으로 액션을 아예 취소하거나, 다른 종류의 액션을 추가로 디스패치 할 수 있음

### 미들웨어 직접 만들기

```javascript
const loggerMiddleware = (store) => (next) => (action) => {
  //미들웨어 기본 구조
};

export default loggerMiddleware;
```

- 함수를 반환하는 함수를 반환하는 함수
- 파라미터로 받아오는 store는 리덕스 스토어 인스턴스를, action은 디스패치된 액션을 가르킴
- next는 함수 형태이며 store.dispatch와 비슷한 역할을 함
  - next(action)을 호출하면 그 다음 처리해야 할 미들웨어에게 액션을 넘겨줌
  - 만약 그 다음 미들웨어가 없다면 리듀서에게 액션을 넘겨줌
  - 미들웨어에서 next를 사용하지 않으면 액션이 리듀서에 전달되지 않음
- 미들웨어는 스토어를 생성하는 과정에서 적용

### 만들어져 있는 미들웨어 사용해보기

#### redux-logger 사용하기

- `npm i redux-logger`
- 스토어를 생성하는 과정에 logger를 만들고 applyMiddleware함수의 파라미터로 전달

### 비동기 작업을 처리하는 미들웨어 사용하기

#### redux-thunk

##### Thunk란?

특정 작업을 나중에 할 수 있도록 미루기 위해 함수 형태로 감싼 것을 의미

- `redux-thunk` 라이브러리를 사용하면 thunk 함수를 만들어서 디스패치할 수 있음
- 리덕스 미들웨어가 그 함수를 전달받아 store의 dispatch와 getState를 파라미터로 넣어서 호출해줌

```javascript
const sampleThunk = () => (dispatch, getState) => {
  //현재 상태를 참조할 수 있고, 새 액션을 디스패치할 수도 있음
};
```

##### 미들웨어 적용하기

- npm i redux-thunk 설치
- 스토어를 만들 때 redux-thunk를 적용

##### Thunk 생성 함수 만들기

- 액션 생성 함수에서 일반 액션 객체를 반환하는 대신에 함수를 반환
- 기본, 성공, 실패 액션을 만들어서 설정

#### redux-saga

##### redux-saga를 이용하는 것이 유리한 상황

- 기존 요청을 취소 처리해야 할 때 (불필요한 중복 요청 방지)
- 특정 액션이 발생했을 때 다른 액션을 발생시키거나, API 요청 등 리덕스와 관계없는 코드를 실행할 때
- 웹소켓을 사용할 때
- API 요청 실패 시 재요청해야 할 때

##### 제너레이터 함수

```javascript
function* generatorFunction() {
  console.log('콘솔1');
  yield 1;
  console.log('콘솔2');
  yield 2;
  console.log('콘솔3');
  yield 3;
  return 4;
}
```

- 제너레이터 함수를 만들 때는 function\* 키워드를 사용
- 함수를 작성한 뒤에는 다음 코드를 사용해 제너레이터를 생성
  - `const generator = generatorFunction()`
- 제너레이터 함수를 호출 했을 때 반환되는 객체를 제너레이터라고 부름

```javascript
generator.next();
//콘솔1
//1
generator.next();
//콘솔2
//2
generator.next();
//콘솔3
//3
generator.next();
//4
generator.next();
//undefined
```

- 제너레이터가 처음 만들어지면 함수의 흐름은 멈춰있는 상태
- next()가 호출되면 다음 yield가 있는 곳까지 호출하고 다시 함수가 멈춤
- 제너레이터 함수를 사용하면 함수를 도중에 멈출 수 있고, 순차적으로 여러 값을 반환시킬 수 있음
- next 함수에 파라미터를 넣으면 제너레이터 함수에서 yield를 사용하여 해당값을 조회할 수 있음

```javascript
function* sumGenerator() {
  console.log('sumGenerator가 만들어졌습니다.');
  let a = yield;
  let b = yield;
  yield a + b;
}

const sum = sumGenerator();
sum.next();
//sumGenerator가 만들어졌습니다.
//undefined
sum.next(1);
//undefined
sum.next(2);
//3
sum.next();
//undefined
```

##### redux-saga는?

- 제너레이터 함수 문법을 기반으로 비동기 작업을 관리해줌
- redux-saga는 디스패치하는 액션을 모니터링해서 그에 따라 필요한 작업을 따로 수행할 수 있는 미들웨어
