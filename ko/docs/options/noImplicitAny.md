## noImplicitAny

추측 할 수 없거나 유추하여 예상치 못한 오류가 발생할 수 있는 몇 가지 사항이 있습니다. 좋은 예는 함수 인수입니다. 어노테이션을 달지 않으면 무엇이 유효해야하고 어떤 것이 유효하지 않아야하는지 명확하지 않습니다. 예를들면:

```ts
function log(someArg) {
  sendDataToServer(someArg);
}

// What arg is valid and what isn't?
log(123);
log('hello world');
```

따라서 일부 함수 인수에 어노테이션을 달지 않으면 TypeScript는 `any`라고 가정하고 계속 진행합니다. 이는 기본적으로 JavaScript개발자가 기대하는 것이지만 높은 안전성 때문에 확인를 원하는 사람들을 위해서는 type 검사를 끄는것과 같습니다. 따라서`noImplicitAny` 옵션이 있습니다. 이 옵션을 켜면 type을 유추 할 수없는 경우를 표시합니다. 예를들면:

```ts
function log(someArg) { // Error : someArg has an implicit `any` type
  sendDataToServer(someArg);
}
```

물론 어노테이션을 추가 할 수 있습니다:

```ts
function log(someArg: number) {
  sendDataToServer(someArg);
}
```

그리고 만약 당신이 진정으로 *무결점*의 안전성을 원한다면 *명백하게* `any`로 표시 할 수 있습니다:

```ts
function log(someArg: any) {
  sendDataToServer(someArg);
}
```
