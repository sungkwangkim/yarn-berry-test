# 2-1주차 배포하기 - 변경된 워크스페이스 감지하기

## yarn-plugin-workspace-since

주어진 두 git revision 사이에 변경점이 있는 workspace에 대해서 주어진 명령어를 실행합니다. 변경점은 파생됩니다. "A" workspace에 의존성을 가진 "B" workspace가 있을때 "A", "B" 모두에 대해서 run이 실행됩니다.

변경된 workspace가 없다면 아무것도 실행하지 않습니다.

```shell
$ yarn workspaces since run <command> <from> [to]
```

<br/><br/>

## 설치

```shell
// root 인지 확인!
// yarn-plugin-workspace-since 설치

yarn plugin import https://raw.githubusercontent.com/toss/yarn-plugin-workspace-since/main/bundles/%40yarnpkg/plugin-workspace-since.js
```

아래와 같이 나오면 정상적으로 설치 된 것 입니다.
(사진 첨부)

<br/><br/>

## Examples

main 브랜치와 4-typecheck 사이에 변경이 있는 workspace에 대해 build 명령어 실행

```shell
yarn workspaces since run build main 4-typecheck

yarn workspaces since run build 4-typecheck 6-deploy

```

<br/><br/>

<br /><br /><br /><br />

---

<br /><br /><br /><br />

# 2-1주차 배포하기 - 테스트 환경 만들기

## apps에 신규 프로젝트 추가한다.

1. 기존의 `apps/wanted` 폴더를 copy + paste 한다.

2. 폴더 이름을 `admin` 으로 변경

