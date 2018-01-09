### 기본틀
필요한 기본 파일로 tsconfig.json을 시작하는 것이 매우 쉽습니다:
```json
{}
```
예를들면, 프로젝트의 *루트*(디렉토리) 빈 JSON파일이 있다면 TypeScript는 현재 디렉토리(하위 디렉토리도)의 *모든* `.ts`파일을 컴파일 컨텍스트에 포함할 것입니다. 또한 몇 가지 기본 컴파일러 옵션을 선택할 것입니다.

### 컴파일러 옵션
`compilerOptions`을 사용해서 사용자 컴파일 옵션을 지정할 수 있습니다:

```json
{
  "compilerOptions": {

    /* Basic Options */                       
    "target": "es5",                       /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', or 'ESNEXT'. */
    "module": "commonjs",                  /* Specify module code generation: 'commonjs', 'amd', 'system', 'umd' or 'es2015'. */
    "lib": [],                             /* Specify library files to be included in the compilation:  */
    "allowJs": true,                       /* Allow javascript files to be compiled. */
    "checkJs": true,                       /* Report errors in .js files. */
    "jsx": "preserve",                     /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
    "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    "sourceMap": true,                     /* Generates corresponding '.map' file. */
    "outFile": "./",                       /* Concatenate and emit output to single file. */
    "outDir": "./",                        /* Redirect output structure to the directory. */
    "rootDir": "./",                       /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
    "removeComments": true,                /* Do not emit comments to output. */
    "noEmit": true,                        /* Do not emit outputs. */
    "importHelpers": true,                 /* Import emit helpers from 'tslib'. */
    "downlevelIteration": true,            /* Provide full support for iterables in 'for-of', spread, and destructuring when targeting 'ES5' or 'ES3'. */
    "isolatedModules": true,               /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */
                                              
    /* Strict Type-Checking Options */        
    "strict": true,                        /* Enable all strict type-checking options. */
    "noImplicitAny": true,                 /* Raise error on expressions and declarations with an implied 'any' type. */
    "strictNullChecks": true,              /* Enable strict null checks. */
    "noImplicitThis": true,                /* Raise error on 'this' expressions with an implied 'any' type. */
    "alwaysStrict": true,                  /* Parse in strict mode and emit "use strict" for each source file. */
                                              
    /* Additional Checks */                   
    "noUnusedLocals": true,                /* Report errors on unused locals. */
    "noUnusedParameters": true,            /* Report errors on unused parameters. */
    "noImplicitReturns": true,             /* Report error when not all code paths in function return a value. */
    "noFallthroughCasesInSwitch": true,    /* Report errors for fallthrough cases in switch statement. */
                                              
    /* Module Resolution Options */           
    "moduleResolution": "node",            /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    "baseUrl": "./",                       /* Base directory to resolve non-absolute module names. */
    "paths": {},                           /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    "rootDirs": [],                        /* List of root folders whose combined content represents the structure of the project at runtime. */
    "typeRoots": [],                       /* List of folders to include type definitions from. */
    "types": [],                           /* Type declaration files to be included in compilation. */
    "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
                                              
    /* Source Map Options */                  
    "sourceRoot": "./",                    /* Specify the location where debugger should locate TypeScript files instead of source locations. */
    "mapRoot": "./",                       /* Specify the location where debugger should locate map files instead of generated locations. */
    "inlineSourceMap": true,               /* Emit a single file with source maps instead of having a separate file. */
    "inlineSources": true,                 /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */
                                              
    /* Experimental Options */                
    "experimentalDecorators": true,        /* Enables experimental support for ES7 decorators. */
    "emitDecoratorMetadata": true          /* Enables experimental support for emitting type metadata for decorators. */
  }
}
```

여기(또는 추가로 있는)컴파일 옵션들은 뒤의 챕터에서 더 다룰 것 입니다.

### TypeScript 컴파일러
좋은 IDE들은 `ts`에서 `js`로의 컴파일을 지원하는 기능이 포함되어 있습니다. 그러나 `tsconfig.json`을 사용해서 커맨드라인에서 TypeScript 컴파일러를 수동으로 실행하는 방법이 몇 가지 있습니다.
* `tsc`를 실행시키면 여기서 현재 디렉토리 뿐 아니라 상위 폴더에서까지 찾은 `tsconfig.json`를 찾을 것입니다.
* `tsc -p ./path-to-project-directory`로 실행시킵니다. 물론 경로는 절대/상대경로여야 합니다. 

 TypeScript 컴파일러를 *지켜보기* 모드인 `tsc -w`를 이용해서 시작할 수 있습니다. 이를 통해서 TypeScript 프로젝트의 파이들이 변경되는 사항을 살펴볼 수 있습니다.
