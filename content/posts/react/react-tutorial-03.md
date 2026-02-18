---
title: "[React 정복기 #03] HTML인 척하는 자바스크립트? JSX 문법 완벽 가이드"
date: 2026-02-18T09:00:00+09:00
draft: false
categories: ["React Tutorial"]
tags: ["React", "JavaScript", "Virtual DOM", "Component", "Declarative", "Frontend", "Web Development"]
author: "Qooing"
---

![겉은 HTML, 속은 JS](/images/react/html_js.png)

안녕하세요, **Qooing**입니다! 👋

지난 시간에 우리는 Vite로 개발 환경을 구축하고 서버를 띄우는 데 성공했습니다.
그런데 `App.jsx` 파일을 보면서 혹시 이런 생각 안 드셨나요?

*"분명 자바스크립트 파일(.jsx)인데, 왜 안에 HTML 태그가 들어있지? 이거 에러 안 나나?"*

이 이상한 문법의 정체는 바로 **JSX(JavaScript XML)** 입니다. 리액트 개발의 90%는 이 JSX를 얼마나 잘 다루느냐에 달려 있다고 해도 과언이 아닙니다.

오늘은 리액트가 뱉어내는 빨간 에러 줄에 겁먹지 않도록, **절대 어기면 안 되는 JSX의 핵심 규칙 4가지**를 파헤쳐 보겠습니다.

---

## 1. JSX: 브라우저는 이걸 모릅니다 (feat. Transpiling)

사실 웹 브라우저(Chrome, Safari 등)는 JSX를 전혀 이해하지 못합니다. 브라우저는 오직 순수한 자바스크립트만 읽을 수 있죠.

그럼 어떻게 화면이 나오는 걸까요?
우리가 구축한 **Vite 환경** 내부에는 **'트랜스파일러(Transpiler)'** 라는 번역기가 숨어 있습니다. (개발 모드에서는 주로 **esbuild** 라는 친구가 이 일을 합니다.)

우리가 편하게 HTML처럼 작성하면, 이 번역기가 순식간에 **"브라우저가 이해할 수 있는 자바스크립트"** 로 변환해서 전달해 주는 것이죠. 그래서 우리는 이걸 **"Syntactic Sugar (문법적 설탕)"** 라고 부릅니다. 개발자 편하라고 뿌려준 달콤한 문법이라는 뜻이죠. 🍬

---

## 2. 절대 어기면 안 되는 4가지 규칙 (매우 중요! ⭐️)

JSX는 HTML과 비슷하게 생겼지만, 엄연히 **자바스크립트**입니다. 그래서 까다로운 규칙들이 몇 가지 있습니다.

### **규칙 1. 반드시 하나의 부모 태그로 감싸라!**

리액트 컴포넌트는 무조건 **하나의 덩어리**를 반환(return)해야 합니다. 자바스크립트 함수는 값을 하나만 반환할 수 있기 때문입니다.

❌ **틀린 예시:**

```jsx
function App() {
  return (
    <h1>제목</h1>  // 덩어리 1
    <p>내용</p>    // 덩어리 2 (에러 발생! 🚨)
  );
}

```


⭕️ **맞는 예시 (Fragment 사용):**
불필요한 `<div>`를 만들기 싫다면, **Fragment(`<> ... </>`)** 문법을 사용하세요. HTML에는 남지 않고 리액트에게 "이거 한 덩어리야"라고 알려주는 역할만 합니다.

```jsx
function App() {
  return (
    <> 
      <h1>제목</h1>
      <p>내용</p>
    </>
  );
}

```

### **규칙 2. 닫는 태그는 필수!**

HTML에서는 `<input>`이나 `<br>` 태그를 닫지 않아도 대충 알아서 넘어갔습니다. 하지만 JSX는 짤없습니다. 무조건 닫아야 합니다.

* `<input>` (X) 👉 `<input />` (O)
* `<br>` (X) 👉 `<br />` (O)
* `<img src="...">` (X) 👉 `<img src="..." />` (O)

### **규칙 3. class 대신 `className`**

이게 가장 많이 하는 실수입니다!
자바스크립트에는 이미 `class`(객체 지향 문법)라는 예약어가 존재합니다. 그래서 HTML의 클래스를 지정할 때는 이름을 살짝 바꿔야 합니다.

