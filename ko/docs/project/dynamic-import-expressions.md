## 동적으로 표현식 가져오기

**동적으로 표현식 가져오기**는 새로운 기능으로 프로그램의 임의의 부분에서 비동기적으로 모듈을 요청하는 것을 허용하도록한 **ECMAScript**입니다.
**TC39**로 JavaScript 위원회에서는 3단계에 있는 자체 제안서를 가지고 있으며 [import() proposal for JavaScript](https://github.com/tc39/proposal-dynamic-import)라고 부르기로 했습니다.

다른쪽에서는 **웹팩** 번들이 갖고있는 기능으로 번들을 나중에 비동기적으로 다운로드 할 수 있는 청크로 나눌 수 있게 한 것으로 이를 [**Code Splitting**](https://webpack.js.org/guides/code-splitting/)라고 부르기로했습니다. 예를들면 서버에서는 작은 부트스트랩 번들을 보내고 나중에 비동기적으로 추가적으로 필요한 요소를 로딩하는 것입니다.

들으면 (만약 현재 개발에서 웹팩을 사용하고 있다면) 자연스럽게 [TypeScript 2.4 dynamic import expressions](https://github.com/Microsoft/TypeScript/wiki/What%27s-new-in-TypeScript#dynamic-import-expressions)을 사용해서 
자동으로 번들청크를 생성하고 JS 최종번들은 자동으로 코드분할 할거라고 생각할 것입니다. 그러나 **tsconfig.json구성**따라 다르므로 그렇게 쉽지는 않습니다.

웹팩코드 분할을 달성하기 위해 두 가지 유사한 기술을 지원한다는 것입니다. **import()**(선호되는 것으로 ECMAScript에서 제안)이나 **require.ensure()**(레거시방식으로 웹펙의 스펙)입니다. 
The thing is that webpack code splitting supports two similar techniques to achieve this goal: using **import()** (preferred, ECMAScript proposal) and **require.ensure()** (legacy, webpack specific). And what that means is the expected TypeScript output is **leave the import() statement as it is** instead of transpile it to anything else.

Let’s see and example to figure out how to configure webpack + TypeScript 2.4.

In the following code I want to **lazy load the library moment** but I am interested on code splitting as well, which means, having moment library in a separate chunk of JS (javascript file) and that will be loaded only when required.

```ts
import(/* webpackChunkName: "momentjs" */ "moment")
  .then((moment) => {
      // lazyModule has all of the proper types, autocomplete works,
      // type checking works, code references work \o/
      const time = moment().format();
      console.log("TypeScript >= 2.4.0 Dynamic Import Expression:");
      console.log(time);
  })
  .catch((err) => {
      console.log("Failed to load moment", err);
  });
```

Here is the tsconfig.json:

```json
{
    "compilerOptions": {
        "target": "es5",                          
        "module": "esnext",                     
        "lib": [
            "dom",
            "es5",
            "scripthost",
            "es2015.promise"
        ],                                        
        "jsx": "react",                           
        "declaration": false,                     
        "sourceMap": true,                        
        "outDir": "./dist/js",                    
        "strict": true,                           
        "moduleResolution": "node",               
        "typeRoots": [
            "./node_modules/@types"
        ],                                        
        "types": [
            "node",
            "react",
            "react-dom"
        ]                                       
    }
}
```


**Important notes**:

- Using **"module": "esnext"** TypeScript produces the mimic import() statement to be input for Webpack Code Splitting.
- For further information read this article: [Dynamic Import Expressions and webpack 2 Code Splitting integration with TypeScript 2.4](https://blog.josequinto.com/2017/06/29/dynamic-import-expressions-and-webpack-code-splitting-integration-with-typescript-2-4/).


You can see full example [here][dynamicimportcode].

[dynamicimportcode]:../code/dynamic-import-expressions
