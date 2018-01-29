## Build Toggles

JavaScript 프로젝트는 실행하는 곳에 따라 전환하는 것이 일반적입니다. 환경변수를 기반으로하는 *비사용 코드 제거*을 지원하므로 webpack을 사용하면이 작업을 매우 쉽게 수행 할 수 있습니다.

`package.json`의  `scripts`에 다른 타겟에 대해서 추가할 수 있습니다:

```json
"build:test": "webpack -p --config ./src/webpack.config.js",
"build:prod": "webpack -p --define process.env.NODE_ENV='\"production\"' --config ./src/webpack.config.js",
```

물론, `npm install webpack --save-dev`되어있다고 가정해습니다. `npm run build:test` 등을 실행시키세요.

변수를 사용하는 것도 매우 쉽습니다:

```ts
/**
 * This interface makes sure we don't miss adding a property to both `prod` and `test`
 */
interface Config {
  someItem: string;
}

/**
 * We only export a single thing. The config.
 */
export let config: Config;

/**
 * `process.env.NODE_ENV` definition is driven from webpack
 *
 * The whole `else` block will be removed in the emitted JavaScript
 *  for a production build
 */
if (process.env.NODE_ENV === 'production') {
  config = {
    someItem: 'prod'
  }
  console.log('Running in prod');
} else {
  config = {
    someItem: 'test'
  }
  console.log('Running in test');
}
```

> `process.env.NODE_ENV`의 사용은 `React`에서와 같이 JavaScript 라이브러리 자체에서 일반적인 것입니다.
