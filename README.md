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
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
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
   console.log("hello from lib");
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

