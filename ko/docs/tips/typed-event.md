## type안전한 이벤트 발행

일반적으로 Node.js와 전통적인 JavaScript에는 단일 이벤트 발행자가 있습니다. 이 이벤트 발행자는 내부적으로 다양한 이벤트 type에 대한 리스너를 추적합니다. 예를들어:

```js
const emitter = new EventEmitter();
// Emit: 
emitter.emit('foo', foo);
emitter.emit('bar', bar);
// Listen: 
emitter.on('foo', (foo)=>console.log(foo));
emitter.on('bar', (bar)=>console.log(bar));
```
필수적으로 `EventEmitter`는 내부적으로 매핑된 배열 형태로 데이터를 저장합니다: 
```js
{foo: [fooListeners], bar: [barListeners]}
```
대신 *event* type 안전을 위해 발행*당* 이벤트 type을 만들 수 있습니다:
```js
const onFoo = new TypedEvent<Foo>();
const onBar = new TypedEvent<Bar>();

// Emit: 
onFoo.emit(foo);
onBar.emit(bar);
// Listen: 
onFoo.on((foo)=>console.log(foo));
onBar.on((bar)=>console.log(bar));
```

이는 다음과 같은 이점이 있습니다:
* 이벤트의 type은 변수로 쉽게 찾을 수 있습니다.
* 이벤트 발행 변수는 독립적으로 쉽게 리팩토링됩니다.
* Type안정적인 인벤트 데이터 구조가됩니다.

### 참조 Type이벤트
```js
export interface Listener<T> {
  (event: T): any;
}

export interface Disposable {
  dispose();
}

/** passes through events as they happen. You will not get events from before you start listening */
export class TypedEvent<T> {
  private listeners: Listener<T>[] = [];
  private listenersOncer: Listener<T>[] = [];

  on = (listener: Listener<T>): Disposable => {
    this.listeners.push(listener);
    return {
      dispose: () => this.off(listener)
    };
  }

  once = (listener: Listener<T>): void => {
    this.listenersOncer.push(listener);
  }

  off = (listener: Listener<T>) => {
    var callbackIndex = this.listeners.indexOf(listener);
    if (callbackIndex > -1) this.listeners.splice(callbackIndex, 1);
  }

  emit = (event: T) => {
    /** Update any general listeners */
    this.listeners.forEach((listener) => listener(event));

    /** Clear the `once` queue */
    this.listenersOncer.forEach((listener) => listener(event));
    this.listenersOncer = [];
  }

  pipe = (te: TypedEvent<T>): Disposable => {
    return this.on((e) => te.emit(e));
  }
}
```
