
## Freshness

TypeScript는 **Freshness** (*엄격한 객체 리터럴 검사*라고도 함)개념을 제공하여 구조적 type 호환이 가능한 객체 리터럴 검사를 쉽게 입력 할 수 있게합니다.

구조형식은 *매우 편리*합니다. 다음 코드를 참고해보겠습니다. 이렇게 하면 type 안전성을 유지하면서 JavaScript를 TypeScript로 매우 편리하게 업그레이드 할 수 있습니다:

```ts
function logName(something: { name: string }) {
    console.log(something.name);
}

var person = { name: 'matt', job: 'being awesome' };
var animal = { name: 'cow', diet: 'vegan, but has milk of own species' };
var random = { note: `I don't have a name property` };

logName(person); // okay
logName(animal); // okay
logName(random); // Error: property `name` is missing
```

그러나 *구조형식*은 약점을 가지고 있습니다. 실제로 입력하는 것보다 더 많은 데이터를 받아 들일 수 있다고 잘못판단하게 생각을 할 수 있습니다. 관련 내용은 아래에 코드를 통해서 설명되고, TypeScript는 에러를 발생시킵니다: 

```ts
function logName(something: { name: string }) {
    console.log(something.name);
}

logName({ name: 'matt' }); // okay
logName({ name: 'matt', job: 'being awesome' }); // Error: object literals must only specify known properties. `job` is excessive here.
```

이 오류는 *객체 리터럴에서만 발생*합니다. 이 오류가 없으면 `logName ({name : 'matt', job : 'awesome'})`호출을 보고 *logName*이 실제로는 완전히 무무시될 `job`과 함께 유용하게 사용할 수 있다고 생각합니다.

또 다른 많은 사용 케이스는 선택적 멤버가 있는 인터페이스와 같은 곳에서 객체 리터럴 검사가 없으면 오타가 올바르게 인식됩니다:

```ts
function logIfHasName(something: { name?: string }) {
    if (something.name) {
        console.log(something.name);
    }
}
var person = { name: 'matt', job: 'being awesome' };
var animal = { name: 'cow', diet: 'vegan, but has milk of own species' };

logIfHasName(person); // okay
logIfHasName(animal); // okay
logIfHasName({neme: 'I just misspelled name to neme'}); // Error: object literals must only specify known properties. `neme` is excessive here.
```

이런 식으로 객체리터럴 type을 검사하는 이유는 *실제로 사용되지 않는 추가 속성*이 거의 항상 오타 또는 API 오해이기 때문입니다.

### 추가 프로퍼티의 허용

type은 추가 프로퍼티가 허용되었음을 명시적으로 나타내는 색인 특성을 포함 할 수 있습니다:

```ts
var x: { foo: number, [x: string]: any };
x = { foo: 1, baz: 2 };  // Ok, `baz` matched by index signature
```

### 사용 케이스: React 상태

[Facebook ReactJS](https://facebook.github.io/react/) 오브젝트의 freshness에 대한 좋은 사용케이스를 제공합니다. 모든 구성 요소를 전달하는 대신 몇 가지 속성만 사용하여 `setState`를 호출하는 구성 요소를 전달합니다. 예를들면:

```ts
// Assuming
interface State {
  foo: string;
  bar: string;
}

// You want to do: 
this.setState({foo: "Hello"}); // Error: missing property bar

// But because state contains both `foo` and `bar` TypeScript would force you to do: 
this.setState({foo: "Hello", bar: this.state.bar}};
```

freshness의 아이디어를 이용해서 모든 멤버를 선택 사항으로 표시하고도 *여전히 오타를 발견 할 수* 있습니다!:

```ts
// Assuming
interface State {
  foo?: string;
  bar?: string;
}

// You want to do: 
this.setState({foo: "Hello"}); // Yay works fine!

// Because of freshness it's protected against typos as well!
this.setState({foos: "Hello"}}; // Error: Objects may only specify known properties

// And still type checked
this.setState({foo: 123}}; // Error: Cannot assign number to a string
```
