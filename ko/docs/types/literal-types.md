## 리터럴
리터럴은 JavaScript의 기본적으로 *고정된* 값을 말한다. 

### 문자열 리터럴

문자열 리터럴은 type으로 사용할 수 있다. 예를들면:

```ts
let foo: 'Hello';
```

`'Hello'`를 *리터럴 값으로 사용한* `foo`변수를 생성하고 이에 값을 할당하려고 합니다. 아래 자세한 내용이 있습니다: 

```ts
let foo: 'Hello';
foo = 'Bar'; // Error: "Bar" is not assignable to type "Hello"
```

그것들은 그 자체만으로는별로 유용하지 않지만, type 유니온에서 결합되어 강력한 (그리고 유용한) 추상화를 생성할 수 있습니다:

```ts
type CardinalDirection =
    "North"
    | "East"
    | "South"
    | "West";

function move(distance: number, direction: CardinalDirection) {
    // ...
}

move(1,"North"); // Okay
move(1,"Nurth"); // Error!
```

### 그외 리터럴 types
TypeScript는 `boolean`, `numbers`역시 리터럴로 지원가능합니다. 예를들면:

```ts
type OneToFive = 1 | 2 | 3 | 4 | 5;
type Bools = true | false;
```

### Inference 
보통 `Type string is not assignable to type "foo"`와 같은 에러를 받는 경우가 꽤 있을 것입니다. 다음 예제가 이와같습니다.

```js
function iTakeFoo(foo: 'foo') { }
const test = {
  someProp: 'foo'
};
iTakeFoo(test.someProp); // Error: Argument of type string is not assignable to parameter of type 'foo'
```

이유는 `test`는 `{someProp: string}`type으로 추론되기 때문입니다. 여기에있는 수정 사항은 간단한 type 주장을 사용하여 TypeScript에 다음과 같이 유추할 문자를 알리는 것입니다:
```js
function iTakeFoo(foo: 'foo') { }
const test = {
  someProp: 'foo' as 'foo'
};
iTakeFoo(test.someProp); // Okay!
```

### 사용 케이스들
문자열 리터럴의 유용한 사용 케이스들은:

#### 문자열 기반의 enums
TypeScript의 enum은 숫자형을 기본으로합니다.[TypeScript enums are number based](../enums.md). 우리는 위의`CardinalDirection` 예제에서와 같이 문자열 기반의 enum을 사용하기 위해서 공용체 type을 가진 문자열 리터럴을 사용할 수 있습니다. 다음 함수를 사용하여`Key : Value` 구조체를 생성할 수도 있습니다: 

```ts
/** Utility function to create a K:V from a list of strings */
function strEnum<T extends string>(o: Array<T>): {[K in T]: K} {
  return o.reduce((res, key) => {
    res[key] = key;
    return res;
  }, Object.create(null));
}
```

그리고나서 `keyof typeof`을 사용해서 리터럴 type 조합를 생성할 수 있습니다. 여기에 완전한 예제가 있습니다:

```ts
/** Utility function to create a K:V from a list of strings */
function strEnum<T extends string>(o: Array<T>): {[K in T]: K} {
  return o.reduce((res, key) => {
    res[key] = key;
    return res;
  }, Object.create(null));
}

/**
  * Sample create a string enum
  */

/** Create a K:V */
const Direction = strEnum([
  'North',
  'South',
  'East',
  'West'
])
/** Create a Type */
type Direction = keyof typeof Direction;

/** 
  * Sample using a string enum
  */
let sample: Direction;

sample = Direction.North; // Okay
sample = 'North'; // Okay
sample = 'AnythingElse'; // ERROR!
```

#### 기존 JavaScript API 모델링
예를들어 CodeMirror에디터는 `boolean` 또는 리터럴 문자열 `"nocursor"`(유효한 유효값 `true,false,"nocursor"`))일 수 있는 `readOnly`옵션을 갖고있습니다.[here](https://codemirror.net/doc/manual.html#option_readOnly). ㅎ당내용은 아래와 같이 선언됩니다:
```ts
readOnly: boolean | 'nocursor';
```

#### 차별된 조합

다음에서 다루겠습니다.[here](./discriminated-unions.md).

[](https://github.com/Microsoft/TypeScript/pull/5185)
