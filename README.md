#### 사용 라이브러리

`react`,`redux`, `react-redux`, `redux-actions`, `redux-logger`, `redux-thunk`, `axios`

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
