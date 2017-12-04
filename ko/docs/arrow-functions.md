* [화살표 함수](#arrow-functions)
* [Tip: Arrow Function Need11](#tip-arrow-function-need11)
* [Tip: Arrow Function Need](#tip-arrow-function-need)
* [Tip: Arrow Function Danger](#tip-arrow-function-danger)
* [Tip: Libraries that use `this`](#tip-arrow-functions-with-libraries-that-use-this)
* [Tip: Arrow Function inheritance](#tip-arrow-functions-and-inheritance)

### 화살표 함수

*굵은 화살표*(`->`은 얇은 화살표이고, `=>` 굵은 화살표이기 때문에)라 부르고 또한 *람다 함수*(다른 언어에서)라고도 한다. 일반적으로 사용되는 굵은 화살표의 또 다른 기능은 `()=>something` 함수 입니다. *굵은 화살표*의 생성계기는:
1. `function` 타이핑을 하기 싫다.
2. 어휘적으로 `this`의 의미를 갖고싶다.
2. 어휘적으로 `arguments`의 의미를 갖고싶다.

JavaScript같이 기능적이라고 주장하는 언어에서 `function`을 많이 입력하는 경향이 있습니다. 굵은 화살표는 사용자가 함수를 간단하게 만들 수 있게 합니다.
```ts
var inc = (x)=>x+1;
```
`this` has traditionally been a pain point in JavaScript. As a wise man once said "I hate JavaScript as it tends to lose the meaning of `this` all too easily". Fat arrows fix it by capturing the meaning of `this` from the surrounding context. Consider this pure JavaScript class:

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
If you run this code in the browser `this` within the function is going to point to `window` because `window` is going to be what executes the `growOld` function. Fix is to use an arrow function:
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
The reason why this works is the reference to `this` is captured by the arrow function from outside the function body. This is equivalent to the following JavaScript code (which is what you would write yourself if you didn't have TypeScript):
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
Note that since you are using TypeScript you can be even sweeter in syntax and combine arrows with classes:
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

> [A sweet video about this pattern 🌹](https://egghead.io/lessons/typescript-make-usages-of-this-safe-in-class-methods)

#### Tip: Arrow Function Need11
#### Tip: Arrow Function Need
Beyond the terse syntax, you only *need* to use the fat arrow if you are going to give the function to someone else to call. Effectively:
```ts
var growOld = person.growOld;
// Then later someone else calls it:
growOld();
```
If you are going to call it yourself, i.e.
```ts
person.growOld();
```
then `this` is going to be the correct calling context (in this example `person`).

#### Tip: Arrow Function Danger

In fact if you want `this` *to be the calling context* you should *not use the arrow function*. This is the case with callbacks used by libraries like jquery, underscore, mocha and others. If the documentation mentions functions on `this` then you should probably just use a `function` instead of a fat arrow. Similarly if you plan to use `arguments` don't use an arrow function.

#### Tip: Arrow functions with libraries that use `this`
Many libraries do this e.g. `jQuery` iterables (one example http://api.jquery.com/jquery.each/) will use `this` to pass you the object that it is currently iterating over. In this case if you want to access the library passed `this` as well as the surrounding context just use a temp variable like `_self` like you would in the absence of arrow functions.

```ts
let _self = this;
something.each(function() {
    console.log(_self); // the lexically scoped value
    console.log(this); // the library passed value
});
```

#### Tip: Arrow functions and inheritance

If you have an instance method as an arrow function then it goes on `this`. Since there is only one `this` such functions cannot participate in a call to `super` (`super` only works on prototype members). You can easily get around it by creating a copy of the method before overriding it in the child.

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
