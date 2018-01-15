# TypeScript Type 시스템
우리는 다음에서 TypeScript Type 시스템의 주요 기능을 다루었었습니다.[Why TypeScript?](../why-typescript.md).
다음은 앞에서 다루어진 중요한 사항으로 더 이상의 추가적인 설명을 하지않을 부분입니다:
* typescript의 type시스템은 *추가적인*기능 부분으로 *javascript로 작성한 것은 typescript가 될 수 있습니다.*
* TypeScript는 Type 에러가 있는 *JavaScript emit*을 차단하지 않으므로 *점진적으로 JS를 TS로 업데이트* 할 수 있습니다.

이제 TypeScript type 시스템의 *구문*부터 시작합겠습니다. 이렇게하면 코드에서 이러한 내용을 즉시 사용하고 이점을 확인할 수 있습니다. 이러한 방식은 보다 더 깊은 내용을 다룰 준비가 되게합니다.

## 기본 키워드
앞에서도 다루었듯이 `:TypeAnnotation`구문으로 타입을 선언합니다. 타입 선언 공간에서 사용할 수 있는 것은 타입 키워드로 사용할 수 있습니다.

다음 예제로 타입키워드가 변수와 함수인자 그리고 함수 리턴값으로 사용되는 것을 볼 수 있습니다:

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
TypeScript는 코드에 형식을 붙이고 문서화하기 쉽게 배열을 위한 전용 type 구문을 제공합니다. 기본적으로 가능한 type키워드 뒤에 `[]` 붙입니다(예, `:boolean[]`). 평소에 할 수 있는 배열 조작을 안전하게 할 수 있게 해주고 잘못된 타입의 멤버를 할당하는 것과 같은 오류로부터 보호합니다. 아래 설명되어있습니다: 

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

`Name`키워드 안에 `first: string` + `second: string`로 구성되었고, 강제로 각각의 멤버의 type을 체크하고있습니다. 인터페이스는 TypeScript에서 많은 힘을 발휘하는데, 관련된 섹션을 통해 인터페이스의 장점에 어떻게 사용할 수 있는지에 대해 설명 할 것입니다.

### Inline Type Annotation
Instead of creating a new `interface` you can annotate anything you want *inline* using `:{ /*Structure*/ }`. The previous example presented again with an inline type:

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

Inline types are great for quickly providing a one off type annotation for something. It saves you the hassle of coming up with (a potentially bad) type name. However, if you find yourself putting in the same type annotation inline multiple times it's a good idea to consider refactoring it into an interface (or a `type alias` covered later in this section).

## Special Types
Beyond the primitive types that have been covered there are few types that have special meaning in TypeScript. These are `any`, `null`, `undefined`, `void`.

### any
The `any` type holds a special place in the TypeScript type system. It gives you an escape hatch from the type system to tell the compiler to bugger off. `any` is compatible with *any and all* types in the type system. This means that *anything can be assigned to it* and *it can be assigned to anything*. This is demonstrated in the example below:

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

If you are porting JavaScript code to TypeScript, you are going to be close friends with `any` in the beginning. However, don't take this friendship too seriously as it means that *it is up to you to ensure the type safety*. You are basically telling the compiler to *not do any meaningful static analysis*.

### `null` and `undefined`

The `null` and `undefined` JavaScript literals are effectively treated by the type system the same as something of type `any`. These literals can be assigned to any other type. This is demonstrated in the below example:

```ts
var num: number;
var str: string;

// These literals can be assigned to anything
num = null;
str = undefined;
```

### `:void`
Use `:void` to signify that a function does not have a return type:

```ts
function log(message): void {
    console.log(message);
}
```

## Generics
Many algorithms and data structures in computer science do not depend on the *actual type* of the object. However you still want to enforce a constraint between various variables. A simple toy example is a function that takes a list of items and returns a reversed list of items. The constraint here is between what is passed in to the function and what is returned by the function:

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

Here you are basically saying that the function `reverse` takes an array (`items: T[]`) of *some* type `T` (notice the type parameter in `reverse<T>`) and returns an array of type `T` (notice `: T[]`). Because the `reverse` function returns items of the same type as it takes, TypeScript knows the `reversed` variable is also of type `number[]` and will give you Type safety. Similarly if you pass in an array of `string[]` to the reverse function the returned result is also an array of `string[]` and you get similar type safety as shown below:

```ts
var strArr = ['1', '2'];
var reversedStrs = reverse(strArr);

reversedStrs = [1, 2]; // Error!
```

In fact JavaScript arrays already have a `.reverse` function and TypeScript does indeed use generics to define its structure:

```ts
interface Array<T> {
 reverse(): T[];
 // ...
}
```

This means that you get type safety when calling `.reverse` on any array as shown below:

```ts
var numArr = [1, 2];
var reversedNums = numArr.reverse();

reversedNums = ['1', '2']; // Error!
```

We will discuss more about the `Array<T>` interface later when we present `lib.d.ts` in the section **Ambient Declarations**.

## Union Type
Quite commonly in JavaScript you want to allow a property to be one of multiple types e.g. *a `string` or a `number`*. This is where the *union type* (denoted by `|` in a type annotation e.g. `string|number`) comes in handy. A common use case is a function that can take a single object or an array of the object e.g.:

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
`extend` is a very common pattern in JavaScript where you take two objects and create a new one that has the features of both these objects. An **Intersection Type** allows you to use this pattern in a safe way as demonstrated below:

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

## Tuple Type
JavaScript doesn't have first class tuple support. People generally just use an array as a tuple. This is exactly what the TypeScript type system supports. Tuples can be annotated using `:[typeofmember1, typeofmember2]` etc. A tuple can have any number of members. Tuples are demonstrated in the below example:

```ts
var nameNumber: [string, number];

// Okay
nameNumber = ['Jenny', 8675309];

// Error!
nameNumber = ['Jenny', '867-5309'];
```

Combine this with the destructuring support in TypeScript, tuples feel fairly first class despite being arrays underneath:

```ts
var nameNumber: [string, number];
nameNumber = ['Jenny', 8675309];

var [name, num] = nameNumber;
```

## Type Alias
TypeScript provides convenient syntax for providing names for type annotations that you would like to use in more than one place. The aliases are created using the `type SomeName = someValidTypeAnnotation` syntax. An example is demonstrated below:

```ts
type StrOrNum = string|number;

// Usage: just like any other notation
var sample: StrOrNum;
sample = 123;
sample = '123';

// Just checking
sample = true; // Error!
```

Unlike an `interface` you can give a type alias to literally any type annotation (useful for stuff like union and intersection types). Here are a few more examples to make you familiar with the syntax:

```ts
type Text = string | { text: string };
type Coordinates = [number, number];
type Callback = (data: string) => void;
```

> TIP: If you need to have hierarchies of Type annotations use an `interface`. They can be used with `implements` and `extends`

> TIP: Use a type alias for simpler object structures (like `Coordinates`) just to give them a semantic name. Also when you want to give semantic names to Union or Intersection types, a Type alias is the way to go.

## Summary
Now that you can start annotating most of your JavaScript code we can jump into the nitty gritty details of all the power available in the TypeScript's Type System.
