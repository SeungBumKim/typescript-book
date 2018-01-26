## 명목상 작성되는 코드
TypeScript type 시스템은 구조적이며 이는 주요 이점 중 하나입니다.[why-typescript](../why-typescript.md). 그러나 동일한 구조를 갖고 있더라도 두 개의 변수가 다른 *type 이름*을 가지기 때문에 두 변수를 구별하기를 원하는 시스템의 실제 사용 사례가 있습니다. 매우 일반적인 사용 사례는 *identity* 구조입니다.(일반적으로 C#/Java와 같은 언어에서 *name*과 관련된 의미 체계가 있는 문자열입니다.)

커뮤니티에 등장하는 몇가지 패턴이 있습니다. 선호하는 순서로 적어보겠습니다:

## 리터럴 type의 사용

이 패턴은 제네릭과 리터럴 type을 사용합니다:

```ts
/** Generic Id type */
type Id<T extends string> = {
  type: T,
  value: string,
}

/** Specific Id types */
type FooId = Id<'foo'>;
type BarId = Id<'bar'>;

/** Optional: contructors functions */
const createFoo = (value: string): FooId => ({ type: 'foo', value });
const createBar = (value: string): BarId => ({ type: 'bar', value });

let foo = createFoo('sample')
let bar = createBar('sample');

foo = bar; // Error
foo = foo; // Okay
```

* 장점
  - 어떤 type의 어설션 필요 없음.
* 단점
  - 구조 `{type, value}`는 바람직하지 않고 서버 직렬화 지원을 필요로 함.

## 열거형 사용
열거형은 일정 수준의 명목적인 타이핑을 해야합니다. 두 개의 열거 type은 이름이 다른 경우 동일하지 않습니다. 이 사실을 사용하여 구조적으로 호환 가능한 type에 대해 명목적인 타이핑을 해야합니다.

해결 방법은 다음과 같습니다.
* *브랜드* 열거형 생성
* 브랜드 열거형 + 실제 구조의 *intersection* (`&`) type을 만듭니다.

다음은 type의 구조가 단순한 문자열인 경우입니다:

```ts
// FOO
enum FooIdBrand {}
type FooId = FooIdBrand & string;

// BAR
enum BarIdBrand {}
type BarId = BarIdBrand & string;

/**
 * Usage Demo
 */
var fooId: FooId;
var barId: BarId;

// Safety!
fooId = barId; // error
barId = fooId; // error

// Newing up
fooId = 'foo' as FooId;
barId = 'bar' as BarId;

// Both types are compatible with the base
var str: string;
str = fooId;
str = barId;
```

## 인터페이스 사용

`numbers`는 `enum`과 type 호환이 가능하기 때문에 앞의 기술을 사용할 수 없습니다. 대신 인터페이스를 사용하여 구조적 호환성을 깨뜨릴 수 있습니다. 이 방법은 TypeScript 컴파일러 팀에서 계속 사용하므로 언급할만한 가치가 있습니다. `_` 접두어와 `Brand` 접미어를 사용하는 것을 추천합니다.([TypeScript team](https://github.com/Microsoft/TypeScript/blob/7b48a182c05ea4dea81bab73ecbbe9e013a79e99/src/compiler/types.ts#L693-L698)).

해결 방법은 다음과 같습니다:
* 구조적 호환성을 손상위한 type에 사용되지 않는 속성을 추가합니다.
* 새로운 것을 필요로 할 때 type 어설션을 사용합니다.

여기에 대한 설명이 아래있습니다:

```ts
// FOO
interface FooId extends String {
    _fooIdBrand: string; // To prevent type errors
}

// BAR
interface BarId extends String {
    _barIdBrand: string; // To prevent type errors
}

/**
 * Usage Demo
 */
var fooId: FooId;
var barId: BarId;

// Safety!
fooId = barId; // error
barId = fooId; // error
fooId = <FooId>barId; // error
barId = <BarId>fooId; // error

// Newing up
fooId = 'foo' as any;
barId = 'bar' as any;

// If you need the base string
var str: string;
str = fooId as any;
str = barId as any;
```
