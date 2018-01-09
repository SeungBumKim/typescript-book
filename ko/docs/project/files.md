## 어느 file들을 사용할까?

명시적으로 `files`에 작성하거나:

```json
{
    "files":[
        "./some/file.ts"
    ]
}
```

특정 파일들을 `include` 또는 `exclude` 시킨다. 예를들면:


```json
{
    "include":[
        "./folder"
    ],
    "exclude":[
        "./folder/**/*.spec.ts",
        "./folder/someSubFolder"
    ]
}
```

알아둘 사항: 

* `files`을 작성하면 다른 옵션은 무시된다.
* `**/*`(예를들어 `somefolder/**/*` 식으로 작성하면)의 모든 폴더와 파일들(확장자가 `.ts`/`.tsx`와 `allowJs`값이 true이면 `.js`/`.jsx`도 포함한다.)이라는 의미이다.
