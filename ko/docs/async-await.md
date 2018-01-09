## Async Await

다음과 같은 경우가 있다고 상상해보겠습니다: JavaScript를 실행 중 잠시멈추게 하려면 promise 사용할 때 `await`키워드를 코드에 넣으면 (promise의 결과를 잘 리턴한다면) 단 *한번*만 재개됩니다:

```ts
// Not actual code. A thought experiment
async function foo() {
    try {
        var val = await getMeAPromise();
        console.log(val);
    }
    catch(err) {
        console.log('Error: ', err.message);
    }
}
```

promise의 동작이 끝나면,
* 만족된다면, await는 결과값을 리턴할 것입니다.
* 에러로 거절된다면 동적으로 catch로 보내질 것입니다.

이 놀라운 방식은 비동기화 프로그래밍을 동기화 프로그램을 작성하듯 편하게 만들어줍니다. 위 테스크 코드를 위해서는 세가지가 필요합니다:
* *일시정지 함수*를 수행하는 기능.
* 함수안에 *값을 넣는* 기능.
* 함수안에서 *예외를 발생시키느* 기능.

이러한 부분은 생성기가 허용해주는 부분입니다! 위 테스트 코드는 *실제로 있으며* TypeScript/JavaScript에서 `async`/`await`로 수행됩니다.

### JavaScript 생성

직접 이해할 필요는 없으나 [generators][generators]를 보았다면 꽤 간단할 것입니다. `foo` 함수는 다음과 같이 간단한게 감싸집니다:

```ts
const foo = wrapToReturnPromise(function* () {
    try {
        var val = yield getMeAPromise();
        console.log(val);
    }
    catch(err) {
        console.log('Error: ', err.message);
    }
});
```

`wrapToReturnPromise`부분은 `생성기`를 얻기위해서 생성기 함수를 그저 실행하였고, 그 후 `generator.next()`를 사용합니다. 
결과값이 `promise`이면 promise의 `then`+`catch`수행하고 결과에 따라`generator.next (result)` 또는`generator.throw (error)`를 호출합니다.


### TypeScript의 Async Await 지원
**Async - Await** [TypeScript since version 1.7](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-1-7.html)부터 지원됩니다. 비동기화 함수앞에 *async* 키워드를 붙이고 *await*는 비동기 함수를 promise가 수행되고 *Promise*의 결과값을 받을 때까지 멈추어둡니다. **ES6 생성기**로 직접 변경가능한 **es6 대상**에서만 지원됩니다.

