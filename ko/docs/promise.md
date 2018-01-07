## Promise


`Promise`클래스는 많은 최신 JavaScript 엔진에 존재하며 쉽게 [polyfilled][polyfill]할 수 있습니다.
promise의 주된 목적는 비동기/콜백 스타일의 코드에서 동기화된 스타일의 에러처리를 할 수 있게 해줍니다.

### 콜백 스타일의 코드

promises를 완전히 이해하기 위해서 신뢰할 수 있는 비동기 코드를 작성하는 것이 어렵다는 것을 보여주는 간단한 샘플을 제시하겠습니다. 파일에서 JSON을로드하는 비동기 버전을 작성하는 간단한 경우를 생각해보십시오. 동기화된 버전은 다음과 같이 상당히 간단합니다:

```ts
import fs = require('fs');

function loadJSONSync(filename: string) {
    return JSON.parse(fs.readFileSync(filename));
}

// good json file
console.log(loadJSONSync('good.json'));

// non-existent file, so fs.readFileSync fails
try {
    console.log(loadJSONSync('absent.json'));
}
catch (err) {
    console.log('absent.json error', err.message);
}

// invalid json file i.e. the file exists but contains invalid JSON so JSON.parse fails
try {
    console.log(loadJSONSync('invalid.json'));
}
catch (err) {
    console.log('invalid.json error', err.message);
}
```

`loadJSONSync` 함수에는 정상적인 값 리턴과 파일시스템과 JSON.parse 에러를 체크하는 세가지 동작이 있습니다. 다른 언어의 동기화된 프로그래밍에서라면 에러를 핸들링하기 위해서 간단히 try/catch를 이용할 수 있을 것입니다. 이제 해당 함수의 비동화된 버전을 보겠습니다. 에러를 확인하는 로직으로 다음과 같이 시작해볼 수 있을 것입니다: 

```ts
import fs = require('fs');

// A decent initial attempt .... but not correct. We explain the reasons below
function loadJSON(filename: string, cb: (error: Error, data: any) => void) {
    fs.readFile(filename, function (err, data) {
        if (err) cb(err);
        else cb(null, JSON.parse(data));
    });
}
```

충분히 간단합니다. 콜백을 통해서 파일 시스템의 에러를 전달합니다. 파일 시스템에 에러가 없다면 `JSON.parse`의 결과를 리턴할 것입니다. 비동기 함수를 처리할 떄 조심해야할 사항이 몇가지 있습니다:
1. 콜백함수가 두번 호출되지 않게 하라.
1. 에러를 throw 시키지 마라.

간단한 함수이지만 두군데의 잘못된 곳이 있다. `JSON.parse`에서 에러가 발생한다면 잘못된 JSON이 넘어갈 것가 잘못된 값에 접근 한 앱은 문제가 생길 것이다. 아래의 예로 설명이 될 것입니다:

```ts
import fs = require('fs');

// A decent initial attempt .... but not correct
function loadJSON(filename: string, cb: (error: Error, data: any) => void) {
    fs.readFile(filename, function (err, data) {
        if (err) cb(err);
        else cb(null, JSON.parse(data));
    });
}

// load invalid json
loadJSON('invalid.json', function (err, data) {
    // This code never executes
    if (err) console.log('bad.json error', err.message);
    else console.log(data);
});
```

간단한 해결책은 아래와 같이 `JSON.parse`을 try/catch로 감싸는 것입니다:

```ts
import fs = require('fs');

// A better attempt ... but still not correct
function loadJSON(filename: string, cb: (error: Error) => void) {
    fs.readFile(filename, function (err, data) {
        if (err) {
            cb(err);
        }
        else {
            try {
                cb(null, JSON.parse(data));
            }
            catch (err) {
                cb(err);
            }
        }
    });
}

// load invalid json
loadJSON('invalid.json', function (err, data) {
    if (err) console.log('bad.json error', err.message);
    else console.log(data);
});
```

그러나 자세히보면 이 코드도 버그가 있습니다. 콜백(`cb`)이 실행되고, `JSON.parse`가 없는 상태에서 에러가 던져지면 `try`/`catch`로 둘러싸여서 `catch`가 실행되면서 다시 콜백이 실행됩니다. 즉 콜백이 두번 실행된 것이다! 아래 예제에서 설명됩니다: 

