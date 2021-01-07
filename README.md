## Reudx
리액트 생태계에서 가장 사용률이 높은 상태관리 라이브러리이다.
리덕스를 사용하면 지금까지 만든 컴포넌트들의 상태 관련 로직들을 다른 파일들로 분리시켜서
 더욱 효율적으로 관리 할 수 있으며 글로벌 상태 관리도 손쉽게 할 수 있다.


### 리덕스를 사용하는 것 과 Context API를 사용하는 것의 차이
1. 미들웨어
리덕스에는 미들웨어(Middleware)라는 개념이 존재한다. 
리덕스로 상태 관리를 할 때에는 우리가  `useReducer`를 사용할 때 접했던 개념인 리듀서 함수를 사용한다. 
리덕스의 미들웨어를 사용하면 액션 객체가 리듀서에서 처리되기 전에 우리가 원하는 작업들을 수행 할 수 있다. 예를 들면 아래와 같다.

-   특정 조건에 따라 액션이 무시되게 만들 수 있습니다.
-   액션을 콘솔에 출력하거나, 서버쪽에 로깅을 할 수 있습니다.
-   액션이 디스패치 됐을 때 이를 수정해서 리듀서에게 전달되도록 할 수 있습니다.
-   특정 액션이 발생했을 때 이에 기반하여 다른 액션이 발생되도록 할 수 있습니다.
-   특정 액션이 발생했을 때 특정 자바스크립트 함수를 실행시킬 수 있습니다.

미들웨어는 주로 비동기 작업을 처리 할 때 많이 사용됩니다.

`useReducer`  Hook 에서도  [외부 라이브러리](https://github.com/streamich/react-use/blob/master/docs/createReducer.md)를 사용하면 미들웨어를 사용 할 수도 있습니다. 다만, 자주 사용되는 방식은 아니다.

2. 유용한 함수와, Hooks

이전에 Context API 와  `useReducer`  를 사용 할 때에는 Context 도 새로 만들고, Context 의 Provider 설정도 하고 각 Context 를 편하게 사용하기 위해 전용 커스텀 Hook 을 따로 만들어서 사용하기도 했는데, 리덕스에서는 이와 비슷한 작업을 편리하게 해줄 수 있는 여러 기능들이 존재한다.

`connect` 함수를 사용하면 리덕스의 상태 또는 액션 생성 함수를 컴포넌트의 props 로 받아올 수 있으며,  `useSelector`,  `useDispatch`,  `useStore`  과 같은  [Hooks](https://react-redux.js.org/api/hooks)를 사용하면 손쉽게 상태를 조회하거나 액션을 디스패치 할 수도 있죠.

`connect`  함수와  `useSelector`  함수에는 내부적으로 최적화가 잘 이루어져있어서 실제 상태가 바뀔때만 컴포넌트가 리렌더링된다. 반면에 Context API를 사용할 때에는 그러한 최적화가 자동으로 이루어져있지 않기 때문에 Context 가 지니고 있는 상태가 바뀌면 해당 Context 의 Provider 내부 컴포넌트들이 모두 리렌더링 되지요.


## 리덕스는 언제 사용할까?
리덕스를 프로젝트에 써야 할지 말지 고민 할 때에는 다음 사항들을 고려해보자.

1.  프로젝트의 규모가 큰가?
    -   Yes: 리덕스
    -   No: Context API

2.  비동기 작업을 자주 하게 되는가?
    -   Yes: 리덕스
    -   No: Context API

3.  리덕스를 배워보니까 사용하는게 편한가?
    -   Yes: 리덕스
    -   No: Context API 또는 MobX

여기서 3번, 매우 중요하다. 아무리 리덕스가 좋은 라이브러리라 해도 여러분의 취향에 맞지 않고 어렵게만 느껴지고 맘에 들지 않는다면 사용하지 않는 것이 좋다. 

## 리덕스의 3가지 규칙
1. 하나의 애플리케이션 안에는 하나의 스토어가 있다.
하나의 애플리케이션에선 단 한개의 스토어를 만들어서 사용한다. 

2. 상태는 읽기전용이다.
리액트에서 state 를 업데이트 해야 할 때, setState 를 사용하고, 배열을 업데이트 해야 할 때는 배열 자체에 push 를 직접 하지 않고, 
concat 같은 함수를 사용하여 기존의 배열은 수정하지 않고 새로운 배열을 만들어서 교체하는 방식으로 업데이트한다. 
엄청 깊은 구조로 되어있는 객체를 업데이트를 할 때도 마찬가지로, 기존의 객체는 건드리지 않고 Object.assign 을 사용하거나 spread 연산자 (...) 를 사용하여 업데이트한다.

리덕스에서도 마찬가지이다.
기존의 상태는 건들이지 않고 새로운 상태를 생성하여 업데이트 해주는 방식으로 해주면, 나중에 개발자 도구를 통해서 뒤로 돌릴 수도 있고 다시 앞으로 돌릴 수도 있다.

리덕스에서 불변성을 유지해야 하는 이유는 내부적으로 데이터가 변경 되는 것을 감지하기 위하여 shallow equality 검사를 하기 때문이다. 
이를 통하여 객체의 변화를 감지할 때 객체의 깊숙한 안쪽까지 비교를 하는 것이 아니라 **겉핥기 식으로 비교를 하여 좋은 성능을 유지**할 수 있는 것이죠.


출처) https://react.vlpt.us/redux/