* `<div class="box">` (X) 👉 `<div className="box">` (O)

### **규칙 4. 자바스크립트 변수는 `{ }` 안에!** 

HTML 중간에 자바스크립트 변수나 함수를 넣고 싶다면 **중괄호 `{ }`** 를 사용해 주세요. 이곳은 자바스크립트가 활동할 수 있는 통로입니다.

```jsx
const name = "Qooing";
return <h1>안녕, {name}!</h1>; // 화면에 "안녕, Qooing!" 출력

```

---

## 3. 실습: Smart To-Do Planner 골격 잡기

자, 이제 배운 규칙들을 활용해 우리 앱의 기본 구조를 잡아볼까요?
`src/App.jsx`를 열고 아래 코드를 작성해 보세요. (기존 내용은 다 지우셔도 됩니다.)

```jsx
// src/App.jsx
import './App.css'

function App() {
  // 자바스크립트 영역: 날짜를 가져옵니다.
  const today = new Date().toLocaleDateString('ko-KR', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  });

  return (
    // JSX 영역
    <div className="app-container">
      {/* 1. 헤더 영역 */}
      <header>
        <h1>Smart To-Do</h1>
        <p className="date-text">오늘은 {today} 입니다.</p>
      </header>

      {/* 2. 입력 영역 */}
      <div className="input-box">
        {/* 규칙: 닫는 태그 필수! */}
        <input type="text" placeholder="할 일을 입력하고 엔터를 치세요" />
        <button className="add-btn">추가</button>
      </div>

      {/* 3. 리스트 영역 (나중에 채울 예정) */}
      <div className="todo-list">
        <p>아직 등록된 할 일이 없습니다.</p>
      </div>
    </div>
  );
}

export default App;

```

#### **💡 코드 뜯어보기**

1. **`{today}`**: 자바스크립트로 구한 오늘 날짜 변수를 중괄호를 사용해 HTML 사이에 쏙 넣었습니다.
2. **`className="date-text"`**: `class` 대신 `className`을 사용했습니다.
3. **`<input ... />`**: 끝에 `/`를 붙여서 태그를 확실하게 닫아주었습니다.

---

## 4. (보너스) 스타일링 살짝 입히기 🎨

화면이 너무 밋밋하죠? `src/App.css` 파일을 열어서 내용을 싹 지우고, 아래 코드를 복사해서 붙여넣어 보세요. (디자인은 거들 뿐이니 가볍게만 적용합니다.)

```css
/* src/App.css */
.app-container {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  border-radius: 15px;
  box-shadow: 0 0 20px rgba(0,0,0,0.1);
  text-align: center;
  background-color: #fff;
}

h1 { color: #333; margin-bottom: 5px; }
.date-text { color: #888; font-size: 0.9rem; margin-bottom: 30px; }

.input-box { display: flex; gap: 10px; margin-bottom: 20px; }
input { flex: 1; padding: 10px; border-radius: 5px; border: 1px solid #ddd; }
.add-btn { padding: 10px 20px; background-color: #646cff; color: white; border: none; border-radius: 5px; cursor: pointer; }
.add-btn:hover { background-color: #535bf2; }

```

저장하고 브라우저를 확인해 보세요. 제법 그럴듯한 앱의 모양이 갖춰졌죠?

![To-Do 앱 초기 화면](/images/react/to-do-01.png)


---

## 🚀 마치며

오늘 우리는 리액트의 가장 기본이 되는 언어, **JSX**를 정복했습니다.

**오늘의 핵심 3줄 요약:**

1. JSX는 **하나의 태그(`<>...</>`)** 로 감싸야 한다.
2. **`class` 대신 `className`**, **닫는 태그(`/>`)** 는 필수다.
3. 자바스크립트 변수는 **중괄호 `{ }`** 안에 넣는다.

지금은 `App.jsx` 파일 하나에 제목, 입력창, 리스트가 다 들어있습니다. 코드가 길어지면 관리하기 힘들겠죠?
다음 시간에는 이 덩어리를 **레고 블록처럼 쪼개는 기술, 컴포넌트(Component)** 에 대해 배워보겠습니다.


기대해 주세요!