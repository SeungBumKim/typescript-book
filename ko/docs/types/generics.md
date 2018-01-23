## Generics

generics의 핵심 동기는 멤버간에 의미있는 type 제약 조건을 제공하는 것입니다. 멤버들은 다음과 같습니다:

* 클래스 인스턴스 멤버
* 클래스 매소드
* 함수 인자
* 함수 반환값

## 동기 및 샘플

간단한 `Queue`(선입선출) 자료구조 실행구문을 고려해보겠습니다. 간단하게보면 TypeScript/JavaScript에서 이렇게 보일 것입니다:

```ts
class Queue {
  private data = [];
  push = (item) => this.data.push(item);
  pop = () => this.data.shift();
}
```

한가지 이슈가 있다면 *아무형이나* 추가하고 큐에서 꺼낼수 있습니다.(역시 *아무형이나*). 아래를보면 큐에 `numbers`형을 넣고 있다가 `string`을 넣을 수 있습니다:

```ts
class Queue {
  private data = [];
  push = (item) => this.data.push(item);
  pop = () => this.data.shift();
}

const queue = new Queue();
queue.push(0);
queue.push("1"); // Ops a mistake

// a developer walks into a bar
console.log(queue.pop().toPrecision(1));
console.log(queue.pop().toPrecision(1)); // RUNTIME ERROR
```

솔루션을 한가지(generics가 없다면 유일한) 제시하면 이러한 제약을 위해서 *특별한* 클래스를 위에 추가하는 것입니다. 예를들면:

```ts
class QueueNumber {
  private data = [];
  push = (item: number) => this.data.push(item);
  pop = (): number => this.data.shift();
}

const queue = new QueueNumber();
queue.push(0);
queue.push("1"); // ERROR : cannot push a string. Only numbers allowed

// ^ if that error is fixed the rest would be fine too
```

물론 이것은 유지보수를 힘들게 할 수 있습니다. 예를들어 문자열큐를 원하면 다시 형을 변환하는 노력을 기울여야합니다. 원하는 것은 푸시하는 type이 *무엇이든* 팝으로 가져오는 것은 동일해야 합니다. 이는 *generic* 인자를 사용하면 쉽게 해결됩니다.(이러한 경우에는 클래스 레벨에서):

```ts
/** A class definition with a generic parameter */
class Queue<T> {
  private data = [];
  push = (item: T) => this.data.push(item);
  pop = (): T => this.data.shift();
}

/** Again sample usage */
const queue = new Queue<number>();
queue.push(0);
queue.push("1"); // ERROR : cannot push a string. Only numbers allowed

// ^ if that error is fixed the rest would be fine too
```

다른 예제는 *reverse*함수에서 이미 보았던 것으로, 함수 인자와 함수 반화값간의 제약사항입니다:

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

이번 섹션에서는 *클래스 단계*와 *함수 단계*의 generics 예제를 보았습니다. 언급할만한 하나의 작은 부가 기능은 멤버 함수용으로 생성된 generics을 가질 수 있다는 것입니다. `Utility`클래스안에 `reverse`함수를 옮겨놓은 간단한 다음 예제를 참고하세요:

```ts
class Utility {
  reverse<T>(items: T[]): T[] {
      var toreturn = [];
      for (let i = items.length - 1; i >= 0; i--) {
          toreturn.push(items[i]);
      }
      return toreturn;
  }
}
```

> 팁: generic인자는 원하는 것으로 호출할 수 있습니다. generics는 `T`, `U`, `V`와 같이 간단하게 사용할 수 있습니다. 만약 `TKey`와 `TValue`와 같이 좀 더 generic인자에 의미를 부여할 수 있습니다.(generics로서 `T`를 접두어로 붙이는 것은 다른 언어들에서 *템플릿*이라고도 부른다. C ++).

## TSX에서의 Generics

종래의`.tsx`/`.jsx`는 JSX 블록을 나타 내기 위해 `<div>`와 같은 구문을 사용하기 때문에 Generics에 대한 몇 가지 독특한 도전 과제를 제공합니다. 