**TypeScript 2.1** [added the capability to ES3 and ES5 run-times](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html)에서는 어떤 환경을 사용하든 관계없이 자유롭게 사용할 수 있습니다. async/await를 TypeScript 2.1와 많은 브라우저(**Promise를 polyfill** 된)에서 사용할 수 있습니다.  
**TypeScript 2.1** [added the capability to ES3 and ES5 run-times](https://www.typescriptlang.org/docs/handbook/release-

**example**을 살펴보고 async/await **표기** 동작이 어떻게 사용되지는 알아보겠습니다:
```ts
function delay(milliseconds: number, count: number): Promise<number> {
    return new Promise<number>(resolve => {
            setTimeout(() => {
                resolve(count);
            }, milliseconds);
        });
}

// async function always return a Promise
async function dramaticWelcome(): Promise<void> {
    console.log("Hello");

    for (let i = 0; i < 5; i++) {
        // await is converting Promise<number> into number
        const count:number = await delay(500, i);
        console.log(count);
    }

    console.log("World!");
}

dramaticWelcome();
```

**ES6로 변환 (--target es6)**
```js
var __awaiter = (this && this.__awaiter) || function (thisArg, _arguments, P, generator) {
    return new (P || (P = Promise))(function (resolve, reject) {
        function fulfilled(value) { try { step(generator.next(value)); } catch (e) { reject(e); } }
        function rejected(value) { try { step(generator["throw"](value)); } catch (e) { reject(e); } }
        function step(result) { result.done ? resolve(result.value) : new P(function (resolve) { resolve(result.value); }).then(fulfilled, rejected); }
        step((generator = generator.apply(thisArg, _arguments || [])).next());
    });
};
function delay(milliseconds, count) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(count);
        }, milliseconds);
    });
}
// async function always return a Promise
function dramaticWelcome() {
    return __awaiter(this, void 0, void 0, function* () {
        console.log("Hello");
        for (let i = 0; i < 5; i++) {
            // await is converting Promise<number> into number
            const count = yield delay(500, i);
            console.log(count);
        }
        console.log("World!");
    });
}
dramaticWelcome();
```
다음을 통해서 충분한 예를 보실 수 있습니다.[here][asyncawaites6code].


**ES5로의 (--target es5)**
```js
var __awaiter = (this && this.__awaiter) || function (thisArg, _arguments, P, generator) {
    return new (P || (P = Promise))(function (resolve, reject) {
        function fulfilled(value) { try { step(generator.next(value)); } catch (e) { reject(e); } }
        function rejected(value) { try { step(generator["throw"](value)); } catch (e) { reject(e); } }
        function step(result) { result.done ? resolve(result.value) : new P(function (resolve) { resolve(result.value); }).then(fulfilled, rejected); }
        step((generator = generator.apply(thisArg, _arguments || [])).next());
    });
};
var __generator = (this && this.__generator) || function (thisArg, body) {
    var _ = { label: 0, sent: function() { if (t[0] & 1) throw t[1]; return t[1]; }, trys: [], ops: [] }, f, y, t, g;
    return g = { next: verb(0), "throw": verb(1), "return": verb(2) }, typeof Symbol === "function" && (g[Symbol.iterator] = function() { return this; }), g;
    function verb(n) { return function (v) { return step([n, v]); }; }
    function step(op) {
        if (f) throw new TypeError("Generator is already executing.");
        while (_) try {
            if (f = 1, y && (t = y[op[0] & 2 ? "return" : op[0] ? "throw" : "next"]) && !(t = t.call(y, op[1])).done) return t;
            if (y = 0, t) op = [0, t.value];
            switch (op[0]) {
                case 0: case 1: t = op; break;
                case 4: _.label++; return { value: op[1], done: false };
                case 5: _.label++; y = op[1]; op = [0]; continue;
                case 7: op = _.ops.pop(); _.trys.pop(); continue;
                default:
                    if (!(t = _.trys, t = t.length > 0 && t[t.length - 1]) && (op[0] === 6 || op[0] === 2)) { _ = 0; continue; }
                    if (op[0] === 3 && (!t || (op[1] > t[0] && op[1] < t[3]))) { _.label = op[1]; break; }
                    if (op[0] === 6 && _.label < t[1]) { _.label = t[1]; t = op; break; }
                    if (t && _.label < t[2]) { _.label = t[2]; _.ops.push(op); break; }
                    if (t[2]) _.ops.pop();
                    _.trys.pop(); continue;
            }
            op = body.call(thisArg, _);
        } catch (e) { op = [6, e]; y = 0; } finally { f = t = 0; }
        if (op[0] & 5) throw op[1]; return { value: op[0] ? op[1] : void 0, done: true };
    }
};
function delay(milliseconds, count) {
    return new Promise(function (resolve) {
        setTimeout(function () {
            resolve(count);
        }, milliseconds);
    });
}
// async function always return a Promise
function dramaticWelcome() {
    return __awaiter(this, void 0, void 0, function () {
        var i, count;
        return __generator(this, function (_a) {
            switch (_a.label) {
                case 0:
                    console.log("Hello");
                    i = 0;
                    _a.label = 1;
                case 1:
                    if (!(i < 5)) return [3 /*break*/, 4];
                    return [4 /*yield*/, delay(500, i)];
                case 2:
                    count = _a.sent();
                    console.log(count);
                    _a.label = 3;
                case 3:
                    i++;
                    return [3 /*break*/, 1];
                case 4:
                    console.log("World!");
                    return [2 /*return*/];
            }
        });
    });
}
dramaticWelcome();
```
다음을 통해서 충분한 예를 보실 수 있습니다.[here][asyncawaites5code]


**참고**: 위 두개의 대상에 대한 시나리오는 실행할 때 공용의 ECMAScript-컴파일러를 준수하는 Promise가 있는지 확인해야합니다. Promise에 대해 polyfill된 것도 포함될 수 있습니다. 또한 lib플래그를 "dom", "es2015" 또는 "dom", "es2015.promise", "es5"와 같은 것으로 설정하여 TypeScript에서 Promise가 있는지 확인해야합니다.
**Promise의 브라우저에서 지원여부(네이티브 또는 poilyfill)는 다음에서 알 수 있습니다. [here](https://kangax.github.io/compat-table/es6/#test-Promise).**

[generators]:./generators.md
[asyncawaites5code]:../code/async-await/es5/asyncAwaitES5.js
[asyncawaites6code]:../code/async-await/es6/asyncAwaitES6.js
