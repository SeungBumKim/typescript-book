# globals.d.ts

[projects](./modules.md) 에서 *전역*과 *파일*모듈에 대해서 다루었고, 파일기반의 모듈을 사용하는 것을 추천했었습니다. 그리고 전역 네임스페이스를 침범하지 않아야한다고 했습니다.

그럼에도 불구하고, TypeScript의 초급 개발자라면 `globals.d.ts`에 인터페이스/타입을 전역 네임스페이스 설정하고 TypeScript의 *모든*코드에서 *마법같이*어떤 *타입*이든지 쉽게 사용하려고 할 것입니다.
Nevertheless, if you have beginning TypeScript developers you can give them a `globals.d.ts` file to put interfaces / types in the global namespace to make it easy to have some *types* just *magically* available for consumption in *all* your TypeScript code.

> *JavaScript*를 생성하는 코드라면 *파일 모듈*을 사용할 것을 강력 추천합니다.

* 필요하다면 `globals.d.ts`는 `lib.d.ts`에 추가 확장하는 것이 좋습니다.
* TS에서 JS로 마이그레이션 할때 `declare module "some-library-you-dont-care-to-get-defs-for";와 같이 할 때 좋습니다.