```ts
import fs = require('fs');

function loadJSON(filename: string, cb: (error: Error) => void) {
    fs.readFile(filename, function (err, data) {
        if (err) {
            cb(err);
        }
        else {
            try {
                cb(null, JSON.parse(data));
            }
            catch (err) {
                cb(err);
            }
        }
    });
}

// a good file but a bad callback ... gets called again!
loadJSON('good.json', function (err, data) {
    console.log('our callback called');

    if (err) console.log('Error:', err.message);
    else {
        // let's simulate an error by trying to access a property on an undefined variable
        var foo;
        // The following code throws `Error: Cannot read property 'bar' of undefined`
        console.log(foo.bar);
    }
});
```

```bash
$ node asyncbadcatchdemo.js
our callback called
our callback called
Error: Cannot read property 'bar' of undefined
```

`loadJSON`함수의 `try`블럭안에 잘못되게 콜백함수가 사용된 것입니다. 여기서 간단한 교훈 한가지를 짚어 보겠습니다.

> 교훈: try/catch안에는 콜백을 호출할 때를 제외하고는 동기화된 코드를 넣으세요. 

이 간단한 교훈을 통해서 우리는 `loadJSON`의 완벽한 버전의 비동기 함수를 아래와 같이 만들 수 있습니다:

```ts
import fs = require('fs');

function loadJSON(filename: string, cb: (error: Error) => void) {
    fs.readFile(filename, function (err, data) {
        if (err) return cb(err);
        // Contain all your sync code in a try catch
        try {
            var parsed = JSON.parse(data);
        }
        catch (err) {
            return cb(err);
        }
        // except when you call the callback
        return cb(null, parsed);
    });
}
```
틀림없이 몇 번 해보면 이 작업을 수행하기가 어렵지 않지만 오류 처리를 위해 간단히 작성하는 보일러 플레이트 코드가 많았습니다. 이제 promises을 사용하여 비동기 JavaScript를 처리하는 더 좋은 방법을 살펴 보겠습니다.

## Promise의 생성

promise는 `pending`나 `fulfilled` 또는 `rejected` 중에 하나 일 수 있니다.

