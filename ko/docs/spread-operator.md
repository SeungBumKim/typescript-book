### 전개 연산자

전개 연산자의 주목적은 배열 또는 객체 요소를 *전개*하는 것이다. 아래 예제들을 보는 것이 가장 좋은 설명이 될 것이다.

#### Apply
일반적인 사용 사례는 배열을 함수인수에 적용시키는 것입니다. 과거에는 아래와 사용했다면,
 `Function.prototype.apply`:

```ts
function foo(x, y, z) { }
var args = [0, 1, 2];
foo.apply(null, args);
```

지금은 아래와 같이 간단하게 인자앞에 `...`을 적어주면 됩니다:

```ts
function foo(x, y, z) { }
var args = [0, 1, 2];
foo(...args);
```

여기서 `args`배열을 `arguments`의 위치로 *전개*시키고있다.

#### 비구조화
우리는 이미 *비구조화*에서 사용하는 방식을 보았다:

```ts
var [x, y, ...remaining] = [1, 2, 3, 4];
console.log(x, y, remaining); // 1, 2, [3,4]
```
여기에서 역할은 단순히 비구조화시 배열의 나머지 요소를 쉽게 캡처 할 수 있도록하는 것입니다.

#### 배열 할당
전개 연산자는 배열의 *펼쳐진 값*을 다른 배열에 쉽게 적용할 수 있습니다. 아래 예로 설명이됩니다:

```ts
var list = [1, 2];
list = [...list, 3, 4];
console.log(list); // [1,2,3,4]
```

#### 객체 전개
객체 역시 다른 객체로 전개가 가능합니다. 일반적인 사용은 원본에 대한 변경없이 간단히 객체의 속성에 추가됩니다:

```ts
const point2d = {x: 1, y: 2};
/** Create a new object by using all the point2D props along with z */
const point3D = {...point2D, z: 3};
```

일반적으로 사용되는 경우는 간단하게 확장하는 경우입니다:

```ts
const foo = {a: 1, b: 2};
const bar = {c: 1, d: 2};
/** Merge foo and bar */
const fooBar = {...foo, ...bar};
```

#### 요약
`apply`는 JavaScript에서 사용해야하는 일이기 때문에, `this`인수에 `null`이 없는 더 나은 구문을 가지는 것이 좋습니다. 또한 부분 배열에서 배열 처리를 수행 할 때 배열을 다른 배열로 이동 (destructuring) 또는 배치(할당)하기위한 전용 구문을 사용하면 깔끔한 구문이 됩니다.


[](https://github.com/Microsoft/TypeScript/pull/1931)
