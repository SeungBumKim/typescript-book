### 구별되는 조합

*리터럴 멤버* 클래스가 있다면 프로퍼티를 이용해서 조합 멤버를 구별 할 수 있습니다. 

예제로 `Square`의 `Rectangle` 조합을 고려해보겠습니다. 여기에 우리는 두 멤버 모두에 존재하고 특별한 *리터럴 type*의 멤버인 `kind`를 가지고 있습니다:

```ts
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
type Shape = Square | Rectangle;
```

type 가드 스타일 체크(`==`, `===`, `!=`, `!==`)나 `switch`로 *구별되는 프러퍼티*(여기서는 `kind`)를 사용한다면, TypeScript는 객체가 해당 리터럴을 가진 type이어야하며 type이 사용자를 위해 좁아진다는 것을 의미한다는 것을 인식합니다:)

```ts
function area(s: Shape) {
    if (s.kind === "square") {
        // Now TypeScript *knows* that `s` must be a square ;)
        // So you can use its members safely :)
        return s.size * s.size;
    }
    else {
        // Wasn't a square? So TypeScript will figure out that it must be a Rectangle ;)
        // So you can use its members safely :)
        return s.width * s.height;
    }
}
```

### 철저한 체크
일반적으로 조합의 모든 구성이 각자에 대해 어떤 코드(행동)를 갖고 있는지 확인하고자 합니다.

```ts
interface Square {
    kind: "square";
    size: number;
}

interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}

// Someone just added this new `Circle` Type
// We would like to let TypeScript give an error at any place that *needs* to cater for this
interface Circle {
    kind: "circle";
    radius: number;
}

type Shape = Square | Rectangle | Circle;
```

잘못된 사용 예제입니다:

```ts
function area(s: Shape) {
    if (s.kind === "square") {
        return s.size * s.size;
    }
    else if (s.kind === "rectangle") {
        return s.width * s.height;
    }
    // Would it be great if you could get TypeScript to give you an error?
}
```

간단하게 실패하는 상황에대해서 추가하고 그 블록의 유추된 type이 `never`type과 호환되는지 확인하십시오. 예를들면:

```ts
function area(s: Shape) {
    if (s.kind === "square") {
        return s.size * s.size;
    }
    else if (s.kind === "rectangle") {
        return s.width * s.height;
    }
    else {
        // ERROR : `Circle` is not assignable to `never`
        const _exhaustiveCheck: never = s;
    }
}
```

### Switch
팁: 물론 `switch`를 사용 할 수도 있습니다:

```ts
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
        default: const _exhaustiveCheck: never = s;
    }
}
```

[references-discriminated-union]:https://github.com/Microsoft/TypeScript/pull/9163

### strictNullChecks

만약 strictNullChecks을 사용하고 철저한 검사를 하면 `_exhaustiveCheck` 변수(`never` type)도 반환해야합니다. 그렇지 않으면 TypeScript는`undefined`의 가능한 반환을 유추합니다. 그래서:

```ts
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.width * s.height;
        case "circle": return Math.PI * s.radius * s.radius;
        default:
          const _exhaustiveCheck: never = s;
          return _exhaustiveCheck;
    }
}
```

### Redux

유명한 라이브러리인 리덕스에서 이러한 방식을 사용합니다.
A popular library that makes use of this is redux.

여기에 TypeScript type 어노테이션이 추가된 redux의 *주요내용*([redux](https://github.com/reactjs/redux#the-gist))이 있습니다:

```ts
import { createStore } from 'redux'

type Action
  = {
    type: 'INCREMENT'
  }
  | {
    type: 'DECREMENT'
  }

/**
 * This is a reducer, a pure function with (state, action) => state signature.
 * It describes how an action transforms the state into the next state.
 *
 * The shape of the state is up to you: it can be a primitive, an array, an object,
 * or even an Immutable.js data structure. The only important part is that you should
 * not mutate the state object, but return a new object if the state changes.
 *
 * In this example, we use a `switch` statement and strings, but you can use a helper that
 * follows a different convention (such as function maps) if it makes sense for your
 * project.
 */
function counter(state = 0, action: Action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1
  case 'DECREMENT':
    return state - 1
  default:
    return state
  }
}

// Create a Redux store holding the state of your app.
// Its API is { subscribe, dispatch, getState }.
let store = createStore(counter)

// You can use subscribe() to update the UI in response to state changes.
// Normally you'd use a view binding library (e.g. React Redux) rather than subscribe() directly.
// However it can also be handy to persist the current state in the localStorage.

store.subscribe(() =>
  console.log(store.getState())
)

// The only way to mutate the internal state is to dispatch an action.
// The actions can be serialized, logged or stored and later replayed.
store.dispatch({ type: 'INCREMENT' })
// 1
store.dispatch({ type: 'INCREMENT' })
// 2
store.dispatch({ type: 'DECREMENT' })
// 1
```

이러한 것을 사용해서 TypeScript는 안전하게 잘못된 타이핑을 찾고, 리펙토리 능력을 시키고 스스로 문서화된 코드를 작성합니다. 
Using it with TypeScript gives you safety against typo errors, increased refactor-ability and self documenting code .
