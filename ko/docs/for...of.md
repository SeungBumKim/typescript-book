### for...of
보통 초급 JavaScript 개발자들은 `for...in`에서 배열 항목이 반복되지 않는 에러를 경험한다. 대신 배열의 *키값*들 반복된다. 이런 경우는 아래에 예에서 설명된다. 우리는 `9,2,5`를 기대하지만 실제로는 `0,1,2` 인덱스 값을 얻는다.:

```ts
var someArray = [9, 2, 5];
for (var item in someArray) {
    console.log(item); // 0,1,2
}
```

이것은 TypeScript(와 ES6)에서 `for...of`가 있는 한가지 이유가 된다. 다음 반복문은 정확하게 기대되는 멤버값을 출력해준다:

```ts
var someArray = [9, 2, 5];
for (var item of someArray) {
    console.log(item); // 9,2,5
}
```

유사하게 TypeScript는 `for...of`을 이용해서 문자열의 문자를 출력하는데 문제가 없다:

```ts
var hello = "is it me you're looking for?";
for (var char of hello) {
    console.log(char); // is it me you're looking for?
}
```

#### JS 생성
ES6 이전 버전에 대해서 TypeScript는 `for (var i = 0; i < list.length; i++)`와 같은 표준화된 루프를 생성한다. 예를들어 앞의 예가 어떻게 생성되는지 보여준다:
```ts
var someArray = [9, 2, 5];
for (var item of someArray) {
    console.log(item);
}

// becomes //

for (var _i = 0; _i < someArray.length; _i++) {
    var item = someArray[_i];
    console.log(item);
}
```
위에서 보았듯이 `for...of`을 사용하면 명료하게 *직관적*이고 작성하는 코드(변수이름 등)를 줄일 수 있다.

#### 제한
만약 ES6나 그 이후버전이 타겟이라면, `length`속성이 있다고 가정하고 객체의 인덱스된 번호를 사용한다. 예를들면 `obj[2]`와 같다. 그래서 오직 `string`와 `array`에 대해서만 지원한다.

문자열이나 배열이 아닌 경우에 사용한다면 TypeScript에서는 *"is not an array type or a string type"* 와 같이 에러가 발생한다;
```ts
let articleParagraphs = document.querySelectorAll("article > p");
// Error: Nodelist is not an array type or a string type
for (let paragraph of articleParagraphs) {
    paragraph.classList.add("read");
}
```

`for...of`을 사용할 때 배열이나 문자열에서만 허용된다는 것을 *명심*하면됩니다. 이러한 제한사항은 TypeScript의 후속버전에서는 개선될 것입니다.

#### 요약
배열의 요소를 반복할 때 얼마나 많이 걸리지 알면 놀랄것입니다. 다음번에는 `for...of`을 사용해보세요. 코드를 검토하는 다음 사람을 행복하게 만들 수 있습니다. 