![스크린샷 2022-12-12 14 06 07](https://user-images.githubusercontent.com/61961190/206964961-d5420564-d0d9-457e-b1bb-95f61d5e0c78.png)

<br />

3. `apps/admin/package.json` `name` 변경 --> `@wanted/admin`

![스크린샷 2022-12-12 14 06 33](https://user-images.githubusercontent.com/61961190/206964994-6660d5f0-cc21-4b87-89f4-2b86e0a69f33.png)

<br />

4. 터미널에서 `yarn` 으로 갱신시켜준다.

```shell
// root 에서
yarn
```

<br />

6. `@wanted/admin` 빌드 되는지 확인.

```shell
yarn workspace @wanted/admin build
```

<<<<<<< Updated upstream
<br/><br/>

✅ 아래와 같이 나오면 정상!

# ![스크린샷 2022-12-12 14 04 45](https://user-images.githubusercontent.com/61961190/206965342-fb361baf-eabf-44be-8a0d-bbe9d96188a0.png)

<img width="670" alt="스크린샷 2022-12-11 22 06 14" src="https://user-images.githubusercontent.com/61961190/206905624-f6c58573-aede-41d6-99ff-851cfb4400a0.png">

- typescript: yes
- eslint: no

<br /><br />

아래와 apps 하위에 admin, dashboard 폴더 생성 확인

<img width="238" alt="스크린샷 2022-12-11 22 11 10" src="https://user-images.githubusercontent.com/61961190/206905695-527d03fa-9d0c-4d3e-ad03-ae8a0cc0206e.png">
>>>>>>> Stashed changes

<br /><br /><br /><br />

---

<br /><br /><br /><br />

# 1-2주차 (보충) 대규모 리펙토링

지난시간 monorepo로 구축하고 브레이킹 체인지를 발생시키고, 체킹하는 방법까지 알아보았습니다.

추가로, `jscodeshift`라는 javascript/typescript 수정도구에 대해서 간단히 인덱싱만 하고 지나가겠습니다.

<br /><br />

## `jscodeshift` github

https://github.com/facebook/jscodeshift

<br /><br />

## 토스 사용 사례

- https://toss.tech/article/jscodeshift

<br /><br />

## `react-query` 사용사례

https://tanstack.com/query/v4/docs/guides/migrating-to-react-query-4

```javascript
- import { useQuery } from 'react-query'
- import { ReactQueryDevtools } from 'react-query/devtools'
+ import { useQuery } from '@tanstack/react-query'
+ import { ReactQueryDevtools } from '@tanstack/react-query-devtools'
```

```shell
npx jscodeshift ./path/to/src/ \
  --extensions=ts,tsx \
  --parser=tsx \
  --transform=./node_modules/@tanstack/react-query/codemods/v4/replace-import-specifier.js
```

<br /><br /><br /><br />

---

<br /><br /><br /><br />

# 1-2주차 `typecheck` 넣어보기

## 01. typescript package.json에 script추가

추가해야 할 파일

- `apps/wanted/package.json`
- `packages/lib/package.json`
- `packages/ui/package.json`

<br /> <br />

`typecheck` script를 추가합니다.

```json
"scripts": {
  "typecheck": "tsc --project ./tsconfig.json --noEmit"
},
```

<br /> <br />

## 02. Button 컴포넌트 브레이킹 체인지 발생 시켜기.

`packages/ui/src/Button.tsx`의 props type에 `variant` 추가 해본다.

```javascript
import { ButtonHTMLAttributes, MouseEventHandler, ReactNode } from 'react';

export type ButtonProps = ButtonHTMLAttributes<HTMLButtonElement> & {
  children: ReactNode,
  variant: 'contained' | 'outlined', // 이 부분 추가
  onClick?: MouseEventHandler<HTMLButtonElement>,
};

const Button = (props: ButtonProps) => {
  const { children, onClick, ...other } = props;

  return (
    <button type="button" onClick={onClick} {...other}>
      {children}
    </button>
  );
};

export default Button;
```

<br /> <br />

그리고 아래 커맨드를 입력해본다.

```shell
yarn workspace @wanted/web typecheck
```

아래와 같이 에러가 체크됨을 알수 있다.

<img width="878" alt="스크린샷 2022-12-11 21 14 49" src="https://user-images.githubusercontent.com/61961190/206903037-7c03a382-c973-4630-bd96-fbc9aac90d1a.png">

<br /> <br />

## 03. 모든 프로젝트를 typecheck 하는 scripts 만들어보기

yarn workspace에서 관리하기 위한 기본 plugin을 제공해줍니다.
https://yarnpkg.com/api/modules/plugin_workspace_tools.html

#### `workspace-tools` plugin 설치하기

```shell
yarn plugin import workspace-tools
```

#### root package.json 수정하기

```json
"scripts": {
  "g:typecheck": "yarn workspaces foreach -pv run typecheck"
},
```

`g:*`를 붙여주는건 global 하게 모든 프로젝트를 실행한다는 의미로 붙여주었다.

`yarn workspaces foreach` 명령어 option 확인
https://yarnpkg.com/cli/workspaces/foreach

- `-p`: 병렬 실행
- `-v`: workspace name 출력

<br /><br />

`yarn g:typecheck` 실행해보자

```shell
// root 에서
yarn g:typecheck
```

전체 프로젝트가 실행된 것을 알수 있고,
`@wanted/web` 에 에러를 확인할 수 있음.

<img width="824" alt="스크린샷 2022-12-11 21 16 30" src="https://user-images.githubusercontent.com/61961190/206903023-0e801934-e29e-49a9-864d-248b4ce49cef.png">

<br /><br />

`apps/wanted/pages/index.tsx` 수정한다.

<img width="507" alt="스크린샷 2022-12-11 21 18 51" src="https://user-images.githubusercontent.com/61961190/206903111-cbb5f209-2682-47cc-80aa-b5a98c1bbca1.png">

<br /><br />

그리고, 다시 실행해보면!
모두 에러가 없음을 확인할 수 있었다.

✅ 아래와 같이 에러가 출력되면 성공!

<img width="688" alt="스크린샷 2022-12-11 21 23 12" src="https://user-images.githubusercontent.com/61961190/206903278-78f2d6bb-013a-4fcc-b95c-cb4000475fa5.png">

<br /><br /><br /><br />

---

<br /><br /><br /><br />

# 1-2주차 react library 패키지 만들기.

## 01. packages/ui 폴더 생성 및 pacakge.json 생성

<img width="188" alt="스크린샷 2022-12-07 22 13 57" src="https://user-images.githubusercontent.com/61961190/206188526-7eb25dc1-1217-4506-8175-d3a65a32edca.png">

```shell
cd packages/ui
yarn init
```

<br />
package.json 파일을 열어서 name을 `@wanted/ui`로 변경.

```json
{
  "name": "@wanted/ui",
  "packageManager": "yarn@3.3.0"
}
```

<br /><br />

## 02. react dependency 설치

```shell
// root 이동
cd ../../

// 갱신
yarn


// install
yarn workspace @wanted/ui add typescript react react-dom @types/node @types/react @types/react-dom -D
```

<br /><br />

## 03. ui 패키지 설정

`packages/ui/tsconfig.json` 설정

```json
{
  "$schema": "https://json.schemastore.org/tsconfig",
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "baseUrl": "./src",
    "target": "esnext",
    "lib": ["dom", "dom.iterable", "esnext"],
    "module": "esnext",
    "jsx": "react-jsx",
    "noEmit": false,
    "incremental": true
  },
  "exclude": ["**/node_modules", "**/.*/", "dist", "build"]
}
```

<br /><br />

`packages/ui/src/index.ts`, `packages/ui/src/Button.tsx` 파일 생성

<img width="238" alt="스크린샷 2022-12-07 22 12 32" src="https://user-images.githubusercontent.com/61961190/206188248-7db8c662-7c6e-4a23-893f-9bb8bb4b1450.png">

<br /><br />

`packages/ui/src/Button.tsx` 내용추가

```javascript
import { ButtonHTMLAttributes, MouseEventHandler, ReactNode } from 'react';

export type ButtonProps = ButtonHTMLAttributes<HTMLButtonElement> & {
  children: ReactNode,
  onClick?: MouseEventHandler<HTMLButtonElement>,
};

const Button = (props: ButtonProps) => {
  const { children, onClick, ...other } = props;

  return (
    <button type="button" onClick={onClick} {...other}>
      {children}
    </button>
  );
};

export default Button;
```

<br /><br />

`packages/ui/src/index.ts` 내용 추가

```javascript
export { default as Button } from './Button';
```

<br /><br />

`packages/ui/package.json` main 추가

```json
{
  "name": "@wanted/ui",
  "packageManager": "yarn@3.3.0",
  "main": "src/index.ts",
  "devDependencies": {
    "@types/node": "^18.11.11",
    "@types/react": "^18.0.26",
    "@types/react-dom": "^18.0.9",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "typescript": "^4.9.3"
  }
}
```

## 04. `apps/wanted` 에서 `packages/ui` 사용해보기

```shell
// root 에서

// @wanted/ui 의존성 설치
yarn workspace @wanted/web add @wanted/ui

// @wanted/web 구동
yarn workspace @wanted/web dev
```

<br /><br />

http://localhost:3000/ 접속해보면 아래와 같은 오류가 난다.

<img width="993" alt="스크린샷 2022-12-07 22 01 19" src="https://user-images.githubusercontent.com/61961190/206187860-6d8931e0-a3e5-4ea8-a393-a897eb3021e4.png">

### ** 오류 원인 **

브라우저에서 typescript 문법을 해석하지 못해서 발생한다.

<br /><br />

### ** 해결안 **

`@wanted/web`에서 javascript로 변환(transpile) 해줘야 한다.

```shell
// next-transpile-modules 설치
yarn workspace @wanted/web add next-transpile-modules
```

<br /><br />

`apps/wanted/next.config.js` 파일 수정

```javascript
// @wanted/ui 패키지를 tranpile 시킨다.
const withTM = require('next-transpile-modules')(['@wanted/ui']);

/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  swcMinify: true,
};

module.exports = withTM(nextConfig);
```

<br />

종료하고 다시 실행해본다.

```shell
// @wanted/web 구동
yarn workspace @wanted/web dev
```

<br /><br />

✅ 아래와 같이 나왔다면 성공!

<img width="784" alt="스크린샷 2022-12-07 22 06 44" src="https://user-images.githubusercontent.com/61961190/206187825-41efbef1-0362-41dd-8c1b-19fb6baf64de.png">

<br /><br /><br /><br />

---

<br /><br /><br /><br />

# 1-2주차 prettier, eslint 설정 공통화

<br /><br />

## 01. root에서 `prettier`, `eslint` 설치

```shell
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

## 03. `.vscode/settings.json` 설정 추가

```json
"editor.defaultFormatter": "esbenp.prettier-vscode",
"editor.formatOnSave": true,
"editor.rulers": [
  120
],
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

  parser: '@typescript-eslint/parser',
  parserOptions: {
    ecmaFeatures: { jsx: true },
  },

  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended',
    'prettier',
  ],
  plugins: ['@typescript-eslint', 'import', 'react', 'react-hooks'],
  settings: { 'import/resolver': { typescript: {} }, react: { version: 'detect' } },
  rules: {
    'no-implicit-coercion': 'error',
    'no-warning-comments': [
      'warn',
      {
        terms: ['TODO', 'FIXME', 'XXX', 'BUG'],
        location: 'anywhere',
      },
    ],
    curly: ['error', 'all'],
    eqeqeq: ['error', 'always', { null: 'ignore' }],

    // Hoisting을 전략적으로 사용한 경우가 많아서
    '@typescript-eslint/no-use-before-define': 'off',
    // 모델 정의 부분에서 class와 interface를 합치기 위해 사용하는 용법도 잡고 있어서
    '@typescript-eslint/no-empty-interface': 'off',
    // 모델 정의 부분에서 파라미터 프로퍼티를 잘 쓰고 있어서
    '@typescript-eslint/explicit-function-return-type': 'off',
    '@typescript-eslint/no-parameter-properties': 'off',
    '@typescript-eslint/no-var-requires': 'warn',
    '@typescript-eslint/no-non-null-asserted-optional-chain': 'warn',
    '@typescript-eslint/no-inferrable-types': 'warn',
    '@typescript-eslint/no-empty-function': 'off',
    '@typescript-eslint/naming-convention': [
      'error',
      { format: ['camelCase', 'UPPER_CASE', 'PascalCase'], selector: 'variable', leadingUnderscore: 'allow' },
      { format: ['camelCase', 'PascalCase'], selector: 'function' },
      { format: ['PascalCase'], selector: 'interface' },
      { format: ['PascalCase'], selector: 'typeAlias' },
    ],
    '@typescript-eslint/explicit-module-boundary-types': 'off',
    '@typescript-eslint/array-type': ['error', { default: 'array-simple' }],
    '@typescript-eslint/no-unused-vars': ['error', { ignoreRestSiblings: true }],
    '@typescript-eslint/member-ordering': [
      'error',
      {
        default: [
          'public-static-field',
          'private-static-field',
          'public-instance-field',
          'private-instance-field',
          'public-constructor',
          'private-constructor',
          'public-instance-method',
          'private-instance-method',
        ],
      },
    ],

    'import/order': [
      'error',
      {
        groups: ['builtin', 'external', 'internal', 'parent', 'sibling', 'index', 'object'],
        alphabetize: { order: 'asc', caseInsensitive: true },
      },
    ],

    'react/prop-types': 'off',
    // React.memo, React.forwardRef에서 사용하는 경우도 막고 있어서
    'react/display-name': 'off',
    'react-hooks/exhaustive-deps': 'error',
    'react/react-in-jsx-scope': 'off',
    'react/no-unknown-property': ['error', { ignore: ['css'] }],
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

<br /><br /><br /><br />

---

<br /><br /><br /><br />

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
    "incremental": true
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

# 1-1주차 yarn berry(2.x, 3.x, 4.x) workspace

## 1. yarn berry 버전 변경

```shell
// yarn 버전 확인
yarn -v

// yarn 버전 변경
yarn set version berry
yarn set version stable

// yarn 버전 확인
yarn -v


// project 폴더 생성
mkdir yarn-berry-workspace
cd yarn-berry-workspace
```

<br />
<br />
<br />

## 2. yarn workspace 패키지 만들기

```shell
// packages 디렉토리 만들기 / 루트 초기화
yarn init -w
```

https://yarnpkg.com/cli/init

<br />
<br />
<br />

## 3. root pacakge.json파일 수정

`./package.json`

`apps/*` 폴더추가

```json
{
  "name": "yarn-berry-workspace-test",
  "packageManager": "yarn@3.3.0",
  "private": true,
  "workspaces": ["apps/*", "packages/*"]
}
```

<br />
<br />
<br />

## 4. apps 폴더에 create next-app 프로젝트 추가

```shell
// 1. create-next-app 프로젝트 생성
cd apps
yarn create next-app
```

<br />
저는 아래와 같이 셋팅 하였습니다.

![스크린샷 2022-12-07 10 44 06](https://user-images.githubusercontent.com/61961190/206067170-13d7b771-9ee4-4d96-93a5-45549cad00ec.png)

pacakge.json 수정
name `@wanted/web`으로 변경
![스크린샷 2022-12-07 10 45 09](https://user-images.githubusercontent.com/61961190/206067328-b73412f1-7de9-4928-96d8-b4b846834350.png)

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

yarn berry는 npm과 모듈을 불러오는 방식이 다르기 때문에 생기는 문제입니다.
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
```

<br /><br />

아래와 같이`packages/lib/package.json` 파일 수정한다.

```json
{
  "name": "@wanted/lib",
  "version": "1.0.0",
  "private": true,
  "main": "./src/index.ts",
  "dependencies": {
    "typescript": "^4.9.3"
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

`packages/lib/src/index.ts` 파일 생성하기 하고 간단한 코드 넣는다.

```typescript
export const sayHello = () => {
  console.log('hello from lib');
  return 'hello from lib';
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

![dadd0545-744d-498e-acc3-a15db42f11d6](https://user-images.githubusercontent.com/61961190/205856200-8c3613de-b998-41ad-bbb3-58a34abb44f2.png)

## 9. `apps/wanted/pages/index.tsx` 파일에서 @wanted/lib 사용해보기

`@wanted/lib`에 `sayHello` 함수를 호출해 봅니다.
![스크린샷 2022-12-07 10 51 52](https://user-images.githubusercontent.com/61961190/206068252-776b4334-ec61-400b-b9b0-4111bbb3b2af.png)

그리고, @wanted/web을 구동해 봅니다.

```shell
yarn workspace @wanted/web run dev
```

<br /><br />

아래와 같이 `hello from lib`이 노출된다면 성공 입니다.

![스크린샷 2022-12-07 10 54 39](https://user-images.githubusercontent.com/61961190/206068483-468265ec-c26a-4faa-bbf9-208a06fe8cf6.png)
