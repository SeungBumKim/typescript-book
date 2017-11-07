
## 클로저
## Closure

자바 스크립트에서 가장 좋은 점은 클로저입니다. JavaScript의 함수는 외부범위에 정의된 모든 변수에 접근 할 수 있습니다. 클로저는 다음 예로 충분히 설명 될 것입니다:

```ts
function outerFunction(arg) {
    var variableInOuterFunction = arg;

    function bar() {
        console.log(variableInOuterFunction); // 외부 범위의 변수에 접근합니다.
    }

    // 로컬 함수를 호출하여 arg에 접근 할 수 있음을 보여줍니다.
    bar();
}

outerFunction("hello closure"); // 로그로 hello closure를 출력합니다.!
```

내부함수에서 외부범위의 변수(variableInOuterFunction)에 접근할 수 있다는 것을 알 수 있습니다. 외부범위의 변수는 내부함수에 의해 닫혀(또는 묶여)있습니다. 이러한 이유로 **클로저**라고 불립니다. 그 자체로 충분히 간단하고 직관적 개념입니다.
You can see that the inner function has access to a variable (variableInOuterFunction) from the outer scope. The variables in the outer function have been closed by (or bound in) the inner function. Hence the term closure. The concept in itself is simple enough and pretty intuitive.

멋진 부분은 내부함수에서 *outerFunction가 반환 된 후*에도 외부 범위의 변수에 접근 할 수 있다는 것 입니다. 왜냐하면 변수는 여전히 내부함수에 묶여있고 외부함수에 종속되지 있지 않기 때문입니다.
Now the awesome part: The inner function can access the variables from the outer scope *even after the outer function has returned*. This is because the variables are still bound in the inner function and not dependent on the outer function. Again let's look at an example:

```ts
function outerFunction(arg) {
    var variableInOuterFunction = arg;
    return function() {
        console.log(variableInOuterFunction);
    }
}

var innerFunction = outerFunction("hello closure!");

// Note the outerFunction has returned
innerFunction(); // logs hello closure!
```

### Reason why it's awesome
It allows you to compose objects easily e.g. the revealing module pattern:

```ts
function createCounter() {
    let val = 0;
    return {
        increment() { val++ },
        getVal() { return val }
    }
}

let counter = createCounter();
counter.increment();
console.log(counter.getVal()); // 1
counter.increment();
console.log(counter.getVal()); // 2
```

At a high level it is also what makes something like Node.js possible (don't worry if it doesn't click in your brain right now. It will eventually 🌹):

```ts
// Pseudo code to explain the concept
server.on(function handler(req, res) {
    loadData(req.id).then(function(data) {
        // the `res` has been closed over and is available
        res.send(data);
    })
});
```
