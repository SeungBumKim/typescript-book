## 인터페이스

인터페이스에는 JS의 실행할 때 미치는 영향이 없습니다. TypeScript 인터페이스에는 변수의 구조를 선언하는 많은 기능이 있습니다.

아래 두개의 동일한 기능의 선언중 앞의 것은 *inline 방식*을 사용했고, 두번째는 *interface*를 사용했습니다:

```ts
// Sample A
declare var myPoint: { x: number; y: number; };

// Sample B
interface Point {
    x: number; y: number;
}
declare var myPoint: Point;
```

그러나 *샘플B*의 장점은 누군가가 myPoint라이브러리가 빌드된 곳에 라이브러리를 작성하여 새 멤버를 추가하면 기존 myPoint선언에 쉽게 추가 할 수 있다는 것입니다:

```ts
// Lib a.d.ts
interface Point {
    x: number; y: number;
}
declare var myPoint: Point;

// Lib b.d.ts
interface Point {
    z: number;
}

// Your code
var myPoint.z; // Allowed!
```

TypeScript의 **인터페이스는 개방형**이기 때문입니다. 이것은 TypeScript의 중요한 의미로서 *인터페이스*를 사용하여 JavaScript의 확장성을 모방 할 수 있습니다.

## 클래스는 인터페이스를 구현할 수 있습니다.

누군가 당신이 `interface`에서 선언한 객체 구조를 따라야하는 *클래스*를 사용하고자 한다면 `implements` 키워드를 사용하면 호환성을 보장 할 수 있습니다:

```ts
interface Point {
    x: number; y: number;
}

class MyPoint implements Point {
    x: number; y: number; // Same as Point
}
```

기본적으로 외부의 `Point` 인터페이스의 변경 사항이 생기면 구현단의 코드에서는 컴파일 오류가 발생하여 쉽게 동기화 할 수 있습니다:

```ts
interface Point {
    x: number; y: number;
    z: number; // New member
}

class MyPoint implements Point { // ERROR : missing member `z`
    x: number; y: number;
}
```

`implements`는 클래스의 *인스턴스*를 제한합니다. 예를들면:

```ts
var foo: Point = new MyPoint();
```

`foo : Point = MyPoint`와는 같은 것이 아닙니다.

## 팁

### 모든 인터페이스가 쉽게 구현되는 것은 아닙니다.

인터페이스는 JavaScript에 있을 수 있는 *임의의 어떠한* 구조를 선언하도록 설계되었습니다.

다음의 인터페이스 안에서 `new` 함수가 있는 경우를 고려해보겠습니다:

```ts
interface Crazy {
    new (): {
        hello: number
    };
}
```

다음과 같이 하셔야 할 것입니다:

```ts
class CrazyClass implements Crazy {
    constructor() {
        return { hello: 123 };
    }
}
// Because
const crazy = new CrazyClass(); // crazy would be {hello:123}
```

인터페이스가 있는 모든 이상한 JS를 *선언*하고 TypeScript에서 안전하게 사용할 수도 있습니다만 꼭 TypeScript 클래스를 사용하여 클래스를 구현할 수 있다는 의미는 아닙니다.
