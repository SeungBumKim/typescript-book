# TypeScript Type 시스템
우리는 다음에서 TypeScript Type 시스템의 주요 기능을 다루었었습니다.[Why TypeScript?](../why-typescript.md).
다음은 앞에서 다루어진 중요한 사항으로 더 이상의 추가적인 설명을 하지않을 부분입니다:
* typescript의 type시스템은 *추가적인*기능 부분으로 *javascript로 작성한 것은 typescript가 될 수 있습니다.*
* TypeScript는 Type 에러가 있는 *JavaScript emit*을 차단하지 않으므로 *점진적으로 JS를 TS로 업데이트* 할 수 있습니다.

이제 TypeScript type 시스템의 *구문*부터 시작합겠습니다. 이렇게하면 코드에서 이러한 내용을 즉시 사용하고 이점을 확인할 수 있습니다. 이러한 방식은 보다 더 깊은 내용을 다룰 준비가 되게합니다.

## 기본 어노테이션
앞에서도 다루었듯이 `:TypeAnnotation`구문으로 타입을 선언합니다. 타입 선언 공간에서 사용할 수 있는 것은 타입 어노테이션으로 사용할 수 있습니다.

다음 예제로 타입 어노테이션이 변수와 함수인자 그리고 함수 리턴값으로 사용되는 것을 볼 수 있습니다:

```
var num: number = 123;
function identity(num: number): number {
    return num;
}
```

### 기본 Type들
JavaScript의 기본 type들은 TypeScript의 type 시스템에 잘 표현되어 있습니다. 이 말의 의미는 `string`, `number`, `boolean`을 아래와 같이 사용하다는 것입니다:

```ts
var num: number;
var str: string;
var bool: boolean;

num = 123;
num = 123.456;
num = '123'; // Error

str = '123';
str = 123; // Error

bool = true;
bool = false;
bool = 'false'; // Error
```

### 배열
TypeScript는 코드에 형식을 붙이고 문서화하기 쉽게 배열을 위한 전용 type 구문을 제공합니다. 기본적으로 가능한 type  뒤에 `[]` 붙입니다(예, `:boolean[]`). 평소에 할 수 있는 배열 조작을 안전하게 할 수 있게 해주고 잘못된 타입의 멤버를 할당하는 것과 같은 오류로부터 보호합니다. 아래 설명되어있습니다: 

```ts
var boolArray: boolean[];

boolArray = [true, false];
console.log(boolArray[0]); // true
console.log(boolArray.length); // 2
boolArray[1] = true;
boolArray = [false, false];

boolArray[0] = 'false'; // Error!
boolArray = 'false'; // Error!
boolArray = [true, 'false']; // Error!
```

### 인터페이스
인터페이스는 여러 개의 type 키워드를 단일 명명된 키워드로 작성하는 TypeScript의 핵심적인 방법입니다. 다음 예제를 고려해보세요:

```ts
interface Name {
    first: string;
    second: string;
}

var name: Name;
name = {
    first: 'John',
    second: 'Doe'
};

name = {           // Error : `second` is missing
    first: 'John'
};
name = {           // Error : `second` is the wrong type
    first: 'John',
    second: 1337
};
```

`Name` 어노테이션 안에 `first: string` + `second: string`로 구성되었고, 강제로 각각의 멤버의 type을 체크하고있습니다. 인터페이스는 TypeScript에서 많은 힘을 발휘하는데, 관련된 섹션을 통해 인터페이스의 장점에 어떻게 사용할 수 있는지에 대해 설명 할 것입니다.

### 인라인 Type 어노테이션
`interface`대신에 `:{ /*Structure*/ }`와 같이 *인라인*을 이용해서 어노테이션을 적용할 수 있습니다. 앞의 예를 인라인을 적용해서 다시보겠습니다:

```ts
var name: {
    first: string;
    second: string;
};
name = {
    first: 'John',
    second: 'Doe'
};

name = {           // Error : `second` is missing
    first: 'John'
};
name = {           // Error : `second` is the wrong type
    first: 'John',
    second: 1337
};
```

