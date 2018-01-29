# 함수의 매개변수

함수에서 많은 매개변수가 있거나, 같은 타입의 매개변수가 있으려면 대신 오브젝트를 가져 오도록 함수를 변경하는 것이 좋습니다.

다음 함수를 보겠습니다:

```ts
function foo(flagA: boolean, flagB: boolean) {
  // your awesome function body 
}
```

이러한 함수 정의를 사용하면 예를들어, `foo(flagB, flagA)`와 같이 잘못 호출되기 매우 쉽습니다. 그리고 이런상황은 컴파일러의 도움도 받을 수 없습니다.

대신 함수에서 오브젝트로 변경하면:

```ts
function foo(config: {flagA: boolean, flagB: boolean}) {
  const {flagA, flagB} = config;
  // your awesome function body 
}
```
이제 함수호출에서 실수를 피하고 코드 검토를 훨씬 쉽게 할 수 있는 `foo({flagA, flagB})`와 같이 호출 할 것입니다. 

> 노트: 함수가 간단하고, 많은 변경이 없을 것 같은 경우에는 위 팁은 무시해도 될 것입니다🌹.
