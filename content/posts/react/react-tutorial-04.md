---
title: "[React 정복기 #04] UI를 조각내는 기술, 컴포넌트 분리하기 (feat. Props)"
date: 2026-02-18T10:00:00+09:00
draft: false
categories: ["React Tutorial"]
tags: ["React", "JavaScript", "Virtual DOM", "Component", "Declarative", "Frontend", "Web Development"]
author: "Qooing"
---

![React Component](/images/react/component.png)


안녕하세요, **Qooing**입니다! 👋

지난 시간에 우리는 JSX 문법을 배워서 그럴듯한 'Smart To-Do Planner'의 화면을 만들었습니다. 그런데 지금 `App.jsx` 파일을 보면 어떤가요? 헤더, 입력창, 리스트가 한 파일에 뒤섞여 있죠?

*"음... 코드가 100줄, 1000줄 넘어가면 나중에 어떻게 찾지?"* 😱

그래서 리액트 개발자들은 화면을 **'컴포넌트(Component)'** 라는 작은 부품으로 쪼개서 관리합니다. 오늘은 이 부품들을 만들고, 부품끼리 대화하는 방법인 **Props**에 대해 알아보겠습니다.

---

## 1. 컴포넌트(Component): 레고 블록 만들기

컴포넌트는 거창한 게 아닙니다. 리액트에서는 **"JSX를 반환하는 함수"** 를 컴포넌트라고 부릅니다.

### **지켜야 할 규칙은 딱 하나!**

* 함수 이름의 **첫 글자는 무조건 대문자**여야 합니다.
* `header` (X) 👉 HTML 태그로 착각함
* `Header` (O) 👉 리액트 컴포넌트로 인식함



---

## 2. 실습: 파일 쪼개기 (Refactoring)

자, 이제 `App.jsx`에 뭉쳐있는 코드들을 세 개의 파일로 분리해 보겠습니다.
먼저 `src` 폴더 안에 `components`라는 폴더를 하나 만들어주세요.

### **Step 1. Header 컴포넌트 분리**

`src/components/Header.jsx` 파일을 만들고 아래 코드를 붙여넣으세요.

```jsx
// src/components/Header.jsx

function Header() {
  const today = new Date().toLocaleDateString('ko-KR', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  });

  return (
    <header>
      <h1>Smart To-Do</h1>
      <p className="date-text">오늘은 {today} 입니다.</p>
    </header>
  );
}

export default Header; // 다른 파일에서 쓸 수 있게 내보내기

```

### **Step 2. TodoInput 컴포넌트 분리**

`src/components/TodoInput.jsx` 파일을 만듭니다.

```jsx
// src/components/TodoInput.jsx

function TodoInput() {
  return (
    <div className="input-box">
      <input type="text" placeholder="할 일을 입력하고 엔터를 치세요" />
      <button className="add-btn">추가</button>
    </div>
  );
}

export default TodoInput;

```

### **Step 3. TodoList 컴포넌트 분리**

`src/components/TodoList.jsx` 파일을 만듭니다.

```jsx
// src/components/TodoList.jsx

function TodoList() {
  return (
    <div className="todo-list">
      <p>아직 등록된 할 일이 없습니다.</p>
    </div>
  );
}

export default TodoList;

```

---

## 3. 조립하기: App.jsx 다이어트

이제 대망의 조립 시간입니다!
뚱뚱했던 `App.jsx`의 내용을 싹 지우고, 방금 만든 부품들을 가져와서 조립해 봅시다.

```jsx
// src/App.jsx
import './App.css'
import Header from './components/Header'
import TodoInput from './components/TodoInput'
import TodoList from './components/TodoList'

function App() {
  return (
    <div className="app-container">
      <Header />
      <TodoInput />
      <TodoList />
    </div>
  );
}

export default App;

```

어떤가요? 코드가 믿을 수 없을 정도로 깔끔해졌죠?
나중에 "입력창 디자인을 바꾸고 싶다" 하면 `App.jsx`를 뒤질 필요 없이 `TodoInput.jsx`만 찾아가면 됩니다. 이것이 바로 **유지보수의 혁명**입니다.

---

## 4. Props: 부모가 자식에게 주는 선물 🎁

여기서 한 가지 아쉬운 점이 있습니다.
`Header` 컴포넌트를 보면 "Smart To-Do"라는 제목이 고정되어 있죠. 만약 다른 페이지에서 "My Diary"라는 제목으로 똑같은 디자인을 쓰고 싶다면?

이럴 때 사용하는 것이 바로 **Props(Properties)** 입니다. **부모(App)가 자식(Header)에게 데이터를 전달**하는 방법이죠.

![props](/images/react/props.png)


### **실습: 제목을 맘대로 바꾸기**

**1. 부모(App.jsx)에서 데이터 던져주기**
`<Header />`를 부를 때 `title`이라는 이름으로 값을 넘겨줍니다.

```jsx
// src/App.jsx 수정
// ...
<Header title="Smart To-Do Planner" />
// ...

```

**2. 자식(Header.jsx)에서 데이터 받기**
함수의 파라미터로 `props`를 받아서 사용합니다.

```jsx
// src/components/Header.jsx 수정

// props라는 매개변수를 받습니다.
function Header(props) {
  // ... 날짜 코드는 생략 ...

  return (
    <header>
      {/* props.title로 꺼내 씁니다 */}
      <h1>{props.title}</h1> 
      <p className="date-text">오늘은 {today} 입니다.</p>
    </header>
  );
}

```

이제 `App.jsx`에서 `title="나만의 일기장"`으로 바꾸면 화면의 제목도 즉시 바뀝니다. 하나의 컴포넌트를 여러 용도로 재사용할 수 있게 된 것이죠!

#### **💡 Qooing의 꿀팁: 구조 분해 할당**

`props.title` 이라고 매번 치기 귀찮죠? 현업에서는 이렇게 더 많이 씁니다.

```jsx
function Header({ title }) { // props 대신 { title } 바로 받기
  return <h1>{title}</h1>;
}

```
[구조 분해 할당(Destructuring) 더 알아보기]({{< relref "/posts/javascript/destructuring.md" >}})

---

## 🚀 마치며

오늘 우리는 리액트 개발의 핵심인 **구조화(Structure)** 를 배웠습니다.

**오늘의 핵심 3줄 요약:**

1. **컴포넌트**는 UI를 독립적인 파일로 쪼갠 것이다. (첫 글자는 대문자!)
2. **`import` / `export`** 를 통해 부품을 조립한다.
3. **Props**를 사용하면 부모에서 자식으로 데이터를 전달해 재사용성을 높일 수 있다.

하지만 아직 우리 앱은 껍데기뿐입니다. 버튼을 눌러도 아무 반응이 없죠?
다음 시간에는 드디어 **버튼을 클릭하면 실제로 할 일이 추가되는 기능(Event & State)** 을 구현해 보겠습니다. 진짜 움직이는 앱이 되는 순간이죠!



기대해 주세요! 

