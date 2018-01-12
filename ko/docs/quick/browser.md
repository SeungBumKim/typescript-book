# 브라우저에서의 TypeScript
웹어플리케이션을 만드는데 TypeScript를 사용한다면 여기에 추천할 내용이 있습니다:

## 일반적인 머신 설정

* 설치 [Node.js](https://nodejs.org/en/download/)

## 프로젝트 설정
* 프로젝트 디렉토리 생성:

```
mkdir your-project
cd your-project
```

* `tsconfig.json` 생성합니다. 관련해서는 다음에서 언급했었습니다([modules here](../project/external-modules.md)). 또한 `tsx` 컴파일을 위해 설정해 주셔도 좋습니다:

```json
{
    "compilerOptions": {
        "target": "es5",
        "module": "commonjs",
        "sourceMap": true,
        "jsx": "react"
    },
    "exclude": [
        "node_modules"
    ],
    "compileOnSave": false
}
```

* npm 프로젝트를 생성합니다:

```
npm init -y
```

* 설치 [TypeScript-nightly](https://github.com/Microsoft/TypeScript), [`webpack`](https://github.com/webpack/webpack), [`ts-loader`](https://github.com/TypeStrong/ts-loader/), [`typings`](https://github.com/typings/typings):

```
npm install typescript@next webpack ts-loader typings --save-dev
```

* typings 초기화(`typings.json`파일을 생성합니다.):

```
"./node_modules/.bin/typings" init
```

* 모든 리소스를 담고있는 하나의 `bundle.js`파일로 모듈을 묶는 `webpack.config.js` 파일을 만드십시오:

```js
const path = require('path');

module.exports = {
    entry: './src/app.tsx',
    output: {
        path: path.resolve(__dirname, 'dist'),  
        filename: 'bundle.js',
        publicPath: '/dist/'
    },
    resolve: {
        // Add `.ts` and `.tsx` as a resolvable extension.
        extensions: ['.webpack.js', '.web.js', '.ts', '.tsx', '.js']
    },
    module: {
        loaders: [
            // all files with a `.ts` or `.tsx` extension will be handled by `ts-loader`
            { test: /\.tsx?$/, loader: 'ts-loader' }
        ]
    }
}
```

* 빌드를 위한 npm 스크립트를 설정하세요. `npm install`에서 `typings install`를 실행 시켰습니다. `package.json`의 `script`섹션에 추가하세요:

```json
"scripts": {
    "prepublish": "typings install",
    "watch": "webpack --watch"
},
```

이제 다음과 같이 실행 시키면됩니다.(디렉토리안에 `webpack.config.js`을 포함해서요.):

```
npm run watch
```

이제 `ts` or `tsx`을 편집하면 웹팩은 `bundle.js`을 생성할 것 입니다. 이 것을 웹서브를 위해서 사용하세요🌹.

## 추가
React를 사용하고 있다면(아래 내용을 보기를 추천합니다.), 아래 몇가지 작업을 더 해야합니다:

```
npm install react react-dom --save
```

```
npm i @types/react --save
```

```
npm i @types/react-dom --save
```

`index.html` 데모는:

```
<html>
    <head>
        <meta charset="UTF-8" />
        <title>Hello React!</title>
    </head>
    <body>
        <div id="root"></div>

        <!-- Main -->
        <script src="./dist/bundle.js"></script>
    </body>
</html>
```

`./src/app.tsx` 데모는:

```ts
import * as React from "react";
import * as ReactDOM from "react-dom";

const Hello = (props: { compiler: string, framework: string }) => {
    return (
        <div>
            <div>{props.compiler}</div>
            <div>{props.framework}</div>
        </div>
    );
}

ReactDOM.render(
    <Hello compiler="TypeScript" framework="React" />,
    document.getElementById("root")
);
```

다음에서 데모 프로젝트를 클론받을 수 있습니다: https://github.com/basarat/react-typescript

## 실시간 리로드

웹팩을 개발 서버에 추가하세요. 매우 쉽습니다:

* 설치 : `npm install webpack-dev-server` 
* `package.json`에 추가하세요 : `"start": "webpack-dev-server --hot --inline --no-info"`

`npm start`으로 실행시키면 개발 서버에서 웹팩이 시작되고 실시간으로 리로드 될것입니다. 
