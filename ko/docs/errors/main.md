# 일반적인 에러
이 섹션에서는 실제로 개발자가 경험하는 몇 가지 일반적인 오류 코드에 대해 설명합니다.

## TS2304
샘플:
> `Cannot find name ga`

외부 라이브러리 (예: Google 애널리틱스)를 사용하고 있으며 이를 *선언*하지 않았습니다. TypeScript는 *맞춤법 오류*와 *선언하지 않고 변수를 사용하여*와 같이 오류를 알려주고 외부 라이브러리를 포함하기 때문에 *런타임에 사용할 수* 있는 어떤 것에 명시적으로 해야합니다. 추가로 해결하는 방식은 다음에 있습니다.([how to fix it][ambient]).

## TS2307
샘플:
> `Cannot find module 'underscore'`

외부 라이브러리(예, underscore)를 *모듈*([modules][modules])로 사용하고 있고 그것에 대한 명시적인 선언파일이 없다는 것입니다. 추가로 해결하는 방식은 다음에 있습니다.([ambient declarations][ambient]).

## TS1148
샘플:
> Cannot compile modules unless the '--module' flag is provided

다음을 모듈섹션을 확인해보세요. [modules][modules].

## 검색 색인
이것을 읽지 않아도됩니다. 이 섹션은 검색 색인 생성을 위한 섹션입니다.

> 사람들이 사용하는데 오류를 얻는 경향이 있는 다른 모듈들:
* Cannot find name $
* Cannot find module jquery

[ambient]: ../types/ambient/d.ts.md
[modules]: ../project/modules.md
