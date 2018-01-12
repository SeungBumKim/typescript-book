#  Node.js에서의 TypeScript
TypeScript는 처음부터 *최우선으로* Node.js를 지원합니다. 여기서는 어떻게 Node.js의 프로젝트를 빠르게 셋업하는지는 살펴보겠습니다.:

> 노트: 이 단계 중 많은 부분이 실제로는 Node.js설정 단계에 불과합니다.

1. `package.json`에 Node.js 프로젝트를 설정합니다. (`npm init -y`)
1. 타입 스크립트를 추가합니다.(`npm install typescript --save-dev`)
1. `node.d.ts`를 추가합니다.(`npm install @types/node --save-dev`)
1. TypeScript의 옵션을 위한 `tsconfig.json`을 초기화합니다.(`node ./node_modules/typescript/lib/tsc --init`)
1. tsconfig.json에는 `compilerOptions.module:commonjs`내용이 있어야합니다.fig.json

이거면 됩니다. IDE를 띄우고(예. `alm -o`) 이것저것 해보세요. 이제 모든 내장형 노드모듈(e.g. `import fs = require('fs');`)을 안정적이고 TypeScript의 개발철학에 맞게 사용할 수 있습니다!

## 보너스: 실시간 컴파일 + 실행
* `ts-node`을 추가하면 실시간 컴파일 + 실행을 노드에서 할 수 있습니다.(`npm install ts-node --save-dev`)
* `nodemon`을 추가하면 파일이 변경될 때마다 `ts-node`을 호출 할 수 있습니다.(`npm install nodemon --save-dev`)

`package.json`에 어플리케이션 기반으로 대상을 `script` 타겟으로 기입해보면(예를들면 index.ts라고 가정해보면):

```json
  "scripts": {
    "start": "npm run build:live",
    "build:live": "nodemon --exec ./node_modules/.bin/ts-node -- ./index.ts"
  },
```

이제 `npm start`를 실행하고 `index.ts`를 편집하면:

* nodemon 해당 명령을 재실행 시켜줍니다.(ts-node)
* ts-node는 tsconfig.json와 설치된 typescript버전을 자동으로 읽어오고,
* ts-node는 Node.js통해서 javascript결과를 실행합니다.

## TypeScript node모듈 생성하기

* 다음 링크를 보세요: [A lesson on creating typescript node modules](https://egghead.io/lessons/typescript-create-high-quality-npm-packages-using-typescript)

작성된 모듈을 TypeScript에서 사용하면 컴파일 할때 안전성과 자동완성(필수적인 실행 문서)이 뛰어나므로 매우 편리할 것입니다.

좋은 TypeScript의 모듈을 만드는 것은 간단합니다. 패키지들에 대해서 다음과 같은 폴더 구조를 가정해보겠습니다:

```text
package
├─ package.json
├─ tsconfig.json
├─ src
│  ├─ All your source files
│  ├─ index.ts
│  ├─ foo.ts
│  └─ ...
└─ lib
  ├─ All your compiled files
  ├─ index.d.ts
  ├─ index.js
  ├─ foo.d.ts
  ├─ foo.js
  └─ ...
```


* `tsconfig.json` 안에는
  * `compilerOptions`: `"outDir": "lib"`와 `"declaration": true`포함되어 있어야하고 (이러한 내용은 lib 폴더에 선언 및 js 파일이 생성됩니다.)
  * `include: ["./src/**/*]"`도 포함해야합니다. (의미는 `src`이하 모든 파일은 모두 포함한다는 것입니다.)

* `package.json` 안에는
  * `"main": "lib/index"` (Node.js에게 `lib/index.js`을 로드하라고 알립니다.)
  * `"types": "lib/index"` (TypeScript에게 `lib/index.d.ts`을 로드하라고 알립니다.)


예제 패키지:
* `npm install typestyle` [for TypeStyle](https://www.npmjs.com/package/typestyle)
* 사용: `import { style } from 'typestyle';` 타입 안전성을 갖게 될 것입니다.

추가:

* 만약 다른 TypeScript로 만들어진 패키지에 의존성을 갖는다면 JS로우 패키지를 사용하는 것  `dependencies`/`devDependencies`/`peerDependencies`안에 넣어주세요.
* 만약 다른 JavaScript로 만들어진 패키지에 의존성을 갖고 타입 안정적으로 사용하고 싶다면, `devDependencies`안에 `@types/foo`와 같이 타입을 넣어주세요. JavaScript 타입은 기본 NPM 스트림에서 *밖*에서 관리되어야 합니다. JavaScript에서는 일반적으로 의미론적 버전 지정이 없는 타입을 없앨 것이고, 그래서 만약 사용자가 이것들을 위해 타입을 필요로한다면 그것들을 위해 작동하는 `@types/foo` 버전을 설치해야합니다.
 
## 보너스 포인트

이러한 NPM 모듈은 browserify(tsify 사용) 또는 웹팩(ts-loader 사용)으로 정상적으로 작동합니다.
