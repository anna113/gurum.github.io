---
title: "[TypeScript] 구조 분해 할당 시 변수 이름 변경과 타입 지정 동시에 하는 방법"
date: 2026-02-11T12:00:00+09:00
draft: false
categories: ["TypeScript"]
tags: ["ES6", "Frontend", "Destructuring", "Web", "TypeScript", "타입스크립트"]
---


안녕하세요! 이전 포스팅에서는 JavaScript의 구조 분해 할당(Destructuring) 시 변수 이름을 변경하는 방법에 대해 알아보았습니다. 코드를 깔끔하게 만들어주는 유용한 문법이지만, 이를 **TypeScript(타입스크립트)** 환경으로 가져오면 많은 개발자들이 헷갈려 하는 구간이 생깁니다.

바로 자바스크립트의 **'이름 변경'** 문법인 콜론(`:`)과 타입스크립트의 **'타입 지정'** 문법인 콜론(`:`)이 똑같이 생겼기 때문인데요.

오늘은 TypeScript 환경에서 에러 없이 객체의 구조 분해 할당, 이름 변경, 그리고 타입 지정까지 한 번에 처리하는 올바른 방법을 정리해 보겠습니다.

---

### 🚨 흔히 하는 실수: 문법의 충돌

API 응답으로 스네이크 케이스(`user_id`) 데이터를 받아 카멜 케이스(`userId`)로 변환하면서, 동시에 `number`라는 타입을 지정하고 싶다고 가정해 봅시다.

처음 TypeScript를 접하면 직관적으로 아래와 같이 코드를 작성하기 쉽습니다.

```typescript
const apiResponse = {
  user_id: 101,
  user_name: '김개발'
};

// ❌ 잘못된 예: 에러 발생!
const { user_id: userId: number, user_name: userName: string } = apiResponse; 

```

하지만 이 코드는 에러를 뱉어냅니다. TypeScript 파서는 `userId: number` 부분을 **'userId라는 이름을 number라는 이름의 변수로 한 번 더 바꾸겠다'** 는 의미로 해석해버리기 때문입니다. 타입스크립트는 구조 분해 할당 내부의 콜론(`:`)을 오직 '이름 변경(Renaming)'으로만 취급합니다.

---

### ✅ 올바른 해결책: 값의 추출과 타입 선언의 분리

TypeScript에서 이 문제를 해결하려면 **할당하는 변수 구조(중괄호)** 와 **타입 선언 구조** 를 명확히 분리해야 합니다. 전체 객체의 모양을 구조 분해 할당 괄호 뒤에 따로 명시해 주는 것이 핵심입니다.

#### 방법 1. 인라인(Inline)으로 타입 지정하기

간단한 객체라면 구조 분해 할당 직후에 곧바로 타입을 명시할 수 있습니다.

```typescript
const { 
  user_id: userId, 
  user_name: userName 
}: { 
  user_id: number; 
  user_name: string; 
} = apiResponse;

console.log(userId);   // 101 (number 타입으로 정상 추론됨)
console.log(userName); // '김개발' (string 타입으로 정상 추론됨)

```

하지만 속성이 많아지면 코드가 가로로 길어지고 가독성이 떨어지는 단점이 있습니다.

#### 방법 2. Interface나 Type Alias 사용하기 (✨ 실무 권장)

실무에서는 가독성과 재사용성을 위해 `interface`나 `type`을 미리 정의해두고 사용하는 방식을 가장 많이 활용합니다.

```typescript
// 1. 타입을 먼저 정의합니다.
interface UserResponse {
  user_id: number;
  user_name: string;
}

const apiResponse: UserResponse = {
  user_id: 101,
  user_name: '김개발'
};

// 2. 정의한 타입을 할당문에 지정합니다.
const { 
  user_id: userId, 
  user_name: userName 
}: UserResponse = apiResponse;

```

이렇게 하면 코드가 훨씬 깔끔해지고, `UserResponse`라는 타입을 다른 곳에서도 재사용할 수 있어 유지보수에 유리합니다.

---

### 💡 보너스: 기본값(Default Value)까지 추가한다면?

이름 변경, 타입 지정에 이어 데이터가 없을 때를 대비한 **기본값**까지 추가해야 한다면 어떻게 될까요? 할당 연산자(`=`)를 사용해 아래와 같이 작성할 수 있습니다.

```typescript
interface UserSettings {
  theme?: string;
  language?: string;
}

const settings: UserSettings = {
  theme: 'dark'
};

// theme -> currentTheme 변경 (기본값 'light')
// language -> currentLang 변경 (기본값 'ko')
const {
  theme: currentTheme = 'light',
  language: currentLang = 'ko'
}: UserSettings = settings;

console.log(currentTheme); // 'dark' (기존 값 유지)
console.log(currentLang);  // 'ko' (값이 없으므로 기본값 할당)

```

---

### 마치며

TypeScript에서 구조 분해 할당을 다룰 때 가장 중요한 규칙은 하나입니다.
**"구조 분해 할당 안에서의 `:`은 이름 변경이고, 중괄호 `{}` 밖에서의 `:`은 타입 지정이다."** 이 규칙만 기억하신다면, 아무리 복잡한 객체가 들어오더라도 자유자재로 이름을 바꾸고 안전하게 타입을 입혀 사용하실 수 있을 것입니다!