인라인 유형은 신속하게 유형에 대한 일회성 어노테이션 선언을 제공하는데 적합합니다. 이는 (잠재적으로 잘못된)type 이름을 생각해내는 번거로움을 덜어줍니다. 그러나 동일한 유형의 어노테이션을 여러번 인라인하는 경우 인터페이스(또는 뒷부분에서 다루는 `type alias`)로 리팩터링하는 것이 좋습니다. 

## 특수한 Types
`any`, `null`, `undefined`, `void` 기본 유형 외에도 TypeScript에서 특별한 의미를 갖는 유형은 거의 없습니다.

### any
`any` type은 TypeScript type 시스템에서 특별한 위치를 가지고 있습니다. 이는 type 시스템에서 벗어나기 위해 컴파일러에게 알려주는 역할을 합니다. `any`는 type 시스템에서 *어떤 또는 모든* type들을 호환시켜줍니다. 이말의 의미는 *어떤한 것도 할당할 수*있고 *어떠한 것으로도 할 당 받을 수*있다는 것입니다. 아래 예제에서 설명하겠습니다:

```ts
var power: any;

// Takes any and all types
power = '123';
power = 123;

// Is compatible with all types
var num: number;
power = num;
num = power;
```

초보자가 JavaScript 코드를 TypeScript로 변환하려면 `any`와 매우 친해져야 할 것입니다. 그러나 *type의 안전까지 보장하지 않으므로* 이러한 편리함에 너무 기대를 많이 갖으면 안됩니다. 기본적으로 컴파일러에게 *의미있는 정적 분석을하지 말아 달라고*을 명령말하기 때문입니다.

### `null`과 `undefined`

JavaScript에서의 `null`과 `undefined` 문법은 `any` type과 같이 type 시스템에서 효과적으로 다루어집니다. 이러한 문법은 다른 유형으로 할당 될 수 있습니다. 아래 예를 통해서 살펴보겠습니다:

```ts
var num: number;
var str: string;

// These literals can be assigned to anything
num = null;
str = undefined;
```

### `:void`
`:void`을 사용하면 함수는 리턴 type이 없다는 것을 명시합니다:

```ts
function log(message): void {
    console.log(message);
}
```

## Generics
컴퓨터 과학의 많은 알고리즘과 데이터 구조는 객체의 *실제 유형*에 의존하지 않습니다. 그러나 여전히 다양한 변수간에 제약 조건을 적용하려고합니다. 간단한 예제는 항목 목록을 가져와 역순으로 나열된 항목 목록을 반환하는 함수입니다. 여기서 제약된 내용은 함수에 전달되는 것과 함수에 의해 반환되는 것 사이의 관계입니다:

```ts
function reverse<T>(items: T[]): T[] {
    var toreturn = [];
    for (let i = items.length - 1; i >= 0; i--) {
        toreturn.push(items[i]);
    }
    return toreturn;
}

var sample = [1, 2, 3];
var reversed = reverse(sample);
console.log(reversed); // 3,2,1

// Safety!
reversed[0] = '1';     // Error!
reversed = ['1', '2']; // Error!

reversed[0] = 1;       // Okay
reversed = [1, 2];     // Okay
```

여기서 `reverse` 함수는 *어떠한* type `T`의 배열 (`items : T []`)을 취하고 (`reverse <T>`에 있는 type 매개 변수에 주목하십시오) 해당 유형의 배열을 반환한다는 것을 기본적으로 말합니다`T`(주의 :`T []`). `reverse` 함수는 동일한 타입의 항목을 반환하기 때문에 TypeScript는`reversed` 변수가`number []` type이기도하고 type 안전성을 제공한다는 것을 알고 있습니다. 유사하게 `string[]`을 reserve함수에 넘긴다면 역시 `string[]`을 반환 할 것이고 아래와 type 안정성도 갖게 될 것입니다:

```ts
var strArr = ['1', '2'];
var reversedStrs = reverse(strArr);

reversedStrs = [1, 2]; // Error!
```

사실은 JavaScript배열은 이미 `.reverse`함수를 갖고있고 TypeScript는 사실 제네릭을 사용하여 구조를 정의합니다:

```ts
interface Array<T> {
 reverse(): T[];
 // ...
}
```

