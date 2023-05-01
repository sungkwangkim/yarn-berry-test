# 1-2주차 prettier, eslint 설정 공통화

<br /><br />

## 01. root에서 `prettier`, `eslint` 설치

```shell
// root에서...

yarn add prettier eslint eslint-config-prettier eslint-plugin-import eslint-plugin-react eslint-plugin-react-hooks eslint-import-resolver-typescript @typescript-eslint/eslint-plugin @typescript-eslint/parser -D


yarn dlx @yarnpkg/sdks
```

`.vscode/extensions.json` 에 익스텐션이 추가 확인

![스크린샷 2022-12-07 16 45 14](https://user-images.githubusercontent.com/61961190/206150428-c90f79ec-879d-4392-8fcf-e4dcc114f9b2.png)

<br /><br />

## 02. vscode 익스텐션 설치

- `esbenp.prettier-vscode`
- `dbaeumer.vscode-eslint`

![스크린샷 2022-12-07 19 11 12](https://user-images.githubusercontent.com/61961190/206151023-639da36f-0542-45a3-8424-1d479f18b62e.png)

![스크린샷 2022-12-07 16 43 59](https://user-images.githubusercontent.com/61961190/206150497-eda74dd2-b3ab-4e3f-a8d6-c4f9be478d32.png)

`.vscode/extensions.json` 추가되면 위 그림에 보이는 대로
`이 확장은 현재 작업 영역의 사용자가 권장한 항목입니다`표시 됩니다.

<br /><br />

## 03. `.prettierrc`생성 및 `.vscode/settings.json` 설정

### root 디렉토리에 `.prettierrc` 파일 생성

```json
{
  "arrowParens": "avoid",
  "bracketSameLine": false,
  "bracketSpacing": true,
  "endOfLine": "lf",
  "jsxSingleQuote": false,
  "printWidth": 120,
  "proseWrap": "preserve",
  "quoteProps": "as-needed",
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5"
}
```

<br /><br />

### `.vscode/settings.json` 파일에 내용 추가

```json
{
  "search.exclude": {
    "**/.yarn": true,
    "**/.pnp.*": true
  },
  "typescript.tsdk": ".yarn/sdks/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true,
  "eslint.nodePath": ".yarn/sdks",
  "prettier.prettierPath": ".yarn/sdks/prettier/index.js",

  // 기본 포맷터 prettier로 사용
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  // 파일 저장시 포맷더 실행
  "editor.formatOnSave": true,
  "editor.rulers": [120]
}
```

### ✅ `prettier` 동작 확인!

![prettier-test](https://user-images.githubusercontent.com/61961190/206151388-ef0705b4-40ce-432f-baf0-4a999e3d4fc4.gif)

<br /> <br />

## 04. root에 `.eslintrc.js` 파일 추가

root에 `.eslintrc.js` 파일 생성 후 아래 내용 추가

<details>
<summary>토글 접기/펼치기</summary>

```javascript
module.exports = {
  root: true,

  env: {
    es6: true,
    node: true,
    browser: true,
  },

  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaFeatures: { jsx: true },
  },

  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "prettier",
  ],
  plugins: ["@typescript-eslint", "import", "react", "react-hooks"],
  settings: {
    "import/resolver": { typescript: {} },
    react: { version: "detect" },
  },
  rules: {
    "no-implicit-coercion": "error",
    "no-warning-comments": [
      "warn",
      {
        terms: ["TODO", "FIXME", "XXX", "BUG"],
        location: "anywhere",
      },
    ],
    curly: ["error", "all"],
    eqeqeq: ["error", "always", { null: "ignore" }],

    // Hoisting을 전략적으로 사용한 경우가 많아서
    "@typescript-eslint/no-use-before-define": "off",
    // 모델 정의 부분에서 class와 interface를 합치기 위해 사용하는 용법도 잡고 있어서
    "@typescript-eslint/no-empty-interface": "off",
    // 모델 정의 부분에서 파라미터 프로퍼티를 잘 쓰고 있어서
    "@typescript-eslint/explicit-function-return-type": "off",
    "@typescript-eslint/no-parameter-properties": "off",
    "@typescript-eslint/no-var-requires": "warn",
    "@typescript-eslint/no-non-null-asserted-optional-chain": "warn",
    "@typescript-eslint/no-inferrable-types": "warn",
    "@typescript-eslint/no-empty-function": "off",
    "@typescript-eslint/naming-convention": [
      "error",
      {
        format: ["camelCase", "UPPER_CASE", "PascalCase"],
        selector: "variable",
        leadingUnderscore: "allow",
      },
      { format: ["camelCase", "PascalCase"], selector: "function" },
      { format: ["PascalCase"], selector: "interface" },
      { format: ["PascalCase"], selector: "typeAlias" },
    ],
    "@typescript-eslint/explicit-module-boundary-types": "off",
    "@typescript-eslint/array-type": ["error", { default: "array-simple" }],
    "@typescript-eslint/no-unused-vars": [
      "error",
      { ignoreRestSiblings: true },
    ],
    "@typescript-eslint/member-ordering": [
      "error",
      {
        default: [
          "public-static-field",
          "private-static-field",
          "public-instance-field",
          "private-instance-field",
          "public-constructor",
          "private-constructor",
          "public-instance-method",
          "private-instance-method",
        ],
      },
    ],

    "import/order": [
      "error",
      {
        groups: [
          "builtin",
          "external",
          "internal",
          "parent",
          "sibling",
          "index",
          "object",
        ],
        alphabetize: { order: "asc", caseInsensitive: true },
      },
    ],

    "react/prop-types": "off",
    // React.memo, React.forwardRef에서 사용하는 경우도 막고 있어서
    "react/display-name": "off",
    "react-hooks/exhaustive-deps": "error",
    "react/react-in-jsx-scope": "off",
    "react/no-unknown-property": ["error", { ignore: ["css"] }],
  },
};
```

</details>

<br /><br />

## 05. `.vscode/settings.json` eslint 설정 추가

```json
"eslint.nodePath": ".yarn/sdks",

// 아래 내용추가
"eslint.packageManager": "yarn",
"eslint.validate": ["javascript", "javascriptreact", "typescript", "typescriptreact"],
```

<br /><br />

## 06. `apps/wanted/.eslintrc.json` 파일 삭제.

![스크린샷 2022-12-07 17 26 06](https://user-images.githubusercontent.com/61961190/206151698-5bbac479-a6d1-4f02-af36-7960700124dc.png)

<br /><br />

## 07. `apps/wanted/pages/index.tsx` eslint 동작 확인.

![스크린샷 2022-12-07 17 26 46](https://user-images.githubusercontent.com/61961190/206151778-94b59cc6-0977-4908-b90f-571bb60d5196.png)

<br /><br />

eslint가 정상적으로 동작이 안되면 eslint 서버를 재시작 해본다.

1. ⌨️ `command + shift + p`
2. `ESLint: Restart EsLint Server` 선택

![스크린샷 2022-12-07 17 28 38](https://user-images.githubusercontent.com/61961190/206151845-03bd97cb-bccb-4b37-bd15-c9701410ce51.png)

### ✅ `eslint` 동작 확인!

![eslint](https://user-images.githubusercontent.com/61961190/206153095-ce39e0bc-37d2-414c-993c-d3455659af31.gif)

<br /><br />

## 🗽 (추가) ESLint 설정 공유하기

eslint 프로젝트를 만들어서 사용하고 싶다면 아래 링크를 확인해 주세요.

- https://tech.kakao.com/2019/12/05/make-better-use-of-eslint/

<br /><br /><br /><br />

---

# 1-2주차 tsconfig 설정 공유하기

## 01. root 디렉토리에 `tsconfig.base.json` 파일 생성

```json
{
  "compilerOptions": {
    "strict": true,
    "useUnknownInCatchVariables": true,
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "incremental": true,
    "newLine": "lf"
  },
  "exclude": ["**/node_modules", "**/.*/"]
}
```

![스크린샷 2022-12-07 14 39 01](https://user-images.githubusercontent.com/61961190/206114497-7a0841be-7e8e-4f0b-9b35-013829e9dbe4.png)

<br /><br /><br />

## 02. `apps/wanted/tsconfig.json` 내용추가

```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "baseUrl": "./src",
    "target": "esnext",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "jsx": "preserve",
    "incremental": true,
    "paths": {
      "@/*": ["./*"]
    }
  },
  "exclude": ["**/node_modules", "**/.*/"],
  "include": [
    "next-env.d.ts",
    "**/*.ts",
    "**/*.tsx",
    "**/*.mts",
    "**/*.js",
    "**/*.cjs",
    "**/*.mjs",
    "**/*.jsx",
    "**/*.json"
  ]
}
```

<br /><br />

## 03. `packages/lib/tsconfig.json` 내용추가

```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "node",
    "target": "ESNext",
    "lib": ["ESNext", "dom"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "noEmit": false,
    "incremental": true,
    "resolveJsonModule": true
  },
  "exclude": ["**/node_modules", "**/.*/", "./dist", "./coverage"],
  "include": ["**/*.ts", "**/*.js", "**/.cjs", "**/*.mjs", "**/*.json"]
}
```

<br /><br /><br /><br />

---

<br /><br /><br /><br />

---

# yarn berry(2.x, 3.x, 4.x) workspace

## 1. yarn berry 버전 변경

```shell
// project 폴더 생성
mkdir yarn-berry-workspace
cd yarn-berry-workspace


// yarn 버전 확인
yarn -v

// yarn 버전 변경
yarn set version berry
yarn set version stable

// yarn 버전 확인
yarn -v

```

<br />
<br />
<br />

## 2. yarn workspace 패키지 만들기

```shell
// packages 디렉토리 만들기 / 루트 초기화
yarn init -w
```

- yarn berry cli 명령어: https://yarnpkg.com/cli/init

<br />
<br />
<br />

## 3. root pacakge.json파일 수정

`./package.json`

`apps/*` 폴더추가

```json
{
  "name": "yarn-berry-workspace-test",
  "packageManager": "yarn@3.5.0",
  "private": true,
  "workspaces": ["apps/*", "packages/*"]
}
```

<br />
<br />
<br />

## 4. apps 폴더에 `create next-app` 프로젝트 추가

```shell
// 1. create-next-app 프로젝트 생성
cd apps
yarn create next-app
```

<br />
저는 아래와 같이 셋팅 하였습니다.

![aaa](https://user-images.githubusercontent.com/61961190/235419035-c7e5192d-01b5-416f-bbc3-3fce7d4e9366.png)

pacakge.json 수정.
`"name": "@wanted/web"`으로 변경

![cccc](https://user-images.githubusercontent.com/61961190/235419746-e4008fb8-2e22-454a-937e-6aff75e9cfc5.png)

```shell
// 3. root가서 상태 갱신하기

cd ..
yarn


// 4. 실행 확인
yarn workspace @wanted/web run dev
```

<br />
<br />
<br />

## 5. typescript error 발생

`./apps/wanted/pages/index.tsx` 을 열어보면 typescript error가 발생합니다.

yarn berry pnp는 `node_modules` 모듈을 불러오는 방식이 다르기 때문에 생기는 문제입니다.
![ea1a826f-42c5-46f0-8755-9cd047efc047](https://user-images.githubusercontent.com/61961190/205853866-cc45759a-85d3-48f3-a99b-1b524d199f8a.png)

```shell
yarn add -D typescript
yarn dlx @yarnpkg/sdks vscode
```

<br />
<br />
<br />

## 6. yarn PnP 사용을 위한 vscode extension 설치 `arcanis.vscode-zipfs`

이미 추가 되어 있다면 skip 합니다.

### `.vscode/extensions.json`에 추가

```json
{
  "recommendations": ["arcanis.vscode-zipfs"]
}
```

![스크린샷 2022-12-06 17 02 37](https://user-images.githubusercontent.com/61961190/205854745-16f2fc6b-62e8-4a34-acd6-b0bddfb9efb1.png)

<br />
<br />
<br />

## 7. pacakges/lib 공통 패키지를 만들어보기

`packages/lib` 폴더 생성하고, 아래 스크립트를 실행한다.

```shell
// lib 폴더 이동
cd packages/lib

// pacakge.json 파일 생성
yarn init

// typescript 설치
yarn add typescript
```

<br /><br />

아래와 같이`packages/lib/package.json` 파일 수정한다.

```json
{
  "name": "@wanted/lib",
  "version": "1.0.0",
  "private": true,
  "main": "./src/index.ts",
  "depdndencies": {
    "typescript": "^5.0.4"
  }
}
```

<br /><br />

`packages/lib/tsconfig.json` 파일 생성 후 설정값 넣기

```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "compilerOptions": {
    "strict": true,
    "useUnknownInCatchVariables": true,
    "allowJs": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "newLine": "lf",
    "module": "ESNext",
    "moduleResolution": "node",
    "target": "ESNext",
    "lib": ["ESNext", "dom"],
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "baseUrl": "./src",
    "noEmit": false,
    "incremental": true,
    "resolveJsonModule": true,
    "paths": {}
  },
  "exclude": ["**/node_modules", "**/.*/", "./dist", "./coverage"],
  "include": ["**/*.ts", "**/*.js", "**/.cjs", "**/*.mjs", "**/*.json"]
}
```

<br /><br />

`packages/lib/src/index.ts` 파일 생성후, 아래 코드 넣는다.

```typescript
export const sayHello = () => {
  return "hello from lib";
};
```

<br /><br /><br />

## 8. `apps/wanted`에서 `pacakges/lib` 의존해 보기

`apps/wanted`에 의존성 주입

```shell
// root로 이동한다.
cd ../../

// root 실행한다.
yarn workspace @wanted/web add @wanted/lib
```

`apps/wanted/package.json`에 의존성이 추가된 것을 확인 합니다.

<img width="854" alt="스크린샷 2023-05-01 14 16 38" src="https://user-images.githubusercontent.com/61961190/235410258-f603b360-bd61-47ac-ac6b-4b9445696fac.png">

## 9. `apps/wanted/pages/index.tsx` 파일에서 @wanted/lib 사용해보기

`@wanted/lib`에 `sayHello` 함수를 호출해 봅니다.

![bbbb](https://user-images.githubusercontent.com/61961190/235419345-91ca6fca-71de-4028-a4b6-0086b3c63ae5.png)

그리고, @wanted/web을 구동해 봅니다.

```shell
yarn workspace @wanted/web run dev
```

<br /><br />

아래와 같이 `hello from lib`이 노출된다면 성공 입니다.

<img width="1473" alt="스크린샷 2023-05-01 15 55 49" src="https://user-images.githubusercontent.com/61961190/235419482-86e0118d-4f0e-4767-82e9-b7293ec95fad.png">
