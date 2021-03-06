## 동등연산자

javaScript에서 주의해야 할 것은`==`와`===`은 다른다는 것 입니다. JavaScript는 `==`에 대한 프로그래밍 오류에 대해서 탄력적으로 두 변수 사이에 타입 강제 변환을 시도한다. 예를들면, 문자열을 숫자로 변환하여 아래와 같이 숫자와 비교가능하게 해줍니다.

```js
console.log(5 == "5"); // true   , TS Error
console.log(5 === "5"); // false , TS Error
```

그러나 JavaScript의 선택이 항상 이상적인 것은 아닙니다. 예를 들어 아래의 예에서 `""`와 `"0"`은 모두 문자열이고 명확하게 같지 않기 때문에 첫 번째 명령문은 false입니다. 그러나 두 번째 경우에서 `0`과 빈 문자열(`""`)은 모두가 거짓(`false`와 같이 동작함)이므로 `==`에 대해서 동일하다고 판단합니다. 두줄의 명령문에 
`===`를 사용하면 모두 false가 됩니다.

```js
console.log("" == "0"); // false
console.log(0 == ""); // true

console.log("" === "0"); // false
console.log(0 === ""); // false
```

> 노트, `string == number`와 `string === number`는 모두 TypeScript의 컴파일 타임 오류이므로, 이런 문제는 걱정할 필요가 없습니다.

`==` vs. `===`와 유사한 연산자로, `!=` vs. `!==`가 있습니다.

팁: 나중에 다룰 null 체크를 제외하고는 `===`와 `!==`을 항상 사용하세요.
