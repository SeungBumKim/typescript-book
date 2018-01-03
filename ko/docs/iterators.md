### Iterators

Iterator자체는 TypeScript 또는 ES6의 기능이 아니며 Iterator는 객체 지향 프로그래밍 언어의 Behavioral Design Pattern입니다.
일반적으로 다음과 같은 인터페이스를 구현한 객체입니다:

```ts
interface Iterator<T> {
    next(value?: any): IteratorResult<T>;
    return?(value?: any): IteratorResult<T>;
    throw?(e?: any): IteratorResult<T>;
}
```


이 인터페이스를 사용하면 객체에 속한 일부 컬렉션 또는 시퀀스에서 값을 검색 할 수 있습니다.

The `IteratorResult` is simply a `value`+`done` pair: 
```ts
interface IteratorResult<T> {
    done: boolean;
    value: T;
}
```

프레임의 구성요소로 이루어진 컴포넌트들을 포함하는 프레임 오브젝트가 있다고 생각해보십시오. 프레임 오브젝트의 Interator인터페이스를 이용해서 아래와같이 컴포넌트를 찾을 수 있습니다:

```ts
'use strict';

class Component {
  constructor (public name: string) {}
}

class Frame implements Iterator<Component> {

  private pointer = 0;

  constructor(public name: string, public components: Component[]) {}

  public next(): IteratorResult<Component> {
    if (this.pointer < this.components.length) {
      return {
        done: false,
        value: this.components[this.pointer++]
      }
    } else {
      return {
        done: true
      }
    }
  }

}

let frame = new Frame("Door", [new Component("top"), new Component("bottom"), new Component("left"), new Component("right")]);
let iteratorResult1 = frame.next(); //{ done: false, value: Component { name: 'top' } }
let iteratorResult2 = frame.next(); //{ done: false, value: Component { name: 'bottom' } }
let iteratorResult3 = frame.next(); //{ done: false, value: Component { name: 'left' } }
let iteratorResult4 = frame.next(); //{ done: false, value: Component { name: 'right' } }
let iteratorResult5 = frame.next(); //{ done: true }

//It is possible to access the value of iterator result via the value property:
let component = iteratorResult1.value; //Component { name: 'top' }
```
다시한번 말하지만 Iterator는 TypeScript의 기능이 아니며 이 코드는 명시적으로 Iterator 및 IteratorResult 인터페이스를 구현하지 않고도 동작할 것입니다. 하지만 코드 일관성을 위해서 일반적인 ES6 [interfaces](./types/interfaces.md)을 사용하는 것이 도움이 될 것입니다.

다음과 같이 인터페이스에 ES6의 [Symbol.iterator] `symbol`에 정의된 *iterable protocol*을 쓰면 더욱 도움이 될 것입니다:
```ts
//...
class Frame implements Iterable<Component> {

  constructor(public name: string, public components: Component[]) {}

  [Symbol.iterator]() {

    let pointer = 0;
    let components = this.components;

    return {

      next(): IteratorResult<Component> {
        if (pointer < components.length) {
          return {
            done: false,
            value: components[pointer++]
          }
        } else {
          return {
            done: true,
            value: null
          }
        }
      }

    }

  }

}

let frame = new Frame("Door", [new Component("top"), new Component("bottom"), new Component("left"), new Component("right")]);
for (let cmp of frame) {
  console.log(cmp);
}
```

`frame.next()`의 패턴은 동작하지 않는게 조금 아쉽게 느껴집니다. IterableIterator의 인터페이스 다음과 같이 해결할 수 있습니다!
```ts
//...
class Frame implements IterableIterator<Component> {

  private pointer = 0;

  constructor(public name: string, public components: Component[]) {}

  public next(): IteratorResult<Component> {
    if (this.pointer < this.components.length) {
      return {
        done: false,
        value: this.components[this.pointer++]
      }
    } else {
      return {
        done: true,
        value: null
      }
    }
  }

  [Symbol.iterator](): IterableIterator<Component> {
    return this;
  }

}
//...
```
`frame.next()`와`for`의 순환동작은 IterableIterator의 인터페이스에서 이제 잘 동작할 것 입니다.

Iterator로 정해진 값을 반복할 필요는 없습니다.
특별한 예제로 피보나치 수열을 보겠습니다:
```ts
class Fib implements IterableIterator<number> {

  protected fn1 = 0;
  protected fn2 = 1;

  constructor(protected maxValue?: number) {}

  public next(): IteratorResult<number> {
    var current = this.fn1;
    this.fn1 = this.fn2;
    this.fn2 = current + this.fn1;
    if (this.maxValue && current <= this.maxValue) {
      return {
        done: false,
        value: current
      }
    } return {
      done: true,
      value: null
    }

  }

  [Symbol.iterator](): IterableIterator<number> {
    return this;
  }

}

let fib = new Fib();

fib.next() //{ done: false, value: 0 }
fib.next() //{ done: false, value: 1 }
fib.next() //{ done: false, value: 1 }
fib.next() //{ done: false, value: 2 }
fib.next() //{ done: false, value: 3 }
fib.next() //{ done: false, value: 5 }

let fibMax50 = new Fib(50);
console.log(Array.from(fibMax50)); // [ 0, 1, 1, 2, 3, 5, 8, 13, 21, 34 ]

let fibMax21 = new Fib(21);
for(let num of fibMax21) {
  console.log(num); //Prints fibonacci sequence 0 to 21
}
```

####  ES5에서의 iterators의 빌드 코드
예제코드는 ES6이상의 환경이 요구되나 `Symbol.iterator`가 지원되는 JS 엔진이라면 ES5에서도 문제없이 동작합니다.
ES5(프로젝트에 es6.d.ts 추가)에서는 ES6 lib를 사용하여 컴파일 할 수 있습니다. 컴파일된 코드는 노드 4+와 구글 크롬, 기타 다른브라우저에서 동작합니다.
