---
title: "[React 정복기 #05] 죽어있는 앱에 생명을! Event와 State 완벽 가이드"
date: 2026-02-18T11:00:00+09:00
draft: false
categories: ["React Tutorial"]
tags: ["React", "JavaScript", "Virtual DOM", "Component", "Declarative", "Frontend", "Web Development"]
author: "Qooing"
---

![React State](/images/react/state.png)


안녕하세요, **Qooing**입니다! 👋

지난 시간까지 우리는 컴포넌트를 조립해서 그럴듯한 'Smart To-Do Planner'의 껍데기를 만들었습니다. 하지만 지금은 버튼을 아무리 광클해도 반응이 없는 '식물인간' 상태입니다.

오늘 우리는 이 앱에 두 가지 핵심 능력을 부여할 것입니다.

1. **Event (이벤트):** 사용자의 행동(클릭)을 감지하는 귀 👂
2. **State (상태):** 화면의 데이터(입력값)를 기억하고 갱신하는 뇌 🧠

이 두 가지는 리액트가 살아 움직이게 만드는 가장 기초적이면서도 필수적인 엔진입니다. 순서대로 하나씩 정복해 봅시다!

---

## 1. 리액트의 귀: 이벤트(Event) 개념

우리가 웹 서핑을 할 때 마우스를 클릭하거나 키보드를 치는 모든 행위를 **'이벤트(Event)'** 라고 합니다. 리액트가 이벤트를 처리하는 방식은 기존 HTML과 아주 비슷하지만, **결정적인 차이점 2가지**가 있습니다.

### **차이점 1. 소문자 말고 CamelCase**

HTML에서는 `onclick`이라고 썼지만, 리액트(JSX)에서는 자바스크립트 문법을 따르기 때문에 낙타 등처럼 중간 글자를 대문자로 써야 합니다.

* `onclick` (X) 👉 **`onClick`** (O)
* `onchange` (X) 👉 **`onChange`** (O)

### **차이점 2. 함수 '호출'이 아니라 '이름'만!**

초보자들이 가장 많이 내는 에러 1위입니다. 🚨

```jsx
// ❌ 틀린 예시: 괄호()를 붙이면, 코드가 읽히자마자 바로 실행돼버립니다.
<button onClick={handleClick()}>클릭</button>

// ⭕️ 맞는 예시: "클릭되면 이 함수를 실행해줘"라고 이름만 넘겨야 합니다.
<button onClick={handleClick}>클릭</button>

```

---

## 2. 실습 1: 버튼 클릭 감지하기

자, 이론은 여기까지! 바로 코드로 확인해 봅시다.
`src/components/TodoInput.jsx` 파일을 열어서 버튼을 누르면 알림창이 뜨게 만들어보세요.

```jsx
// src/components/TodoInput.jsx

function TodoInput() {
  // 1. 버튼이 클릭되면 실행될 함수를 만듭니다.
  const handleClick = () => {
    alert("버튼이 클릭되었습니다!");
  };

  return (
    <div className="input-box">
      <input type="text" placeholder="할 일을 입력하고 엔터를 치세요" />
      {/* 2. 버튼에 onClick 이벤트를 연결합니다. (괄호 없이 이름만!) */}
      <button className="add-btn" onClick={handleClick}>추가</button>
    </div>
  );
}

export default TodoInput;

```

저장하고 브라우저에서 '추가' 버튼을 눌러보세요. 알림창이 뜬다면 리액트의 '귀'가 열린 것입니다! 🎉

---

## 3. 리액트의 뇌: State(상태) 개념 (매우 중요 ⭐️)

이벤트를 감지했다면, 이제 화면을 바꿔야겠죠? 여기서 **State**가 등장합니다.

### **(1) State란 무엇인가요?**

State는 우리말로 **'상태'** 입니다. 리액트에서 State는 **"컴포넌트의 현재 상황을 보여주는 정보(데이터)"** 를 뜻합니다.

쉽게 예를 들어볼까요?

* **전등 스위치:** [켜짐 / 꺼짐] 이라는 2가지 State를 가집니다.
* **카카오톡:** [읽지 않음 1 / 읽음] 이라는 State를 가집니다.

### **(2) 왜 일반 변수(`let`)는 안 되나요?**

*"데이터라면 그냥 `let name = "철수"` 처럼 변수에 담아도 되잖아요?"*

여기서 리액트의 독특한 성격이 나옵니다. 리액트는 **'게으른 화가'** 입니다.

* **일반 변수(`let`) 변경:**
메모리 상의 값은 바뀝니다. 하지만 리액트는 이걸 쳐다보지도 않습니다.
> 리액트: "변수가 바뀌었네? 근데 나보고 어쩌라고? 난 다시 그리기 귀찮아." (화면 갱신 X)


