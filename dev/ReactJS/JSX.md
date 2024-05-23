---
title: JSX 소개
description: 
published: true
date: 2024-05-23T13:21:36.139Z
tags: 
editor: markdown
dateCreated: 2024-05-23T13:15:10.806Z
---

# JSX 소개

---

## JSX (JavaScript XML)

- JavaScript의 확장 문법 (HTML도 문자열도 아니다)

```jsx
const element = <h1>Hello, world!</h1>;**
```

- 바벨을 통해 JS로 변환된다

```jsx
// 실제 작성할 JSX 예시 
function App() { 
	return ( 
		<h1>Hello, GodDaeHee!</h1> 
		); 
} 

// 위와 같이 작성하면, 바벨이 다음과 같이 자바스크립트로 해석하여 준다. 
function App() { 
	return React.createElement("h1", null, "Hello, GodDaeHee!"); 
}

출처: https://goddaehee.tistory.com/296 [갓대희의 작은공간]
```

- 최상위 요소는 하나여야 한다.
- JSX 내 JS요소는 {} 로 감싼다
- JSX 스타일링은 객채 형태로 사용한다

```jsx
function App() { 
	const style = { backgroundColor: 'green', fontSize: '12px' } 
	return ( 
		<div style={style}>Hello, GodDaeHee!</div> 
		); 
}

출처: https://goddaehee.tistory.com/296 [갓대희의 작은공간]
```

- class 대신 className을 사용한다
- 소괄호를 제외해도 되지만 return 뒤에 값이 없다면 JS는 자동으로 ; 을 붙인다.(즉 return 밑의 값들은 실행되지 않는다)

```jsx
function App() { 
	const style = { backgroundColor: 'green', fontSize: '12px' } 
	return <div style={style}>Hello, GodDaeHee!</div> //가능
}

function App() { 
	const style = { backgroundColor: 'green', fontSize: '12px' } 
	return 
	<div style={style}>Hello, GodDaeHee!</div> 

	//다음과 같다

	return;
	<div style={style}>Hello, GodDaeHee!</div> 
}
```

- 카멜 표기법을 사용한다
- JSX는 렌더링 전 이스케이프 하기 때문에 XSS공격을 방지할 수 있다

### 바벨(babel)

- 자바스크립트 코드를 변환해주는 트랜스 컴파일러
- 최신 자바스크립트 문법을 지원하지 않는 환경에서도 최신 문법을 사용할 수 있도록 지원
- 즉 바벨을 사용할 경우 ES5문법만 지원하는 브라우저에 알맞게 ES6문법을 ES5 문법으로 번역
- [https://babeljs.io/repl#?browsers=defaults%2C not ie 11%2C not ie_mob 11&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&modules=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.24.5&externalPlugins=&assumptions={}](https://babeljs.io/repl#?browsers=defaults%2C%20not%20ie%2011%2C%20not%20ie_mob%2011&build=&builtIns=false&corejs=3.21&spec=false&loose=false&code_lz=Q&debug=false&forceAllTransforms=false&modules=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=env%2Creact%2Cstage-2&prettier=false&targets=&version=7.24.5&externalPlugins=&assumptions=%7B%7D)

## **웹팩(webpack)**

- 자바스크립트로 만든 프로그램을 배포하기 좋은 형태로 묶어주는 툴
- ESM(ES6의 모듈 시스템)과 commonJS와 같은 외부 javascript 파일을 import 하고 export 함으로써 파일을 분리하여 작성하고 이후에 webpack을 이용해서 하나의 파일로 합쳐준다

## create-react-app

- babel과 webpack, css 후 처리나 그 밖의 다양한 테스트 시스템을 모두 묶어서 개발 환경을 손쉽게 구축할 수 있도록 해주는 프로젝트