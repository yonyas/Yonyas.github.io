---
title: 'Typescript eslintrc.json 살펴보기'
date: 2021-10-10
category: 'etc'
draft: false
---

## plugins

```json
"plugins": [
	"@typescript-eslint", "jsdoc", "no-null", "import"
],

        // eslint-plugin-jsdoc
        "jsdoc/check-alignment": "error",

        // eslint-plugin-no-null
        "no-null/no-null": "error",

        // eslint-plugin-import
        "import/no-extraneous-dependencies": ["error", { "optionalDependencies": false }],

```

1. @typesript-eslint : TS를 ESLint에서 사용할 수 있도록 ESTree-compatible form으로 변환한다.
2. jsdoc/check-alignment : /\*\* \*/ 의 블록주석을 정렬을 맞게 수정한다.
3. no-null : null 대신 undefined를 사용한다.
4. import : package.json에 선언되지 않은 외부패키지 사용을 금지한다.

## env

어느 환경에서 스크립트가 동작할지 설정한다. 그 환경에 맞는 전역변수를 사용할 수 있다.

```json
 "env": {
        "browser": false,
        "node": true,
        "es6": true
    },

```

## parserOptions

```json
  "parser": "@typescript-eslint/parser",
    "parserOptions": {
        // TS가 명시적으로 지원되는 버전이 아니라면 error를 내는데 이것을 막는다.
        "warnOnUnsupportedTypeScriptVersion": false,

        //eslint는 기본적으로 ECMAScript 5 를 지원하기 떄문에 version: 6 명시
        "ecmaVersion": 6,

        // sourceType은 parser의 export 형태를 설정
        // default 는 script, ECMAScript modules 이면 module
        "sourceType": "module"
    },

```

## rules

(추가중)

```json
 "rules": {
    /* overload member들을 묶어준다.
    **from**
    type Foo = {
        foo(s: string): void;
        foo(n: number): void;
        bar(): void;
        foo(sn: string | number): void;
    };
    **to**
    type Foo = {
        foo(s: string): void;
        foo(n: number): void;
        foo(sn: string | number): void;
        bar(): void;
    };
    */
    "@typescript-eslint/adjacent-overload-signatures": "error",

    /*
    **stroustrup**
    if (foo) {
        bar();
    }          <-- enter
    else {
        baz();
    }
    */
    "brace-style": "off",
    "@typescript-eslint/brace-style": ["error", "stroustrup", { "allowSingleLine": true }],

    // enforce using interfaces for object type definitions.
    "@typescript-eslint/consistent-type-definitions": ["error", "interface"],

    /* there are two kind of type assertion 1. as, 2. angle-braket
    ** as **
    let x = 'hi' as const;

    ** angle-bracket **
    let x = <const>'hi';
    */
    "@typescript-eslint/consistent-type-assertions": ["error", { "assertionStyle": "as" }],

    /*
    no-duplicate-imports rule
    ** from **
    import { merge } from 'module';
    import { find } from 'module';

    ** to **
    import { merge, find } from 'module';
    */
    "no-duplicate-imports": "off",
    "@typescript-eslint/no-duplicate-imports": "error",

    /*
    추론될 수 있는 타입에 굳이 타입정의 하지 말기
    ** from **
    const a: number = 10;

    ** to **
    const a = 10;
    */
    "@typescript-eslint/no-inferrable-types": "error",

    //변수에 this 할당 금지
    "@typescript-eslint/no-this-alias": "error",

    //n + 1; is not a syntax error, but it might be a typing mistake where a programmer meant an assignment statement n += 1; instead
    "no-unused-expressions": "off",
    "@typescript-eslint/no-unused-expressions": ["error", { "allowTernary": true }],

    /* 인덱스가 읽기에만 쓰일 때 for문보다 of를 사용.
    ** from **
    for (let i = 0; i < arr.length; i++) {
        console.log(arr[i]);
    }

    ** to **
    for (const x of arr) {
        console.log(x);
    }
    */
    "@typescript-eslint/prefer-for-of": "error",

    //TS v1.5 이전에는 internal-module, 이후에는 namespace로 변경되었다. namespace keyword 사용 강제해서 헷갈리지 않도록 함
    "@typescript-eslint/prefer-namespace-keyword": "error",

    //use double quote
    "quotes": "off",
    "@typescript-eslint/quotes": ["error", "double", { "avoidEscape": true, "allowTemplateLiterals": true }],

    //require semicolons instead of ASI
    "semi": "off",
    "@typescript-eslint/semi": "error",

    /*
    함수 괄호 전에 공백 주기
    ** from **
    function withoutSpace(x) {
        // ...
    }

    ** to **
    function withSpace (x) {
        // ...
    }
    */
    "space-before-function-paren": "off",
    "@typescript-eslint/space-before-function-paren": ["error", {
        "asyncArrow": "always",
        "anonymous": "always",
        "named": "never"
    }],

    // /// 대신 import 사용
    "@typescript-eslint/triple-slash-reference": "error",

    /*
    타입 지정 문자 전 띄어쓰기
    ** from **
    let foo:string = "bar";
    let foo :string = "bar";
    let foo : string = "bar";

    ** to **
    let foo: string = "bar";
    */
    "@typescript-eslint/type-annotation-spacing": "error",

    /*
    하나로 통합될 수 있는 타입지정 경고
    ** from **
    function f(x: number): void;
    function f(x: string): void;

    ** to **
    function f(x: number | string): void;
    */
    "@typescript-eslint/unified-signatures": "error",
```