> 빠른 팁: 다음에서 사용된 type주장을 위해서 `as Foo`을 사용하세요.

[type-assertion]:./type-assertion.md#as-foo-vs-foo


### Generic 함수

다음과 같이 작성해도 잘 작동합니다:

```ts
function foo<T>(x: T): T { return x; }
```

그러나 화살표 generic 함수는 그렇지 못합니다:

```ts
const foo = <T>(x: T) => x; // ERROR : unclosed `T` tag
```

**임시방편**: generic인자에 `extends`을 사용해서 컴파일러에게 generic이라고 힌트를 주는 것입니다. 예:

```ts
const foo = <T extends {}>(x: T) => x;
```

### Generic 컴포넌트

JSX에는 generic인자를 제공하기 위한 구문이 없기 때문에 이를 작성하기 전에 type 주장을 사용하여 구성 요소를 전문화 해야합니다. 예:

```ts
/** Generic component */
type SelectProps<T> = { items: T[] }
class Select<T> extends React.Component<SelectProps<T>, any> { }

/** Specialization */
interface StringSelect { new (): Select<string> };
const StringSelect = Select as StringSelect;

/** Usage */
const Form = ()=> <StringSelect items={['a', 'b']} />;
```

## 쓸모없는 Generic

사람들이 그냥 generics을 사용하는 것을 보았습니다. *어떤한 것을 제약하려는지 설명하세요*라는 의문을 제기합니다. 만약 쉽게 대답 할 수 없다면 아마 쓸모없는 Generic을 가지고있을 것입니다. 예, 사람들은 Node.js의 `require`함수를 다음과 같이 사용하려고 합니다:

```ts
declare function require<T>(name: string): T;
```

이 경우 'T'type이 한 곳에서만 사용된다는 것을 알 수 있습니다. 그래서 멤버간에 제약을 둘 필요가 없습니다. 이러한 경우에는 type주장을 사용하는 것이 좋습니다:

```ts
declare function require(name: string): any;

const something = require('something') as TypeOfSomething;
```

이것은 예제일 뿐입니다; `require`의 사용을 고려하신다면 아래와 같은 이유로 필요치 않습니다:

1. 이미 `node.d.ts`안에 있습니다: `npm install @types/node --save-dev`을 통해서 설치할 수 있습니다.
1. 직접 `require`을 사용하는 대신  `npm install @types/jquery --save-dev`를 이용한 jquery를 같은 라이브러리에 대한 type 정의 사용을 고려해야합니다.

### 디자인 패턴: 편리한 generic

`require<T>`의 이전 예제는 의도적으로 generics이 *단 한 번만* 사용 된 것은 type안전성이 type주장보다 낫지 않다는 사실을 명확히 하기위한 것입니다. 
이러한 상황은 API를 통하면 *편의성*을 제공할 수 있다고 말해줍니다.

예제는 json 응답을 로드하는 함수입니다. 여기서는 *어떠한 타입이 오던* promise로 반환해줍니다:
```ts
const getJSON = <T>(config: {
    url: string,
    headers?: { [key: string]: string },
  }): Promise<T> => {
    const fetchConfig = ({
      method: 'GET',
      'Accept': 'application/json',
      'Content-Type': 'application/json',
      ...(config.headers || {})
    });
    return fetch(config.url, fetchConfig)
      .then<T>(response => response.json());
  }
```

원하는 것을 명시적으로 어노테이션 처리해야합니다. 그러나 `getJSON <T>`의 `(config) => Promise <T>`는 몇가지 키 스트로크를 저장합니다:

```ts
type LoadUsersResponse = {
  users: {
    name: string;
    email: string;
  }[];
}
function loadUsers() {
  return getJSON<LoadUsersResponse>({ url: 'https://example.com/users' });
}
```

또한 반환값으로`Promise <T>`는 `Promise <any>`와 같은 대안보다 확실히 좋습니다.
