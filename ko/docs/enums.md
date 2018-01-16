### 열거형
열거형은 관련된 값의 모음을 구성하는 방법입니다. 다른 많은 프로그래밍 언어(C/C#/Java)에는 `enum`데이터 유형이 있지만 JavaScript는 없습니다. 그러나 TypeScript에는 있습니다. 이제부터 TypeScript의 열거형의 정의를 예제를 통해서 보겠습니다: 

```ts
enum CardSuit {
	Clubs,
	Diamonds,
	Hearts,
	Spades
}

// Sample usage
var card = CardSuit.Clubs;

// Safety
card = "not a member of card suit"; // Error : string is not assignable to type `CardSuit`
```

#### 열거형과 숫자값
TypeScript의 열거형은 숫자을 기초로합니다. 이말의 의미는 숫자값은 열거형의 인스턴스에 할당가능하고 그러하므로 `number`형과 호환 가능합니다. 

```ts
enum Color {
    Red,
    Green,
    Blue
}
var col = Color.Red;
col = 0; // Effectively same as Color.Red
```

#### 열거형과 문자열
열거형에 대해서 더 살펴보기전에 JavaScript로 생성되는 것을 보겠습니다. 여기에 TypeScript 샘플이 있습니다:

```ts
enum Tristate {
    False,
    True,
    Unknown
}
```
JavaScript를 생성하면 다음과 같습니다:

```js
var Tristate;
(function (Tristate) {
    Tristate[Tristate["False"] = 0] = "False";
    Tristate[Tristate["True"] = 1] = "True";
    Tristate[Tristate["Unknown"] = 2] = "Unknown";
})(Tristate || (Tristate = {}));
```

`Tristate[Tristate["False"] = 0] = "False";` 이부분을 자세히 보겠습니다. `Tristate["False"] = 0`가 스스로 할당하고 존재합니다. 예를들면 `Tristate`의 멤버 `"False"`의 값을 `0`으로 할당합니다. 여기서 참고할 사항은 JavaScript의 할당 연산자의 결과로 할당된 값을 준다는 것입니다(예를들어 `0`의 경우). 그래서 실행할 때 JavaScript는 `Tristate[0] = "False"`로 합니다. 즉, `Tristate` 변수를 사용하여 문자열 버전의 열거형 숫자 또는 숫자 버전의 문자열로 변환 할 수 있습니다. 아래에서 설명하는 내용입니다:

```ts
enum Tristate {
    False,
    True,
    Unknown
}
console.log(Tristate[0]); // "False"
console.log(Tristate["False"]); // 0
console.log(Tristate[Tristate.False]); // "False" because `Tristate.False == 0`
```

#### 열거형에서의 숫자값 관련 변환
기본적으로 열거형은 `0`부터 시작하고 각각의 값은 순서대로 1씩 자동으로 증가합니다. 다음 예제를 보겠습니다:

```ts
enum Color {
    Red,     // 0
    Green,   // 1
    Blue     // 2
}
```

그러나 열거 형 멤버와 관련된 번호는 구체적으로 할당하여 변경할 수 있습니다. 관련 내용은 아래 설명되어있는데, 3으로 시작하면 해당 지점부터 증가합니다:

```ts
enum Color {
    DarkRed = 3,  // 3
    DarkGreen,    // 4
    DarkBlue      // 5
}
```

> 팁: 저는 열거형 값에 대한 안전한 ture값 인지 확인을 할 수 있게 첫 번째 열거형을 `= 1`로 초기화합니다.

#### 열거형은 개방형입니다.
여기에 다시 JavaScript로 생성된 열거형을 보여드리겠습니다: 

```js
var Tristate;
(function (Tristate) {
    Tristate[Tristate["False"] = 0] = "False";
    Tristate[Tristate["True"] = 1] = "True";
    Tristate[Tristate["Unknown"] = 2] = "Unknown";
})(Tristate || (Tristate = {}));
```

`Tristate[Tristate["False"] = 0] = "False";` 의 부분은 앞에서 설명드렸습니다. 이번에는 `(function (Tristate) { /*code here */ })(Tristate || (Tristate = {}));`에서의 `(Tristate || (Tristate = {}));`부분입니다. 이부분은 기본적으로 이미 정의된 `Tristate` 값을 가리키거나 새로운 빈 `{}` 객체로 초기화한 로컬 변수 `TriState`를 캡쳐합니다.

즉, 열거형 정의를 여러 파일로 분할 및 확장 할 수 있습니다. 예를들면, `Color`를 두개의 블럭으로 분할해서 정의한 내용의 예제가 있습니다:

```ts
enum Color {
    Red,
    Green,
    Blue
}

enum Color {
    DarkRed = 3,
    DarkGreen,
    DarkBlue
}
```

주의할 사항은 연속되는 열거형의 첫번째 멤버(여기서는 `DarkRed = 3`)를 이전의 정의된 값(예,`0`, `1`, 이어진 값들)에 겹치지 않게 다시 초기화 *해주어야*합니다. 이사항을 지키지 않으면 TypeScript은 경고할 것입니다.(에러 메시지: `In an enum with multiple declarations, only one declaration can omit an initializer for its first enum element.`).  

#### 플래그로서의 열거형
열거형의 사용하는 훌륭한 방법 중 하나느 `플래그`로 사용하는 것입니다. 플래그를 사용하면 일련의 조건에서 특정 조건이 true인지 확인할 수 있습니다. 동물에 관한 일련의 속성이있는 다음 예제를 고려해보십시오:

```ts
enum AnimalFlags {
    None           = 0,
    HasClaws       = 1 << 0,
    CanFly         = 1 << 1,
    EatsFish       = 1 << 2,
    Endangered     = 1 << 3
}
```

여기서 `1`을 왼쪽 쉬프트연산자를 이용해서 비트를 이동시키면서 `0001`, `0010`, `0100`, `1000`(1,2,4,8 값이 됩니다.) 을 만들고 있습니다. 비트연산자 `|` (or) / `&` (and) / `~` (not)은 아래의 설명과 같이 플래그로서 동작할 때 유용하게 사용될 것입니다:

```ts

enum AnimalFlags {
    None           = 0,
    HasClaws       = 1 << 0,
    CanFly         = 1 << 1,
}

function printAnimalAbilities(animal) {
    var animalFlags = animal.flags;
    if (animalFlags & AnimalFlags.HasClaws) {
        console.log('animal has claws');
    }
    if (animalFlags & AnimalFlags.CanFly) {
        console.log('animal can fly');
    }
    if (animalFlags == AnimalFlags.None) {
        console.log('nothing');
    }
}

var animal = { flags: AnimalFlags.None };
printAnimalAbilities(animal); // nothing
animal.flags |= AnimalFlags.HasClaws;
printAnimalAbilities(animal); // animal has claws
animal.flags &= ~AnimalFlags.HasClaws;
printAnimalAbilities(animal); // nothing
animal.flags |= AnimalFlags.HasClaws | AnimalFlags.CanFly;
printAnimalAbilities(animal); // animal has claws, animal can fly
```

여기에서:
* `|=`는 플래그에 추가하는 것이고
*  `&=`는 플래그를 동일한것만 합치고 `~`은 플래그를 초기화합니다.
* `|` 플래그를 합칩니다.

> 노트: 플래그를 결합하여 enum 정의 내에서 편리한 바로 가기를 만들 수 있습니다. 예를들어, 아래에 `EndangeredFlyingClawedFishEating`을 보면: 

```ts
enum AnimalFlags {
	None           = 0,
    HasClaws       = 1 << 0,
    CanFly         = 1 << 1,
    EatsFish       = 1 << 2,
    Endangered     = 1 << 3,

	EndangeredFlyingClawedFishEating = HasClaws | CanFly | EatsFish | Endangered,
}
```

#### 열거헝의 상수

만약 열거형의 정의를 다음과 같이 갖는다면:

```ts
enum Tristate {
    False,
    True,
    Unknown
}

var lie = Tristate.False;
```
`var lie = Tristate.False`가 JavaScript로 컴파일되면 `var lie = Tristate.False`(네, 동일한 결과가 나옵니다.)가 됩니다. 즉, 실행시키면 런타임에 `Tristate`을 찾고 `Tristate.False`을 한다는 것입니다. 부팅 속도를 높이려면 `enum`을 `const enum`로 하면됩니다. 아래 설명을 보겠습니다:

```ts
const enum Tristate {
    False,
    True,
    Unknown
}

var lie = Tristate.False;
```

JavaScript로 생성하면:

```js
var lie = 0;
```

예를들어, 컴파일러에서:
1. 열거형의 사용(`Tristate.False`의 `0`)은 코드로 *인라인*화 됩니다.
1. 열거형의 정의는 JavaScript로 생성되지 않으며 (런타임에 `Tristate`은 없습니다.) 인라인화되어 사용됩니다.

##### Const enum preserveConstEnums
인라인시키면 동작속도에는 효과적입니다. 
Inlining has obvious performance benefits. The fact that there is no `Tristate` variable at runtime is simply the compiler helping you out by not generating JavaScript that is not actually used at runtime. However you might want the compiler to still generate the JavaScript version of the enum definition for stuff like *number to string* or *string to number* lookups as we saw. In this case you can use the compiler flag `--preserveConstEnums` and it will still generate the `var Tristate` definition so that you can use `Tristate["False"]` or `Tristate[0]` manually at runtime if you want. This does not impact *inlining* in any way.

### Enum with static functions
You can use the declaration `enum` + `namespace` merging to add static methods to an enum. The following demonstrates an example where we add a static member `isBusinessDay` to an enum `Weekday`:

```ts
enum Weekday {
	Monday,
	Tuesday,
	Wednesday,
	Thursday,
	Friday,
	Saturday,
	Sunday
}
namespace Weekday {
	export function isBusinessDay(day: Weekday) {
		switch (day) {
			case Weekday.Saturday:
			case Weekday.Sunday:
				return false;
			default:
				return true;
		}
	}
}

const mon = Weekday.Monday;
const sun = Weekday.Sunday;
console.log(Weekday.isBusinessDay(mon)); // true
console.log(Weekday.isBusinessDay(sun)); // false
```