![](https://raw.githubusercontent.com/basarat/typescript-book/master/images/promise%20states%20and%20fates.png)

promise의 생성을 살펴보면 간단하게 `Promise`앞에 `new`(promise 생성자)를 넣어주면 됩니다. promise 생성자는 promise 상태를 정하는 `resolve`와 `reject`함수를 전달합니다:

```ts
const promise = new Promise((resolve, reject) => {
    // the resolve / reject functions control the fate of the promise
});
```

### promise의 fate 연결하기

promise의 fate는 `.then`(해결될 경우)나 `.catch`(거절될 경우)을 통해서 연결됩니다.

```ts
const promise = new Promise((resolve, reject) => {
    resolve(123);
});
promise.then((res) => {
    console.log('I get called:', res === 123); // I get called: true
});
promise.catch((err) => {
    // This is never called
});
```

```ts
const promise = new Promise((resolve, reject) => {
    reject(new Error("Something awful happened"));
});
promise.then((res) => {
    // This is never called
});
promise.catch((err) => {
    console.log('I get called:', err.message); // I get called: 'Something awful happened'
});
```

> 팁: Promise 바로접근하기
* 이미 resolved promise를 이용해서 빠르게 생성: `Promise.resolve(result)`
* 이미 rejected promise를 이용해서 빠르게 생성: `Promise.reject(error)`

### Promises의 연쇄성
Promises의 연쇄성은 *promises가 제공하는 핵심기능* 입니다. promise를 생성했다면 그곳을 기점으로 `then`함수를 이용해서 연결되는 promise들을 생성할 수 있습니다.

* 체인안의 특정 함수에서 promise를 리턴한다면, `.then`은 오직 값이 resolved 된경우에만 호출됩니다.  
* If you return a promise from any function in the chain, `.then` is only called once the value is resolved:

```ts
Promise.resolve(123)
    .then((res) => {
        console.log(res); // 123
        return 456;
    })
    .then((res) => {
        console.log(res); // 456
        return Promise.resolve(123); // Notice that we are returning a Promise
    })
    .then((res) => {
        console.log(res); // 123 : Notice that this `then` is called with the resolved value
        return 123;
    })
```

* 체인에서 한개의 'catch'로 선행부분의 오류를 처리 할 수 있습니다.

```ts
// Create a rejected promise
Promise.reject(new Error('something bad happened'))
    .then((res) => {
        console.log(res); // not called
        return 456;
    })
    .then((res) => {
        console.log(res); // not called
        return 123;
    })
    .then((res) => {
        console.log(res); // not called
        return 123;
    })
    .catch((err) => {
        console.log(err.message); // something bad happened
    });
```

* `catch`는 실제로 새로운 promise(새로운 promise 연결을 효과적으로 생성함)를 리턴합니다:
* The `catch` actually returns a new promise (effectively creating a new promise chain):

```ts
// Create a rejected promise
Promise.reject(new Error('something bad happened'))
    .then((res) => {
        console.log(res); // not called
        return 456;
    })
    .catch((err) => {
        console.log(err.message); // something bad happened
        return 123;
    })
    .then((res) => {
        console.log(res); // 123
    })
```

* `then` (또는 `catch`)안에서 동기화된 에러를 던지면 리턴되는 promise는 실패입니다:

```ts
Promise.resolve(123)
    .then((res) => {
        throw new Error('something bad happened'); // throw a synchronous error
        return 456;
    })
    .then((res) => {
        console.log(res); // never called
        return Promise.resolve(789);
    })
    .catch((err) => {
        console.log(err.message); // something bad happened
    })
```

* 오직 관련된(가장 가까운 연결) `catch`로 에러가 발생되었을 때 호출됩니다.(여기서부터 새로운 promise 체인을 시작합니다)

```ts
Promise.resolve(123)
    .then((res) => {
        throw new Error('something bad happened'); // throw a synchronous error
        return 456;
    })
    .catch((err) => {
        console.log('first catch: ' + err.message); // something bad happened
        return 123;
    })
    .then((res) => {
        console.log(res); // 123
        return Promise.resolve(789);
    })
    .catch((err) => {
        console.log('second catch: ' + err.message); // never called
    })
```

* `catch`는 앞 단계의 체인에서의 에러상황에서만 호출 됩니다:

```ts
Promise.resolve(123)
    .then((res) => {
        return 456;
    })
    .catch((err) => {
        console.log("HERE"); // never called
    })
```

관련 사실을 정리하면:
* 에러가 발생하면 뒤에 있는 `catch`(중간에 있는 `then`은 넘어갑니다.) 로 이동하고
* 동기화된 에러처리 역시 뒤에 있는 `catch`에 잡힙니다.

실제로 원시적인 콜백보다 더 나은 오류처리를 제공하는 비동기 프로그래밍 패러다임을 제공합니다. 추가적인 내요은 아래 있습니다.


### TypeScript와 promises
TypeScript의 좋은점은 promise의 연결에서 값의 흐름을 이해 한다는 것입니다:

```ts
Promise.resolve(123)
    .then((res) => {
         // res is inferred to be of type `number`
         return true;
    })
    .then((res) => {
        // res is inferred to be of type `boolean`

    });
```

물론 promise를 리턴하는 함수의 호출도 이해할 수 있습니다:

```ts
function iReturnPromiseAfter1Second(): Promise<string> {
    return new Promise((resolve) => {
        setTimeout(() => resolve("Hello world!"), 1000);
    });
} 

Promise.resolve(123)
    .then((res) => {
        // res is inferred to be of type `number`
        return iReturnPromiseAfter1Second(); // We are returning `Promise<string>`
    })
    .then((res) => {
        // res is inferred to be of type `string`
        console.log(res); // Hello world!
    });
```


### 콜백 스타일을 promise리턴 형태로 변경하기

함수를 promise로 감싸고
- 에러가 발생하면 `reject`,
- 잘 처리 되었다면 `resolve`한다.

예. `fs.readFile`를 감싸봅시다:

```ts
import fs = require('fs');
function readFileAsync(filename: string): Promise<any> {
    return new Promise((resolve,reject) => {
        fs.readFile(filename,(err,result) => {
            if (err) reject(err);
            else resolve(result);
        });
    });
}
```


### 앞의 JSON 예를 다시 살펴봅니다.

앞의 `loadJSON`의 예를 다시보고 promise를 사용해서 비동기화 버전 코드를 다시 작성해보겠습니다. 필요한 부분은 파일을 읽는 부분은 promise로 한다음 그것을 JSON으로 파싱하면 됩니다. 이것을 아래와 같이 작성할 수 있습니다:

```ts
function loadJSONAsync(filename: string): Promise<any> {
    return readFileAsync(filename) // Use the function we just wrote
                .then(function (res) {
                    return JSON.parse(res);
                });
}
```

사용법(이 섹션의 시작부분에 있는 원래의 `동기화`버전과 얼마나 유사한지 살펴보세요):
```ts
// good json file
loadJSONAsync('good.json')
    .then(function (val) { console.log(val); })
    .catch(function (err) {
        console.log('good.json error', err.message); // never called
    })

// non-existent json file
    .then(function () {
        return loadJSONAsync('absent.json');
    })
    .then(function (val) { console.log(val); }) // never called
    .catch(function (err) {
        console.log('absent.json error', err.message);
    })

// invalid json file
    .then(function () {
        return loadJSONAsync('invalid.json');
    })
    .then(function (val) { console.log(val); }) // never called
    .catch(function (err) {
        console.log('bad.json error', err.message);
    });
```

이 함수가 더 간단한 이유는 "`loadFile`(async) + `JSON.parse` (sync) => `catch`"과정이 promise의 연결로 간편해졌기 때문입니다. 또한 콜백이 *우리*에 의해서 호출되지 않고 promise연결에 의해서 호출되므로 `try/catch`를 감싸면서 만들 수 있는 실수도 줄일 수 있습니다.

### 병렬 처리의 흐름
promise로 사용된 일련의 비동기화 작업 시퀀스가 얼마나 간단한지 보았습니다. 간단하게 `then`의 호출로 연결해주면 됩니다.

그러나 일련의 비동기 작업을 실행한 다음 이러한 모든 작업의 ​​결과로 무언가를 수행하려고 할 수 있습니다. `Promise`는 `n`의 promise가 끝나는 것을 기다려주는 `Promise.all` static 함수를 제공합니다. `n`개의 promise를 제공하고 `n`의 성공된 결과를 줍니다. 아래 병렬과 연결에 대한 예를 보여드립니다:

```ts
// an async function to simulate loading an item from some server
function loadItem(id: number): Promise<{ id: number }> {
    return new Promise((resolve) => {
        console.log('loading item', id);
        setTimeout(() => { // simulate a server delay
            resolve({ id: id });
        }, 1000);
    });
}

// Chaining
let item1, item2;
loadItem(1)
    .then((res) => {
        item1 = res;
        return loadItem(2);
    })
    .then((res) => {
        item2 = res;
        console.log('done');
    }); // overall time will be around 2s

// Parallel
Promise.all([loadItem(1), loadItem(2)])
    .then((res) => {
        [item1, item2] = res;
        console.log('done');
    }); // overall time will be around 1s
```

때때로 일련의 비동기화된 작업을 실행시키고 이작업 중 한개만 해결되어도 모든 결과를 얻을 수 있는 경우가 있을 수 있습니다. `Promise`는 이러한 시나리오에 `Promise.race`를 static 함수로 제공하고 있습니다:

```ts
var task1 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 1000, 'one');
});
var task2 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 2000, 'two');
});

Promise.race([task1, task2]).then(function(value) {
  console.log(value); // "one"
  // Both resolve, but task1 resolves faster
});
```

### 콜백함수를 promise 바꾸기 

가장 안정적인 방법은 직접 작성하는 것입니다. 예를 들면 `setTimeout`를 promise된 `delay`로 변경하는 것은 매우 쉽습니다:

```ts
const delay = (ms: number) => new Promise(res => setTimeout(res, ms));
```

NodeJS에는 `node 스타일 함수 => promise 리턴 함수`로 변경해주는 함수가 있다는 것을 알아두세요: 

```ts
/** Sample usage */
import fs = require('fs');
import util = require('util');
const readFile = util.promisify1(fs.readFile);
```

[polyfill]:https://github.com/stefanpenner/es6-promise
