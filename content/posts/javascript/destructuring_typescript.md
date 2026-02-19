---
title: "[TypeScript] 구조 분해 할당(Destructuring) 완벽 정리: 올바른 타입 지정 방법"
date: 2026-02-11T11:30:00+09:00
draft: false
categories: ["TypeScript"]
tags: ["ES6", "Frontend", "Destructuring", "Web", "TypeScript", "타입스크립트"]
---


안녕하세요! TypeScript에서 **구조 분해 할당(Destructuring)** 을 사용할 때, JavaScript처럼 편하게 쓰려다가 빨간색 에러 줄을 마주친 경험이 한 번쯤은 있으실 겁니다.

자바스크립트에서는 단순히 값을 꺼내오는 용도였지만, 타입스크립트에서는 **'이 값을 꺼낼 건데, 이 값들의 타입은 이거야'** 라고 컴파일러에게 명확히 알려주어야 합니다. 실무에서 가장 자주 쓰이는 객체, 배열, 그리고 함수 파라미터에서의 구조 분해 할당과 타입 지정 방법을 총정리해 드립니다.

---

### 1. 객체(Object) 구조 분해 할당과 타입 지정

TypeScript에서 객체를 구조 분해 할당할 때 가장 많이 하는 실수는 **'이름 변경' 문법(`:`)을 '타입 지정'으로 오해하는 것**입니다.

#### 🚨 흔한 실수 (문법 오류)

```typescript
const user = { name: '김개발', age: 28 };

// 에러 발생! TS는 이를 'name을 string이라는 이름의 변수로 바꾸겠다'로 해석합니다.
const { name: string, age: number } = user; 

```

#### ✅ 올바른 방법: 타입 분리하기

구조 분해 할당 괄호 `{}` 바깥에 전체 객체의 타입을 명시해야 합니다. 실무에서는 보통 `interface`나 `type`을 미리 선언하여 사용합니다.

```typescript
interface User {
  name: string;
  age: number;
}

const user: User = { name: '김개발', age: 28 };

// 1. 기본: 타입을 명시하고 구조 분해 할당
const { name, age }: User = user;

// 2. 심화: 이름 변경과 타입 지정을 동시에 하기
// (name을 userName으로 변경)
const { name: userName, age }: User = user; 

console.log(userName); // '김개발' (string 타입으로 추론됨)

```

---

### 2. 함수 파라미터(Parameter)에서의 구조 분해 할당

React 컴포넌트의 `Props`를 받거나, 여러 개의 인자를 가진 함수를 작성할 때 가장 많이 쓰이는 패턴입니다. 파라미터 자리에서 바로 구조 분해 할당을 하면서 타입을 지정할 수 있습니다.

```typescript
interface PrintOptions {
  title: string;
  isBold?: boolean; // 선택적 프로퍼티
}

// 파라미터 자리에서 구조 분해 할당과 동시에 PrintOptions 타입 지정
function printText({ title, isBold = false }: PrintOptions) {
  console.log(`제목: ${title}, 굵게: ${isBold}`);
}

printText({ title: '타입스크립트 가이드' }); 
// 출력: 제목: 타입스크립트 가이드, 굵게: false

```

이 방식을 사용하면 함수 내부에서 `props.title`처럼 반복해서 적을 필요 없이 코드가 훨씬 간결해집니다.

---

### 3. 배열(Array) 및 튜플(Tuple) 구조 분해 할당

배열은 객체와 달리 '순서(Index)'를 기준으로 값을 꺼내옵니다. TypeScript에서는 특히 길이가 고정된 배열인 **튜플(Tuple)** 을 구조 분해 할당할 때 그 진가를 발휘합니다.

```typescript
// 1. 일반 배열의 구조 분해 할당
const numbers: number[] = [1, 2, 3, 4, 5];
const [first, second, ...rest] = numbers; 
// first, second는 number, rest는 number[] 타입으로 추론됩니다.

// 2. 튜플(Tuple)의 구조 분해 할당 (React useState와 동일한 원리)
type Coordinates = [number, number, string];
const location: Coordinates = [37.5665, 126.9780, 'Seoul'];

const [lat, lng, cityName] = location;
// lat: number, lng: number, cityName: string 으로 정확히 타입이 매칭됩니다.

```

---

### 💡 요약: 이것만 기억하세요!

* **객체 할당 시:** 내부의 콜론(`:`)은 **이름 변경**, 외부의 콜론(`:`)은 **타입 지정**입니다.
* **타입 선언 분리:** 복잡한 타입은 가급적 인라인(Inline)으로 길게 적지 말고, `interface`나 `type`으로 빼서 선언하는 것이 가독성에 좋습니다.
* **함수 파라미터:** 파라미터 내부에서 `({ a, b }: MyType)` 형태로 구조 분해와 타입 지정을 동시에 할 수 있습니다.

