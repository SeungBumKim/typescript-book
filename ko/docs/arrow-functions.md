### 화살표 함수

*굵은 화살표*(`->`은 얇은 화살표이고, `=>` 굵은 화살표이기 때문에)라 부르고 또한 *람다 함수*(다른 언어에서)라고도 한다. 일반적으로 사용되는 굵은 화살표의 또 다른 기능은 `()=>something` 함수 입니다. *굵은 화살표*의 생성계기는:
1. `function` 타이핑을 하기 싫다.
2. 어휘적으로 `this`의 의미를 갖고싶다.
2. 어휘적으로 `arguments`의 의미를 갖고싶다.

JavaScript같이 기능적이라고 주장하는 언어에서 `function`을 많이 입력하는 경향이 있습니다. 굵은 화살표는 사용자가 함수를 간단하게 만들 수 있게 합니다.
```ts
var inc = (x)=>x+1;
```
`this` 는 전통적으로 JavaScript에서 힘들게 하는 포인트였습니다. 그래서 일부 현자?는 "나는 JavaScript를 싫어한다. 왜냐하면 `this`의 의미를 너무 쉽게 잃어 버리는 경향이 있기 때문이다."와 같이 말했다. 굵은 화살표는 주변 컨텍스트에서 `this`의 의미를 포착함으로써 개선하였습니다. 이 JavaScript 클래스를 보십시오:

```ts
function Person(age) {
    this.age = age;
    this.growOld = function() {
        this.age++;
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 1, should have been 2
```
이 코드를 실행시키면 `window`는 `growOld`함수를 실행할 것이기때문에 브라우저는 함수안의 `this`는 `window`를 가리킬것입니다.
```ts
function Person(age) {
    this.age = age;
    this.growOld = () => {
        this.age++;
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 2
```
이것이 작동하는 이유는 `this`에 대한 참조를 함수 외부에서 화살표 함수에 의해 찾게됩니다. 이내용은 다음 JavaScript 코드와 동일합니다(TypeScript가없는 경우 직접 작성해야합니다.)
```ts
function Person(age) {
    this.age = age;
    var _this = this;  // capture this
    this.growOld = function() {
        _this.age++;   // use the captured this
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 2
```
TypeScript를 사용하고 있기때문에 구문이 더 멋지고 클래스와 화살표를 결합 할 수 있습니다.
```ts
class Person {
    constructor(public age:number) {}
    growOld = () => {
        this.age++;
    }
}
var person = new Person(1);
setTimeout(person.growOld,1000);

setTimeout(function() { console.log(person.age); },2000); // 2
```

> [이 패턴에 대한 비디오 🌹](https://egghead.io/lessons/typescript-make-usages-of-this-safe-in-class-methods)

#### Tip: 화살표 함수의 쓰임새
간결한 구문 외에도 다른 곳에서 함수를 효과적으로 호출해서 사용하게하려면 화살표가 *필요*합니다:
```ts
var growOld = person.growOld;
// Then later someone else calls it:
growOld();
```
직접 호출하려면 아래와 같습니다:
```ts
person.growOld();
```
그러면 `this`는 올바른 문맥이 호출 될것입니다.(이 예제에서는 `person`).

#### Tip: 화살표 함수의 위험성
사실`this`를 *호출 컨텍스트로 사용*하려면 *화살표 함수를 사용하지 않아야*합니다. jquery, underscore, mocha나 그외의 콜백이 사용되는 경우입니다. 문서에`this`에 대한 함수가 언급되어 있다면, 굵은 화살표 대신`function`을 사용해야 할 것입니다. 비슷하게`arguments`를 사용할 계획이라면 화살표 함수를 사용하지 마십시오.

#### Tip: `this`를 사용하는 라이브러에서의 화살표 함수
많은 라이브러리에서(예를들면 `jQuery` 반복문 (예) http://api.jquery.com/jquery.each/) `this`를 사용하여 현재 반복되는 객체를 전달합니다. 이 경우 `this`뿐만 아니라 주변 문맥을 통과한 라이브러리에 접근하기를 원하는 경우 화살표 함수가 없을 때처럼`_self`와 같은 임시 변수를 사용하십시오.

```ts
let _self = this;
something.each(function() {
    console.log(_self); // the lexically scoped value
    console.log(this); // the library passed value
});
```

#### Tip: 화살표 함수와 상속

인스턴스 함수에 화살표 함수를 사용하고있다면 `this`를 사용하세요. `this`는 단 하나이므로`super`호출에 참여할 수 없습니다.(`super`는 프로토타입 맴버로서만 동작합니다.) 자식에서 메서드를 재정의하기 전에 메서드 복사본을 만들어 쉽게 처리 할 수 있습니다.

```ts
class Adder {
    constructor(public a: number) {}
    // This function is now safe to pass around
    add = (b: number): number => {
        return this.a + b;
    }
}

class ExtendedAdder extends Adder {
    // Create a copy of parent before creating our own
    private superAdd = this.add;
    // Now create our override
    add = (b: number): number => {
        return this.superAdd(b);
    }
}
```
