---
layout: post
title: (React) 모던 리액트 Deep Dive 3장
categories: Frontend
published: true
---

<br>

## 목차

1. [리액트의 모든 훅 파헤치기](#리액트의-모든-훅-파헤치기)
2. [사용자 정의 훅과 고차 컴포넌트](#사용자-정의-훅과-고차-컴포넌트)

<br>

## 리액트의 모든 훅 파헤치기

### 1. useState

useState는 컴포넌트 내부에서 상태를 정의하고, 이 상태를 관리할 수 있게 해주는 훅이다. 인수로 초기값을 넘겨주는데 아무런 값을 넘겨주지 않으면 초기값은 undefined다.

매번 실행되는 함수형 컴포넌트 환경에서 state의 값을 유지하고 사용할 수 있는 이유는 클로저를 활용했기 때문이다. 클로저를 사용함으로써 외부에 해당 값을 노출시키지 않고 컴포넌트가 매번 실행되더라도 useState에서 이전 값을 꺼내올 수 있는 것이다.

- **게으른 초기화 (lazy initialization)** <br>

  ```javascript
  // 일반적인 useState 사용
  // 원시값을 넣거나 바로 값을 집어 넣는 경우가 많다.
  const [state, setState] = useState(
    Number.parseInt(window.localStorage.getItem("key"))
  );

  // lazy initialization
  // 함수를 실행하여 값을 반환한다.
  const [state, setState] = useState(() => {
    Number.parseInt(window.localStorage.getItem("key"));
  });
  ```

  게으른 초기화는 초기값이 복잡하거나 무거운 연산을 포함할 때 사용한다. 리액트에서는 렌더링이 실행될 때마다 함수형 컴포넌트의 함수들이 다시 실행되고 useState값도 재실행된다는 점이 있지만 게으른 초기화에서는 최초의 state값을 넣을 때만 실행되고 이후에는 실행되지 않는다.

  localStorage나 seesionStorage에 대한 접근, map, filter, find같은 배열 접근, 초기값 계산을 위해 함수 호출이 필요하거나 무거운 연산을 포함해 실행 비용이 많이 드는 경우에 사용하는 것이 좋다.

### 2. useEffect

useEffect는 컴포넌트들의 여러 값들을 활용해 동기적으로 부수 효과를 만드는 메커니즘으로, 생명주기 메서드를 대체하기 위한 훅이 아니다. <br>
렌더링할 떄마다 의존성에 있는 값을 보면서 이 의존성의 값이 이전과 다른 게 하나라도 있다면 부수 효과를 실행하는 평범한 함수이다. useEffect는 state와 props의 변화 속에 일어나는 렌더링 과정에서 실행되는 부수 효과 함수라 볼 수 있다.

- **클린업 함수의 목적** <br>
  클린업 함수는 이벤트를 등록하고 지울 때 사용한다.

  ```javascript
  // 최초 실행
  useEffect(() => {
    function addMouseEvent() {
      console.log(1);
    }

    window.addEventListener("click", addMouseEvent);

    // 클린업 함수
    return () => {
      window.removeEventListener("click", addMouseEvent);
    };
  }, [state]);

  // ...

  useEffect(() => {
    function addMouseEvent() {
      console.log(2);
    }

    window.addEventListener("click", addMouseEvent);

    // 클린업 함수
    return () => {
      window.removeEventListener("click", addMouseEvent);
    };
  }, [state]);
  ```

  useEffect는 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 클린업 함수를 실행한 뒤에 콜백을 실행한다. 이전에 등록했던 이벤트 핸들러를 삭제하는 코드를 클린업 함수에 추가하는 것이다.

  클린업 함수는 언마운트 개념과는 차이가 있다. <br>
  언마운트는 클래스형 컴포넌트 용어로 특정 컴포넌트가 DOM에서 사라진다는 것을 의미하고, 클린업 함수는 함수형 컴포넌트가 리렌더링됐을 때 의존성 변화가 있을 당시 이전의 값을 기준으로 실행되는, 이전 상태를 청소해 주는 개념이다.

- **의존성 배열** <br>
  보통 빈 배열이거나, 값을 넘기지 않거나 원하는 값을 넣어줄 수있다. 만약 아무런 값도 넘기지 않는다면 렌더링이 발생할 때마다 실행된다.

  ```javascript
  function Component() {
    console.log("렌더링");
  }

  function Component() {
    useEffect(() => {
      console.log("렌더링");
    });
  }
  ```

  렌더링이 발생할 때마다 실행된다고 하지만, 두 코드에는 명확한 차이점이 있다.

  - useEffect는 서버 사이드 렌더링 관점에서 클라이언트 사이드에서 실행되는 것을 보장해준다.
  - useEffect는 컴포넌트의 렌더링이 완료된 이후에 실행된다. 반면 직접 실행은 컴포넌트가 렌더링 되는 도중에 실행된다. 따라서 컴포넌트의 반환을 지연시킬 수도 있고 렌더링을 방해해 성능에 악영향을 미칠 수 있다.

- **useEffect를 사용할 때 주의할 점** <br>

  1. eslint-disable-line react-hooks/exhaustive-deps 주석은 최대한 자제할 것

     ```javascript
     useEffect(() => {
       console.log(props);
     }, []); // eslint-disable-line react-hooks/exhaustive-deps
     ```

     useEffect는 반드시 의존성 배열로 전달한 값의 변경에 의해 실행되야 하는 훅이다. 의존성 배열을 넘기지 않은 채 콜백 함수에서 다른 특정 값을 사용하는 것은 <u>컴포넌트의 state, props와 같은 어떤 값의 변경과 useEffect의 부수 효과가 별개로 작동하게 된다는 것</u>이다.

     ```javascript
     function Component({ props }: { props: string }) {
       useEffect(() => {
         logging(props);
       }, []); // eslint-disable-line react-hooks/exhaustive-deps
     }
     ```

     위 코드는 컴포넌트가 최초로 렌더링됐을 때만 `logging()` 함수를 실행하고 싶었을 것이다. 하지만 위 코드는 버그의 위험성이 있다. props가 변하더라도 useEffect는 실행되지 않고 useEffect의 흐름과 props 흐름이 맞지 않게 된다.

     나은 방법은 부모 컴포넌트에서 component가 렌더링되는 시점을 결정하고 이에 맞게 props 값을 넘겨준다면 useEffect의 주석을 제거해도 동일한 결과를 만들 수 있고 Component의 부수 효과 흐름을 거스르지 않을 수 있다.

  2. useEffect의 콜백 함수에 함수명을 부여할 것 <br>
     많은 코드에서 useEffect의 첫 번째 인수로 익명 함수를 넘겨준다. 하지만 코드가 복잡하고 많아질수록 무슨 일을 하는 useEffect 코드인지 파악하기 어렵다. 따라서 적절한 이름을 붙이면 해당 useEffect의 목적을 파악하기 쉽다.

  3. 거대한 useEffect를 만들지 말 것 <br>
     useEffect는 컴포넌트의 렌더링 이후에 실행되기 때문에 렌더링 작업에는 영향을 적게 미치지만 자바스크립트 실행 성능에도 영향을 미친다는 것은 변함없다. 따라서 가능한 간결하고 가볍게 유지하는 것이 좋고, 큰 useEffect가 필요하다면 여러개의 useEffect로 분리하는 것이 좋다.

     또 만약 의존성 배열에 여러 변수를 넣어야 한다면 최대한 useCallback, useMemo 등으로 사전에 정제한 내용들만 useEffect에 담아주는 것이 좋다.

  4. 불필요한 외부 함수를 만들지 말 것 (206p 예제 참고) <br>
     useEffect안에서 API를 호출하려고 할 때 API 호출하는 함수를 밖에다 선언하여 불필요한 코드가 많아지고 가독성이 떨어지는 경우가 있다. 외부에 있던 함수를 useEffect로 가져오면 불필요한 의존성 배열도 줄어들 수 있다.

### 3. useMemo

비용이 큰 연산에 대한 결과를 저장(메모이제이션)하고, 저장된 값을 반환하는 훅이다. <br>
렌더링 시 의존성 배열의 값이 변경되지 않으면 함수를 재실행하지 않고 이전에 기억해 둔 값을 반환한다. 값이 변경되면 첫 번째 인수의 함수를 실행한 뒤 값을 반환하고 그 값을 다시 기억해둔다.

컴포넌트로도 메모이제이션이 가능하다. 컴포넌트는 `React.memo`를 쓰는 것이 더 좋다.

### 4. useCallback

useCallback은 인수로 넘겨받은 콜백 자체를 기억한다. 즉 특정 함수를 새로 만들지 않고 다시 재사용한다는 것이다. useMemo와 마찬가지로 의존성 배열의 값이 변경되지 않는 한 함수를 재생성하지 않는다. <br>
함수의 재생성을 막아 불필요한 리소스 또는 리렌더링을 방지하고 싶을 때 사용하면 좋다.

useMemo와 useCallback의 차이가 크게 없어보이지만, 유일한 차이는 메모이제이션을 하는 대상이 함수인가, 변수인가일 뿐이다. useMemo는 값 자체를 메모이제이션하는 용도이므로 반환문으로 함수 선언문을 반환해야한다.

### 5. useRef

useRef는 useState와 동일하게 컴포넌트 내부에서 렌더링이 일어나도 변경 가능한 상태값을 저장한다는 공통점이 있다.

그럼 고정된 값을 관리하기 위해 useRef를 사용한다면 함수 외부에 값을 선언해도 충분하지 않을까? <br>
하지만 이 방식은 몇 가지 단점이 있다.

- 컴포넌트가 실행되어 렌더링되지 않았음에도 값이 기본적으로 존재한다. 이는 메모리에 불필요한 값을 갖게 하는 악영향을 미친다.
- 컴포넌트가 여러 번 생성된다면 각 컴포넌트에서 가리키는 값이 모두 동일하다. 컴포넌트는 인스턴스 하나당 하나의 값을 필요로 하는 것이 일반적이다.

하지만 useRef는 컴포넌트가 렌더링될 때만 생성되고, 컴포넌트 인스턴스가 여러개라도 각각 별개의 값을 바라본다. <br>
단, useRef의 최초 기본값은 return 문에 정의한 DOM이 아닌 `useRef()`로 넘겨받은 인수라는 점을 명심해야 한다. useRef가 선언된 당시는 아직 컴포넌트가 렌더링되기 전이라 return으로 컴포넌트의 DOM이 반환되기 전이므로 undefined 상태이다.

### 6. useContext

리액트는 부모와 자식으로 이뤄진 트리구조로 단방향 데이터 흐름이 특징이다.

```javascript
<A props={something}>
  <B props={something}>
    <C props={something}>
      <D props={something} />
    </C>
  </B>
</A>
```

A에서 D까지 props를 계속해서 넘겨주는 기법을 `props drilling (props 내려주기)` 라 한다. 해당 값을 사용하지 않는 컴포넌트에서도 단순히 값을 전달하기 위한 props가 있어야 하고, 사용하는 쪽도 props가 있는지 확인해야하는 번거로움이 있다.

이런 props를 극복하기 위해 context가 등장했다.

- **함수형 컴포넌트에서도 사용할 수 있는 useContenxt hook** <br>
  useContext는 상위 컴포넌트에서 만들어진 Context를 컴포넌트에서 사용할 수 있도록 만들어진 훅이다. 상위 컴포넌트 어딘가에 선언된 `<Context.Provider />`에서 제공하는 값을 사용할 수 있다. 만약 여러개 Provider가 있다면 가장 가까운 Provider의 값을 가져온다.

  다수의 Provider와 useContext를 사용할 때, 타입스크립트를 사용한다면 별도 함수로 감싸서 사용하는 것이 좋다. 타입 추론에도 유용하고, 상위 Provider가 없는 경우 사전에 에러를 잡을 수 있다. (221p 예제 참고)

- **useContext 사용 시 주의할 점** <br>
  - useContext를 함수형 컴포넌트 내부에서 사용할 때는 컴포넌트 재활용이 어려워진다. (Provider에 의존성을 가지고 있는 셈이 되므로) 그렇다고 context를 최상위 루트 컴포넌트에 넣으면 에러는 줄어들지언정 context가 많아질수록 더 많이 둘러싸여지고 불필요한 리소스 낭비가 된다. 따라서 context가 미치는 범위는 필요한 환경에서 최대한 좁게 만들어야 한다.

  context와 useContext를 상태 관리로 오해하기도 하는데, 엄밀히 따지면 context는 '상태를 주입해주는 API'로 단순히 props를 위한 값을 하위로 전달할 뿐이다.

### 7. useReducer

useState의 심화버전이다. 기본적인 사용법은 이렇다.

```javascript
type State = {
  count: number,
};

type Action = {
  type: "up" | "down" | "reset",
  payload?: State,
};

// 게으른 초기화 함수
function init(count: State): State {
  return count;
}

const initialState: State = { count: 0 };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "up":
      return { count: state.count + 1 };
    ...
    ...
    default:
      throw new Error();
  }
}

export default function App() {
  const [state, dispatcher] = useReducer(reducer, initialState, init);

  function handleUpButtonClick() {
    dispatcher({ type : 'up' })
  }

  ...
  ...
  return (
    <button onclick={handleUpButtonClick}> + </button>
  )
}
```

복잡해보이지만 dispatcher로만 state를 수정할 수 있게 만들어 state 값에 대한 접근은 컴포넌트에서만 가능하게 한다. 복잡한 형태의 state나 여러개의 state를 성격이 비슷한 state들로 묶어 useReducer로 관리하는 것이 더 효율적이다.

### 8. useImperativeHandle
부모에게서 넘겨받는 ref를 원하는 대로 수정할 수 있는 훅이다. 

- **forwardRef** <br>
  ref는 useRef에서 반환한 객체로 리액트 컴포넌트의 props인 ref에 넣어 HTMLElement에 접근하는 용도로 사용된다. <br>
  forwardRef는 ref를 전달하는 데 일관성을 제공하기 위해 탄생됐다. ref에서는 어떤 props명으로 전달할지 네이밍 자유가 있었기 때문에 forwardRef로 확실한 ref를 전달할 것임을 예측할 수 있고 안정적으로 받아 사용할 수 있다.

  상위 ref를 하위에서 받고자 할 떄 (231p 예제 참고)

  1. 먼저 ref를 받고자 하는 컴포넌트를 forwardRef로 감싸고 인수로 ref를 전달받는다.
  2. 부모 컴포넌트에서 props.ref를 통해 ref를 넘겨준다.
  3. 전달받는 컴포넌트에서 ref 이름을 그대로 사용할 수 있다. 

이러한 방법으로 useImperativeHandle 에서는 부모로부터 받는 ref를 원하는대로 수정까지 할 수 있게 해준다.

### 9. useLayoutEffect
DOM이 계산됐지만 화면에 반영되기 전에 하고 싶은 작업이 있을 때 사용하면 좋다. <br>
그리고 중요한 점은 모든 DOM의 변경 후에 useLayoutEffect의 콜백 함수 실행이 '동기적으로 발생'한다. (여기서 DOM 변경은 렌더링을 의미, 브라우저에 변경되는 시점을 의미하는 것이 아님)

1. 리액트가 DOM을 업데이트
2. useLayoutEffect를 실행
3. 브라우저에 변경 사항을 반영
4. useEffect를 실행

useLayoutEffect는 브라우저에 변경사항이 반영되기 이전에 실행되고, useEffect는 브라우저에 변경사항이 반영된 이후에 실행된다. 

위에서 말한 동기적으로 발생한다는 것은 useLayoutEffect의 실행이 종료될 때까지 기다린 다음에 화면을 그린다는 것을 의미한다.

### 10. useDebugValue
개발하는 과정에서 디버깅하고 싶은 정보를 useDebugValue 훅에 사용하면 개발자 도구에서 확인이 가능하다. <br>
사용자 정의 훅 내부의 내용에 대한 정보를 남길 수 있다.

### 11. 훅의 규칙
`rules-of-hooks` 라고 하며 리액트에서 제공하는 훅을 사용하는 데 몇 가지 규칙이 존재한다.

- 최상위에서만 훅을 호출한다. 컴포넌트가 렌더링될 때마다 항상 동일한 순서로 훅이 호출되는 것을 보장할 수 있다. 
- 훅을 호출할 수 있는 곳은 리액트 함수형 컴포넌트와 사용자 정의 훅, 두 가지 뿐이다. 

훅에 대한 정보 저장은 리액트 어딘가에 index와 같은 키를 기반으로 객체 기반 링크드 리스트에 가깝게 구현되어있다. 즉 순서에 아주 큰 영향을 받는다. 

## 사용자 정의 훅과 고차 컴포넌트
두 가지 모두 재사용할 수 있는 로직을 관리하는 방법이다. 

### 1. 사용자 정의 훅
사용자 정의 훅의 규칙 중 하나는 이름이 반드시 `use`로 시작해야 한다. 개발할 때 해당 함수가 리액트 훅이라는 것을 바로 인식할 수 있다. 만약 `use`로 시작하지 않는다면 에러가 발생한다. 

### 2. 고차 컴포넌트
고차 컴포넌트는 컴포넌트 자체의 로직을 재사용하기 위한 방법으로 다양한 최적화나 중복 로직 관리를 할 수 있다. 대표적으로 `React.memo`가 있다. 고차 컴포넌트도 마찬가지로 `with`으로 시작하는 이름을 사용해야 한다. 

고차 컴포넌트에서 주의할 점
- 부수효과를 최소화할 것. 컴포넌트의 props를 임의로 수정, 추가, 삭제하는 일이 없어야 한다.
- 여러 개의 고차 컴포넌트로 컴포넌트를 감쌀 경우 복잡성이 커진다.

### 3. 사용자 정의 훅과 고차 컴포넌트 중 무엇을 써야 할까?
- 사용자 정의 훅이 필요한 경우 <br>
  리액트에서 제공하는 훅으로만 공통 로직을 분리할 수 있을 때이다. 컴포넌트 내부에 미치는 영향을 최소화해 개발자가 훅을 원하는 방향으로 사용할 수 있다는 장점이 있다. 대부분의 고차 컴포넌트는 렌더링에 영향을 미치는 로직이 존재하므로 사용자 정의 훅에 비해 예측이 어렵다.

- 고차 컴포넌트를 사용해야 하는 경우 <br>
  예를 들어 로그인 되지 않은 어떤 사용자가 컴포넌트에 접근하려 할 때 애플리케이션 관점에서 컴포넌트를 감추고 로그인을 요구하는 공통 컴포넌트를 노출하는 것이 좋다.

  즉 렌더링의 결과물에도 영향을 미치는 공통 로직이라면 고차 컴포넌트를 사용하는 것이 좋다. 공통화된 렌더링 로직을 처리하기에 매우 훌륭한 방법이다. 하지만 고차 컴포넌트가 많아질수록 복잡성이 증가하므로 신중하게 사용해야 한다. 
