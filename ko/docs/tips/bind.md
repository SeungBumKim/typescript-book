## 바인딩의 위험성

`lib.d.ts`의 `bind`의 정의입니다:

```ts
bind(thisArg: any, ...argArray: any[]): any;
```

보시다시피 **any**를 반환합니다! 즉, 함수에서 `bind`를 호출하면 원래 함수 시그니처의 type 안전성을 완전히 잃게됩니다.

예를들어 다음내용을 컴파일해보면:

```ts
function twoParams(a:number,b:number) {
    return a + b;
}
let curryOne = twoParams.bind(null,123);
curryOne(456); // Okay but is not type checked!
curryOne('456'); // Allowed because it wasn't type checked!
```

이를 작성하는 더 좋은 방법은 명시적 type 어노테이션이 있는 간단한 화살표함수를 사용하는 것입니다.[arrow function](../arrow-functions.md):
```ts
function twoParams(a:number,b:number) {
    return a + b;
}
let curryOne = (x:number)=>twoParams(123,x);
curryOne(456); // Okay and type checked!
curryOne('456'); // Error!
```

그러나 커리된 ([curring](./currying.md)) 함수를 기대한다면, 더 좋은 패턴이있습니다..

### 클래스 멤버
또 다른 일반적인 사용법은 `bind`를 사용하여 클래스함수를 전달할 때 `this`의 값을 보장하는 것입니다. 그렇게는 사용하지 마세요! 

다음은 `bind`를 사용하면 매개변수 type의 안전성을 상실한다는 사실을 보여줍니다:

```ts
class Adder {
    constructor(public a: string) { }

    add(b: string): string {
        return this.a + b;
    }
}

function useAdd(add: (x: number) => number) {
    return add(456);
}

let adder = new Adder('mary had a little 🐑');
useAdd(adder.add.bind(adder)); // No compile error!
useAdd((x) => adder.add(x)); // Error: number is not assignable to string
```


**예상되는** 전달할 클래스 멤버 함수가 있다면, 처음부터 화살표 함수([arrow-function](../arrow-functions.md))를 사용하세요. 예를들어 같은 `Adder` 클래스를 사용하겠습니다:

```ts
class Adder {
    constructor(public a: string) { }

    // This function is now safe to pass around
    add = (b: string): string => {
        return this.a + b;
    }
}
```


또 다른 방법은 바인디되는 변수의 type을 *수동*으로 지정하는 것입니다. 예를들면:

```ts
const add: typeof adder.add = adder.add.bind(adder);
```