## eslint 적용해보는 사이트

[ESLint Demo - ESLint - Pluggable JavaScript linter](https://eslint.org/demo#eyJ0ZXh0IjoidmFyIGZvbyA9IGJhcjtcbmNvbnN0IGEgPSAwO1xuYSA9IG51bGxcbiIsIm9wdGlvbnMiOnsicGFyc2VyT3B0aW9ucyI6eyJlY21hVmVyc2lvbiI6MTIsInNvdXJjZVR5cGUiOiJzY3JpcHQiLCJlY21hRmVhdHVyZXMiOnt9fSwicnVsZXMiOnsiY29uc3RydWN0b3Itc3VwZXIiOjIsImZvci1kaXJlY3Rpb24iOjIsImdldHRlci1yZXR1cm4iOjIsIm5vLWFzeW5jLXByb21pc2UtZXhlY3V0b3IiOjIsIm5vLWNhc2UtZGVjbGFyYXRpb25zIjoyLCJuby1jbGFzcy1hc3NpZ24iOjIsIm5vLWNvbXBhcmUtbmVnLXplcm8iOjIsIm5vLWNvbmQtYXNzaWduIjoyLCJuby1jb25zdC1hc3NpZ24iOjIsIm5vLWNvbnN0YW50LWNvbmRpdGlvbiI6Miwibm8tY29udHJvbC1yZWdleCI6Miwibm8tZGVidWdnZXIiOjIsIm5vLWRlbGV0ZS12YXIiOjIsIm5vLWR1cGUtYXJncyI6Miwibm8tZHVwZS1jbGFzcy1tZW1iZXJzIjoyLCJuby1kdXBlLWVsc2UtaWYiOjIsIm5vLWR1cGUta2V5cyI6Miwibm8tZHVwbGljYXRlLWNhc2UiOjIsIm5vLWVtcHR5IjoyLCJuby1lbXB0eS1jaGFyYWN0ZXItY2xhc3MiOjIsIm5vLWVtcHR5LXBhdHRlcm4iOjIsIm5vLWV4LWFzc2lnbiI6Miwibm8tZXh0cmEtYm9vbGVhbi1jYXN0IjoyLCJuby1leHRyYS1zZW1pIjoyLCJuby1mYWxsdGhyb3VnaCI6Miwibm8tZnVuYy1hc3NpZ24iOjIsIm5vLWdsb2JhbC1hc3NpZ24iOjIsIm5vLWltcG9ydC1hc3NpZ24iOjIsIm5vLWlubmVyLWRlY2xhcmF0aW9ucyI6Miwibm8taW52YWxpZC1yZWdleHAiOjIsIm5vLWlycmVndWxhci13aGl0ZXNwYWNlIjoyLCJuby1taXNsZWFkaW5nLWNoYXJhY3Rlci1jbGFzcyI6Miwibm8tbWl4ZWQtc3BhY2VzLWFuZC10YWJzIjoyLCJuby1uZXctc3ltYm9sIjoyLCJuby1vYmotY2FsbHMiOjIsIm5vLW9jdGFsIjoyLCJuby1wcm90b3R5cGUtYnVpbHRpbnMiOjIsIm5vLXJlZGVjbGFyZSI6Miwibm8tcmVnZXgtc3BhY2VzIjoyLCJuby1zZWxmLWFzc2lnbiI6Miwibm8tc2V0dGVyLXJldHVybiI6Miwibm8tc2hhZG93LXJlc3RyaWN0ZWQtbmFtZXMiOjIsIm5vLXNwYXJzZS1hcnJheXMiOjIsIm5vLXRoaXMtYmVmb3JlLXN1cGVyIjoyLCJuby11bmRlZiI6Miwibm8tdW5leHBlY3RlZC1tdWx0aWxpbmUiOjIsIm5vLXVucmVhY2hhYmxlIjoyLCJuby11bnNhZmUtZmluYWxseSI6Miwibm8tdW5zYWZlLW5lZ2F0aW9uIjoyLCJuby11bnVzZWQtbGFiZWxzIjoyLCJuby11bnVzZWQtdmFycyI6Miwibm8tdXNlbGVzcy1jYXRjaCI6Miwibm8tdXNlbGVzcy1lc2NhcGUiOjIsIm5vLXdpdGgiOjIsInJlcXVpcmUteWllbGQiOjIsInVzZS1pc25hbiI6MiwidmFsaWQtdHlwZW9mIjoyfSwiZW52Ijp7fX19)

## 참고

1. [ESLint - Pluggable JavaScript linter](https://eslint.org/)
2. [eslint-plugin-jsdoc - npm](https://www.npmjs.com/package/eslint-plugin-jsdoc#eslint-plugin-jsdoc-rules-check-alignment)
3. [eslint-plugin-no-null - npm](https://www.npmjs.com/package/eslint-plugin-no-null)
4. [eslint-plugin-import - npm](https://www.npmjs.com/package/eslint-plugin-import)
5. [@typescript-eslint/parser - npm](https://www.npmjs.com/package/@typescript-eslint/parser)