* **State 변경:**
State는 리액트에게 보내는 **강력한 신호**입니다.
> 리액트: **"앗! State가 바뀌었다! 주인님이 화면을 다시 그리라고 하신다! (Re-render)"** (화면 갱신 O)



### **(3) 사용법: `useState` 훅(Hook) 해부하기**

리액트에서 State를 만들기 위해서는 **`useState`** 라는 특별한 함수(Hook)를 써야 합니다. 생김새가 좀 특이하니 자세히 뜯어봅시다.

![useState](/images/react/useState.png)

```javascript
import { useState } from 'react'; // 1. 도구 꺼내기

// 2. 사용하기
const [inputValue, setInputValue] = useState("");

```

이 한 줄에는 3가지 비밀이 숨어 있습니다.

1. **`useState("")`**: "리액트야, 초기값이 빈 문자열(`""`)인 상태 주머니 하나 만들어줘."
2. **`inputValue` (첫 번째 변수)**: 현재 상태 값이 들어있습니다. 우리가 화면에 보여줄 때 사용합니다.
3. **`setInputValue` (두 번째 변수)**: 상태를 바꿀 수 있는 **유일한 리모컨(함수)** 입니다.
* `inputValue = "안녕"` 처럼 직접 바꾸면 안 됩니다! (리액트가 모름)
* `setInputValue("안녕")` 처럼 리모컨을 눌러야 리액트가 화면을 고칩니다.



**참고:** `const [a, b] = ...` 문법은 자바스크립트의 **'구조 분해 할당'** 입니다. 리액트가 주는 선물 상자(배열)를 열어서 첫 번째는 `a`에, 두 번째는 `b`에 담는다는 뜻입니다.
[구조 분해 할당(Destructuring) 더 알아보기]({{< relref "/posts/javascript/destructuring.md" >}})

---

## 4. 실습 2: 입력창(Input) 제어하기

이제 우리가 배운 `useState`를 사용해, 입력창에 쓰는 글자를 리액트가 실시간으로 기억하도록 만들어봅시다.
`TodoInput.jsx`를 다시 수정합니다.

```jsx
// src/components/TodoInput.jsx

// 1. useState 도구를 불러옵니다.
import { useState } from 'react';

function TodoInput() {
  // 2. State 선언: [현재값, 바꾸는함수]
  const [inputValue, setInputValue] = useState('');

  const handleClick = () => {
    // 3. 버튼을 누르면 현재 State 값을 알림창으로 띄웁니다.
    alert(inputValue); 
  };

  return (
    <div className="input-box">
      <input
        type="text"
        placeholder="할 일을 입력하세요"
        
        // 4. [중요] input의 주인은 이제 리액트(State)입니다.
        // 화면에 보여지는 값은 무조건 State를 따라갑니다.
        value={inputValue}
        
        // 5. [중요] 타이핑(Change)할 때마다 리모컨(setInputValue)을 누릅니다.
        // 이게 없으면 타이핑이 안 먹힙니다! (State가 안 바뀌면 화면도 안 바뀌니까요)
        onChange={(event) => setInputValue(event.target.value)}
      />
      <button className="add-btn" onClick={handleClick}>추가</button>
      
      {/* 6. 내가 쓰는 글자가 실시간으로 보이나요? */}
      <p>실시간 입력 중: {inputValue}</p>
    </div>
  );
}

export default TodoInput;

```

### **코드가 어떻게 동작하나요?**

1. 사용자가 키보드로 'A'를 칩니다. (`onChange` 발동)
2. `setInputValue('A')`가 실행되어 리액트에게 알립니다.
3. 리액트가 **"State가 바뀌었네? 다시 그려!"** 하고 깨어납니다.
4. 화면의 `<input>`과 `<p>` 태그를 'A'가 들어간 상태로 다시 그립니다.

저장 후 브라우저에서 타이핑해 보세요. `<p>` 태그의 글자가 실시간으로 바뀌나요? 그렇다면 리액트의 '뇌'와 '귀'가 모두 정상 작동하는 것입니다!

---

## 🚀 마치며

오늘 우리는 죽어있던 앱에 **생명(State)** 과 **청력(Event)** 을 선물했습니다.

**오늘의 핵심 요약:**

1. **Event:** `onClick`처럼 CamelCase를 쓰며, 함수 **이름**만 전달한다.
2. **State:** 리액트가 화면을 다시 그리게 만드는 유일한 트리거다.
3. **useState:** `[현재값, 변경함수]`를 반환하며, 반드시 변경함수(`set...`)로 값을 바꿔야 한다.

이제 입력한 값을 저장하는 것까지 성공했습니다!
하지만 아직은 알림창만 뜰 뿐, 실제로 **할 일 목록(List)** 에는 추가되지 않죠?

다음 시간에는 이 데이터를 부모 컴포넌트(`App`)에게 전달하고, 진짜 배열에 추가하는 **"데이터 흐름의 마법(Props Drilling)"** 을 배워보겠습니다.