이것은 아래와 같이 배열에서`.reverse`를 호출 할 때 type 안전성을 얻는다는 것을 의미합니다:

```ts
var numArr = [1, 2];
var reversedNums = numArr.reverse();

reversedNums = ['1', '2']; // Error!
```

나중에 **Ambient Declarations** 섹션에서 `lib.d.ts`를 제시 할 때 `Array<T>` 인터페이스에서 더 자세히 논의 할 것입니다.

## 유니온 Type
JavaScript에서 일반적으로 속성을 여러 유형 중 하나가 되게 하려는 경우가 많습니다. 예를들면, *`문자열`또는`숫자`*. 여기서 *유니온 type*(type 어노테이션에서`|`로 표시됨, 예를 들어`string | number`)가 유용합니다. 일반적인 사용 사례는 하나의 객체 또는 객체의 배열을 취할 수 있는 함수입니다:

```ts
function formatCommandline(command: string[]|string) {
    var line = '';
    if (typeof command === 'string') {
        line = command.trim();
    } else {
        line = command.join(' ').trim();
    }

    // Do stuff with line: string
}
```

## Intersection Type
`extend`는 JavaScript에서 두 개의 객체를 가져와 두 객체의 기능을 가진 새로운 객체를 만드는 매우 일반적인 패턴입니다. **Intersection Type**을 사용하면 아래에 설명된 것처럼 안전하게이 패턴을 사용할 수 있습니다:

```ts
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U> {};
    for (let id in first) {
        result[id] = first[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            result[id] = second[id];
        }
    }
    return result;
}

var x = extend({ a: "hello" }, { b: 42 });

// x now has both `a` and `b`
var a = x.a;
var b = x.b;
```

## 튜플 Type
JavaScript에는 튜플 지원이 없습니다. 일반적으로 튜플 배열로 만들어서 사용합니다. 이것은 정확히 TypeScript type 시스템이 지원하는 것입니다. 튜플은 `:[typeofmember1, typeofmember2]`와 같은 방식으로 사용합니다. 튜플은 여러개의 멤버를 가질 수 있습니다. 튜플은 아래에 설명된 예로 볼 수 있습니다:

```ts
var nameNumber: [string, number];

// Okay
nameNumber = ['Jenny', 8675309];

// Error!
nameNumber = ['Jenny', '867-5309'];
```

이것을 TypeScript의 구조적 지원과 결합하면 튜플은 배열임에도 불구하고 상당히 우수하다고 느껴집니다.

```ts
var nameNumber: [string, number];
nameNumber = ['Jenny', 8675309];

var [name, num] = nameNumber;
```

## Type 별칭
TypeScript는 두 개 이상의 장소에서 사용하려는 유형 어노테이션에 이름을 제공하기 위한 편리한 구문을 제공합니다. 별칭은 `type SomeName = someValidTypeAnnotation`와 같은 구문으로 만들 수 있습니다. 아래의 예제와 같이 설명됩니다:

```ts
type StrOrNum = string|number;

// Usage: just like any other notation
var sample: StrOrNum;
sample = 123;
sample = '123';

// Just checking
sample = true; // Error!
```


`interface`와는 달리 문자 그대로 type 어노테이션에 type 별칭을 줄 수 있습니다(union과 intersection type과 같은 것들에 유용합니다). 아래에 좀더 구문을 친숙하게 사용할 수 있는 예제가 있습니다:

```ts
type Text = string | { text: string };
type Coordinates = [number, number];
type Callback = (data: string) => void;
```

> 팁: Type 어노테이션의 계층을 가질 필요가 있다면 `interface`를 사용하십시오. `implements`와 `extends`와 함께 사용할 수 있습니다.

> 팁: 의미가있는 이름을 주기위한 더 간단한 객체 구조(`Coordinates`와 같은)를 위해 타입 별명을 사용하십시오. 또한 Union 또는 Intersection 유형에 의미론적 이름을 지정하려는 경우 Type 별칭을 사용하십시오.

## 요약
이제 대부분의 JavaScript 코드에 어노테이션을 달기 시작할 수 있으므로 TypeScript의 type 시스템에서 사용할 수 있는 모든 기능에 대해 자세히 살펴볼 수 있습니다.
