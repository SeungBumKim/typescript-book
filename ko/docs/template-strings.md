### Template Strings
구문적으로 따옴표(')나 쌍따옴표(")대신 백틱을 사용하는 문자열입니다. 템플릿 문자열의 세가지 주요기능이 있습니다:

* 문자열 보간
* 다중라인 문자열
* 태그된 Templates

#### 문자열 보간
일반적으로 사용하는 방식은 고정된 문자열에 어떤 변수를 더해 어떠한 문자열을 생성할때 사용할 수 있습니다. 사용하기 위해서는 *templating logic*과 이름이 정의된 *template strings*이 필요합니다. 예전에 html 문자열을 만드는 것입니다:

```ts
var lyrics = 'Never gonna give you up';
var html = '<div>' + lyrics + '</div>';
```
template strings로는 아래와 같이만 하면됩니다:

```ts
var lyrics = 'Never gonna give you up';
var html = `<div>${lyrics}</div>`;
```

보간 (`${` and `}`)안의 placeholder는 JavaScript의 표현식으로 취급되고 예를들면 멋진 수학을 하는것처럼 다뤄집니다.
```ts
console.log(`1 and 1 make ${1 + 1}`);
```

#### 다중라인 문자열
JavaScript 문자열에 줄바꿈을 넣으려면 어떻게 해야할까요? 아마도 줄바꿈을 넣으려고 할겁니다. 자주 사용되는 *줄바꿈 문자를 벗어나는* `\`를 사용해서 하고 다음줄에 수동으로 줄바꿈 `\n`을 넣어 줍니다. 해당 내용은 아래에 볼 수 있습니다.

```ts
var lyrics = "Never gonna give you up \
\nNever gonna let you down";
```

TypeScript에서는 template string으로 아래와 같이 사용하면됩니다.

```ts
var lyrics = `Never gonna give you up
Never gonna let you down`;
```

#### 태그된 Templates

template string 앞에 함수(`tag`라고 함)를 배치 할 수 있으며 template string 문장과 모든 placeholder 표현식의 값을 미리 처리하고 결과를 리턴 할 수 있습니다. 몇가지 살펴보면:
* 모든 고정된 문자들은 첫번재 인자의 배열로 넘어 갑니다.
* 모든 placeholders의 값들은 나머지 인자로 넘어 갑니다. 일반적으로는 나머지 매개변수를 사용하여 배열로 변환 할 수도 있습니다.

여기 모든 placeholders로부터 html를 처리하는 태그 함수(`htmlEscape` 정의된)가 예로 있습니다.
Here is an example where we have a tag function (named `htmlEscape`) that escapes the html from all the placeholders:

```ts
var say = "a bird in hand > two in the bush";
var html = htmlEscape `<div> I would just like to say : ${say}</div>`;

// a sample tag function
function htmlEscape(literals, ...placeholders) {
    let result = "";

    // interleave the literals with the placeholders
    for (let i = 0; i < placeholders.length; i++) {
        result += literals[i];
        result += placeholders[i]
            .replace(/&/g, '&amp;')
            .replace(/"/g, '&quot;')
            .replace(/'/g, '&#39;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;');
    }

    // add the last literal
    result += literals[literals.length - 1];
    return result;
}
```

#### JS 생성
ES6 이전 컴파일 대상의 코드는 상당히 단순합니다. 다중라인 문자열은 줄바꿈 처리된 문자열이 됩니다. 문자열 보간은 *문자열 연결*이 됩니다. 태그된 Templates은 함수호출 방식으로 됩니다.
For pre ES6 compile targ ets the c ode is fairly simple. Multiline strings become escaped strings. String interpolation becomes *string concatenation*. Tagged Templates become function calls.

#### 요약
다중라인 문자열과 문자열 보간은 어떤언어에서라도 상당히 좋은 기능이 될 것입니다. 지금 JavaScript에서 사용할 수 있다는 점은 매우 좋습니다.(TypeScript 고마워요!). 태그된 templates 강력한 문자열 유틸리티를 만들 수 있습니다.
Multiline strings and string interpolation are just great things to have in any language. It's great that you can now use them in your JavaScript (thanks TypeScript!). Tagged templates allow you to create powerful string utilities.
