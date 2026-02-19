---
title: "[JavaScript/ES6] 구조 분해 할당(Destructuring) 시 변수 이름 변경하는 방법"
date: 2026-02-11T11:00:00+09:00
draft: false
categories: ["JavaScript"]
tags: ["ES6", "Frontend", "Destructuring", "Web"]

---
![구조분해할당-Destructuring 이름 변경](/images/javascript/destructuring_renaming.png)



안녕하세요! 자바스크립트로 개발을 하다 보면 객체에서 필요한 값만 쏙쏙 뽑아 쓰는 **구조 분해 할당(Destructuring Assignment)** 을 숨 쉬듯이 사용하게 됩니다. 코드를 훨씬 간결하고 가독성 좋게 만들어주는 아주 고마운 문법이죠.

하지만 가끔 객체의 키(Key) 이름 그대로 변수를 생성하고 싶지 않을 때가 있습니다. 변수 이름이 겹치거나, 서버에서 온 데이터의 키 이름이 마음에 들지 않을 때 말이죠. 오늘은 구조 분해 할당을 하면서 **내가 원하는 이름으로 변수명을 변경하는 방법**과 실무에서 자주 쓰이는 활용 사례를 정리해 보겠습니다.

---

### 1. 기본 문법: `기존 이름 : 새로운 이름`

구조 분해 할당에서 이름을 바꾸는 방법은 아주 간단합니다. 객체의 원래 속성 이름 뒤에 콜론(`:`)을 붙이고, 새로 사용할 변수 이름을 적어주면 됩니다.

```javascript
const user = {
  name: '김개발',
  age: 28
};

// name을 userName으로, age를 userAge로 변경하여 할당
const { name: userName, age: userAge } = user;

console.log(userName); // '김개발'
console.log(userAge);  // 28

// console.log(name); // ReferenceError: name is not defined

```

위 코드에서 보시다시피, `name`이나 `age`라는 변수는 생성되지 않으며 오직 `userName`과 `userAge`라는 새로운 이름의 변수만 사용할 수 있습니다.

---

### 2. 실무에서는 언제 유용할까?

#### 💡 상황 A. 변수명 충돌을 방지해야 할 때

이미 같은 스코프 내에 객체의 키와 동일한 이름의 변수가 선언되어 있다면, 구조 분해 할당 시 에러가 발생합니다. 이럴 때 이름을 변경해주면 충돌을 우려 없이 값을 추출할 수 있습니다.

```javascript
const title = '메인 페이지';

const article = {
  title: '구조 분해 할당 완벽 가이드',
  content: '...'
};

// 이미 title이라는 변수가 있으므로, articleTitle로 이름을 변경해서 추출
const { title: articleTitle } = article;

console.log(title);       // '메인 페이지'
console.log(articleTitle); // '구조 분해 할당 완벽 가이드'

```

#### 💡 상황 B. API 응답 데이터의 네이밍 컨벤션을 맞출 때

프론트엔드에서는 주로 **카멜 케이스(camelCase)** 를 사용하지만, 백엔드 API 응답은 종종 **스네이크 케이스(snake_case)** 로 오는 경우가 많습니다. 데이터를 받을 때 바로 구조 분해 할당으로 이름을 변경해주면 코드의 일관성을 유지하기 좋습니다.

```javascript
const apiResponse = {
  user_id: 101,
  first_name: '철수',
  last_name: '김'
};

// 스네이크 케이스를 카멜 케이스로 변경
const { 
  user_id: userId, 
  first_name: firstName, 
  last_name: lastName 
} = apiResponse;

console.log(userId, firstName, lastName); // 101 '철수' '김'

```

---

### 3. 심화: 이름 변경과 동시에 '기본값' 설정하기

구조 분해 할당의 또 다른 강력한 기능인 **기본값 할당(Default values)** 과 이름 변경을 동시에 사용할 수도 있습니다. 문법은 `기존 이름 : 새로운 이름 = 기본값` 형태가 됩니다.

```javascript
const settings = {
  theme: 'dark',
  // language 키는 없는 상태
};

// theme은 currentTheme으로 이름 변경
// language는 currentLang으로 이름 변경하되, 값이 없으므로 'ko'를 기본값으로 설정
const { 
  theme: currentTheme, 
  language: currentLang = 'ko' 
} = settings;

console.log(currentTheme); // 'dark'
console.log(currentLang);  // 'ko'

```

---

### 마치며

객체의 구조 분해 할당 시 변수 이름을 변경하는 `원래키: 새변수명` 문법은 간단하지만, 코드의 가독성을 높이고 변수명 충돌을 우아하게 해결해 주는 훌륭한 패턴입니다. 특히 외부 데이터를 다룰 때 네이밍 컨벤션을 맞추는 용도로 꼭 활용해 보시길 추천드립니다!

