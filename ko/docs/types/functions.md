## 함수
TypeScript의 type 시스템은 함수에 많은 관심을 쏟아 부었습니다. 모두 구성 가능한 시스템의 핵심 구성 요소입니다.

### 매개변수 어노테이션
물론 다른 변수에 어노테이션을 달 수 있는 것처럼 함수 매개 변수에 어노테이션을 달 수 있습니다.
Of course you can annotate function parameters just like you can annotate other variables:

```ts
// variable annotation
var sampleVariable: { bar: number }

// function parameter
function foo(sampleParameter: { bar: number }) { }
```

여기에 인라인 type 어노테이션을 사용했습니다. 물론 인터페이스로 사용해도 됩니다.

### type 어노테이션 반환

변수에 사용하는 것과 동일한 스타일의 함수 매개변수 목록에 반환 type에 키워드를 달 수 있습니다. 예를들면, 아래 예의 `: Foo` 와 같은 경우 입니다:

```ts
interface Foo {
    foo: string;
}

// Return type annotated as `: Foo`
function foo(sample: Foo): Foo {
    return sample;
}
```

마찬가지로 여기서 `interface`를 사용했지만 다른 어노테이션을 사용하시는 것은 자유입니다. 예를들면, 인라인 어노테이션. 

보편적으로 컴파일러가 일반적으로 추론 할 수 있는 함수의 반환 type에는 키워드를 달지 않아도됩니다.

```ts
interface Foo {
    foo: string;
}

function foo(sample: Foo) {
    return sample; // inferred return type 'Foo'
}
```

그러나 일반적으로 이러한 어노테이션을 추가하여 오류를 방지하는 것이 좋습니다. 예를들면:

```ts
function foo() {
    return { fou: 'John Doe' }; // You might not find this misspelling `foo` till it's too late
}

sendAsJSON(foo());
```

함수에서 무엇이든지 반환 할 계획이 아니라면 `: void`로 어노테이션을 달 수 있습니다. 일반적으로 `: void`를 삭제하고 엔진에서 처리하게 남겨 둘 수 있습니다.

### 선택적 매개변수
매개변수를 선택할 수 있는 표시 방식이 있습니다:

```ts
function foo(bar: number, bas?: string): void {
    // ..
}

foo(123);
foo(123, 'hello');
```

또는 매개 변수 선언 뒤에 `= someValue`를 사용하여 기본값을 제공 할 수도 있습니다. 호출자가 해당 인수를 제공하지 않으면 해당 값이 에게 할당됩니다:

```ts
function foo(bar: number, bas: string = 'hello') {
    console.log(bar, bas);
}

foo(123);           // 123, hello
foo(123, 'world');  // 123, world
```

### 오버로딩
TypeScript는 *선언*함수의 오버로딩을 허용합니다. 이러한 내용은 문서적 + type안정성의 목적으로 유용합니다. 다음 코드를 고려해보세요: 

```ts
function padding(a: number, b?: number, c?: number, d?: any) {
    if (b === undefined && c === undefined && d === undefined) {
        b = c = d = a;
    }
    else if (c === undefined && d === undefined) {
        c = a;
        d = b;
    }
    return {
        top: a,
        right: b,
        bottom: c,
        left: d
    };
}
```

코드를 자세히보면 `a`,`b`,`c`,`d`는 변환 가능해서 많은 종류의 인자가 넘어올 수 있습니다.  또한 함수에서는 오직 `1`, `2`또는 `4`의 인자만 원할 수도 있습니다. 이러한 제약은 함수 오버로딩을 사용하여 *강제화* 및 *문서화* 될 수 있습니다. 당신은 그저:

* 함수의 헤더를 여러번 선언하고,
* 마지막 함수 헤더는 함수 본체 *내에서* 실제로 활성화 되어 있지만 외부 세계에서는 사용할 수 없습니다.

이러한 내요은 아래 보여줍니다:

```ts
// Overloads
function padding(all: number);
function padding(topAndBottom: number, leftAndRight: number);
function padding(top: number, right: number, bottom: number, left: number);
// Actual implementation that is a true representation of all the cases the function body needs to handle
function padding(a: number, b?: number, c?: number, d?: number) {
    if (b === undefined && c === undefined && d === undefined) {
        b = c = d = a;
    }
    else if (c === undefined && d === undefined) {
        c = a;
        d = b;
    }
    return {
        top: a,
        right: b,
        bottom: c,
        left: d
    };
}
```

여기서 처음 세 함수 시그니처는 `padding`에 대한 유효한 호출로 사용할 수 있는 시그니처입니다:

```ts
padding(1); // Okay: all
padding(1,1); // Okay: topAndBottom, leftAndRight
padding(1,1,1,1); // Okay: top, right, bottom, left

padding(1,1,1); // Error: Not a part of the available overloads
```


물론 최종 선언(함수 내부에서 본 진정한 선언)은 모든 오버로드와 호환 될 수 있어야 합니다. 이는 함수 본문이 고려해야하는 함수 호출의 본질이기 때문입니다.

> TypeScript의 함수 오버로드에는 런타임 오버 헤드가 없습니다. 함수 호출을 기대하는 방식과 컴파일러가 나머지 코드를 체크 할 수 있도록 문서화 할 수 있습니다.

[](### Declaring Functions)
[](With lambda, with interfaces which allow overloading declarations)

[](### Type Compatability)
