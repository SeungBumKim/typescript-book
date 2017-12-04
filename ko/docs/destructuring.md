### 비구조화

TypeScript은 다음과 같은 형식의 비구조화(비구조화된 코드 뒤에 선언된 이름으로 예를들면, 구조체가 파괴된 형식)를 지원합니다:

1. 오브젝트 비구조화
2. 배열 비구조화

*구조화*의 반대라고 생각하고 비구조화는 쉽게 생각할 수 있습니다. JavaScript에서의 *구조화*방식은 오브젝트로 작성하는 것입니다:

```ts
var foo = {
    bar: {
        bas: 123
    }
};
```

JavaScript에 내장된 멋진 *구조화* 지원이 없다면, 새로운 객체를 즉석에서 만드는 것은 매우 성가실 것입니다. 비구조화는 구조체에서 데이터를 가져 오는것과 같은 수준의 편리함을 제공합니다.

#### 오브젝트 비구조화
비구조화는 몇줄의 라인으로 작성해야할 코드를 한줄로 작성할 수 있게해주어 매우 유용합니다. 다음 예를 보시면:

```ts
var rect = { x: 0, y: 10, width: 15, height: 20 };

// Destructuring assignment
var {x, y, width, height} = rect;
console.log(x, y, width, height); // 0,10,15,20

rect.x = 10;
({x, y, width, height} = rect); // assign to existing variables using outer parentheses
console.log(x, y, width, height); // 10,10,15,20
```
여기 비구조화가 `rect`에서 `x,y,width,height`에 한개씩 선택해서 넣어야합니다.

추출된 변수를 새로운 변수이름에 할당하려면 다음과 같이 하면됩니다:

```ts
// structure
const obj = {"some property": "some value"};

// destructure
const {"some property": someProperty} = obj;
console.log(someProperty === "some value"); // true
```

추가적으로 구조체의 *깊은*단계의 데이터도 비구조화를 이용해서 얻을 수 있습니다. 다음예를 통해서 볼 수 있습니다:

```ts
var foo = { bar: { bas: 123 } };
var {bar: {bas}} = foo; // Effectively `var bas = foo.bar.bas;`
```

#### rest에 대한 오브젝트 비구조화
오브젝트 비구조화를 통해서 한 오브젝트에서 임의의 갯수의 요소를 가져올 수 있고, 나머지 요소를 *객체*형태로 얻을 수 있습니다.

```ts
var {w, x, ...remaining} = {w: 1, x: 2, y: 3, z: 4};
console.log(w, x, remaining); // 1, 2, {y:3,z:4}
```
일반적으로 사용하는 방식으로 확실한 프로퍼티는 무시합니다: 예를들면:
```ts
// Example function
function goto(point2D: {x: number, y: number}) {
  // Imagine some code that might break
  // if you pass in an object
  // with more items than desired
}
// Some point you get from somewhere
const point3D = {x: 1, y: 2, z: 3};
/** A nifty use of rest to remove extra properties */
const { z, ...point2D } = point3D;
goto(point2D);
```

#### 배열 비구조화
보통 프로그래밍에서 하는 질문으로: "어떻게하면 세번째 변수 없이 두 변수를 교체 할 수 있지?" 가있습니다. 여기에 대한 TypeScript의 해답은:

```ts
var x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2,1
```
배열 비구조화는 컴파일러가 `[0], [1], ...`와 같이 수행하는 것입니다. 이러한 값이 존재한다는 보장은 없습니다.

#### rest에 대한 배열 비구조화
배열 비구조화를 통해서 한 배열에서 임의의 갯수의 요소를 가져올 수 있고, 나머지 요소를 *배열*형태로 얻을 수 있습니다.

```ts
var [x, y, ...remaining] = [1, 2, 3, 4];
console.log(x, y, remaining); // 1, 2, [3,4]
```

#### 배열 비구조화에서의 무시
좌측변수에서 `, ,`와 같이 비어있는 경우는 건너띄고 무시합니다. 예를들면:
```ts
var [x, , ...remaining] = [1, 2, 3, 4];
console.log(x, remaining); // 1, [3,4]
```

#### JS 생성
ES6 대상으로 JavaScript를 생성할때는 간단하게 임시로 변수를 생성하고, 비구조화가 없는 언어처럼 작성합니다. 예를들면:

```ts
var x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2,1

// becomes //

var x = 1, y = 2;
_a = [y,x], x = _a[0], y = _a[1];
console.log(x, y);
var _a;
```

#### 요약
비구조화는 코드의 라인을 줄이고 모호함을 제거해서 가독성과 유지보수를 용이하게 해줍니다. 배열 비구조화는 배열을 튜플처럼 사용할 수 있게해줍니다.
