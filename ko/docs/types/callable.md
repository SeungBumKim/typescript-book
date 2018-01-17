## Callable
다음과 같이 type 또는 인터페이스의 일부로 호출 가능 파일에 키워드를 추가 할 수 있습니다

```js
interface ReturnString {
  (): string
}
```
이러한 인터페이스의 인스턴스는 문자열을 반환하는 함수입니다. 예를 들면:

```js
declare const foo: ReturnString;
const bar = foo(); // bar is inferred as a string
```

### 예제
물론 이러한 호출 가능한 키워드는 필요에 따라 인수/선택적 인수/나머지 인수를 지정할 수도 있습니다. 예를들어, 다음 복잡한 예제를 보겠습니다:

```js
interface Complex {
  (foo: string, bar?: number, ...others: boolean[]): number;
}
```
They can even specify overloads: 
```js
interface Overloaded {
  (foo: string): string
  (foo: number): number
}

// example implementation
const overloaded: Overloaded = (foo) => foo;

// example usage
const str = overloaded(''); // str is inferred string
const number = overloaded(123); // num is inferred number
```

물론 인터페이스/type의 *모든* 본문처럼 변수 type 키워드로 사용할 수 있습니다. 예를들면:

```js
const overloaded: {
  (foo: string): string
  (foo: number): number
} = (foo) => foo;
```

### 화살표  구문
호출 가능 서명을 쉽게 지정할 수 있도록 TypeScript는 간단한 화살표 type 키워드도 허용합니다. 예를들면:

```js
const simple: (foo: number) => string
    = (foo) => foo.toString();
```

화살표 구문의 제한 사항을 보자면: 오버로드를 지정할 수 없는 것 입니다. 오버로드의 경우, 당신은 완전한 본문의 `{(someArgs) : someReturn}`구문을 사용해야 합니다.

### Newable

Newable은 'new'라는 접두사를 가진 특별한 유형의 *호출 가능한* type키워드 입니다. 단순히 `new`로 *호출*해야 한다는 것을 의미합니다. 예를들면: 

```js
interface CallMeWithNewToGetString {
  new(): string
}
// Usage 
declare const Foo: CallMeWithNewToGetString;
const bar = new Foo(); // bar is inferred to be of type string 
```
