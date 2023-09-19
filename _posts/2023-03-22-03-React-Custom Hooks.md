---
layout: post
title: React 36장 - custom hooks
author: admin
date: 2023-03-22 00:00:00 +900
lastmod: 2023-03-22 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, hook]
---

# React

## Custom Hooks

- 개발자가 스스로 커스텀한 훅을 의미하며 이를 이용해 반복되는 로직을 함수로 뽑아내어 재사용할 수 있다.
- 여러 url을 fetch할 때, 여러 input에 의한 상태 변경 등 반복되는 로직을 동일한 함수에서 작동하게 하고 싶을 때 커스텀 훅을 주로 사용한다.

  - 상태관리 로직의 재활용이 가능하고
  - 클래스 컴포넌트보다 적은 양의 코드로 동일한 로직을 구현할 수 있으며
  - 함수형으로 작성하기 때문에 보다 명료하다.

  ```jsx
  // FriendStatus: 친구가 online인지 offline인지 return하는 컴포넌트
  function FriendStatus(props) {
    const [isOnline, setIsOnline] = useState(null);
    useEffect(() => {
      function handleStatusChange(status) {
        setIsOnline(status.isOnline);
      }
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(
          props.friend.id,
          handleStatusChange
        );
      };
    });

    if (isOnline === null) {
      return "Loading...";
    }
    return isOnline ? "Online" : "Offline";
  }

  // FruendListItem: 친구가 online일 때 초록색으로 표시하는 컴포넌트
  function FriendListItem(props) {
    const [isOnline, setIsOnline] = useState(null);
    useEffect(() => {
      function handleStatusChange(status) {
        setIsOnline(status.isOnline);
      }
      ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
      return () => {
        ChatAPI.unsubscribeFromFriendStatus(
          props.friend.id,
          handleStatusChange
        );
      };
    });

    return (
      <>
        <li style={{ color: isOnline ? "green" : "black" }}>
          {props.friend.name}
        </li>
      </>
    );
  }
  ```

- `FriendStatus` 컴포넌트는 사용자들이 온라인인지 오프라인인지 확인하고, `FriendListItem` 컴포넌트는 사용자들의 상태에 따라 온라인이라면 초록색으로 표시하는 컴포넌트이다.
- 두 컴포넌트는 정확하게 똑같이 쓰이는 로직이 존재하고 있으며, `custom hook`을 이용하여 로직을 빼내서 두 컴포넌트에서 공유가 가능하다.
- Custom hook을 정의할 때는 함수 이름 앞에 `use`를 붙이는 것이 규칙이다.
- 대개의 경우 프로젝트 내의 hooks 디렉토리에 Custom hook을 위치 시킨다.
- Custom hook으로 만들 때 함수는 조건부 함수가 아니어야 한다. 즉 return하는 값은 조건부여서는 안 된다. 그렇기 때문에 `useFriendStatus` Hook은 온라인 상태의 여부를 boolean 타입으로 반환하고 있다.

  ```jsx
  function useFriendStatus(friendID) {
    const [isOnline, setIsOnline] = useState(null);

    useEffect(() => {
      function handleStatusChange(status) {
        setIsOnline(status.isOnline);
      }

      ChatAPI.subscribeStatus(friendID, handleStatusChange);
      return () => {
        ChatAPI.subscribeStatus(friendID, handleStatusChange);
      };
    });

    return isOnline;
  }
  ```

- 이렇게 만들어진 Custom Hook은 Hook 내부에 `useState`와 같은 리액트 내장 Hook을 사용하여 작성할 수 있다.
- 일반 함수 내부에서는 리액트 내장 Hook을 불러 사용할 수 없지만 Custom Hook에서는 가능하다.

  ```jsx
  function FriendStatus(props) {
    const isOnline = useFriendStatus(props.friend.id);

    if (isOnline === null) {
      return "Loading...";
    }
    return isOnline ? "Online" : "Offline";
  }

  function FriendListItem(props) {
    const isOnline = useFriendStatus(props.friend.id);

    return (
      <>
        <li style={{ color: isOnline ? "green" : "black" }}>
          {props.friend.name}
        </li>
      </>
    );
  }
  ```

### 예제

```jsx
const useFetch = (initialUrl) => {
  const [url, setUrl] = useState(initialUrl);
  const [value, setValue] = useState("");

  const fetchData = () => axios.get(url).then(({ data }) => setValue(data));

  useEffect(() => {
    fetchData();
  }, [url]);

  return [value];
};

export default useFetch;
```

```jsx
import { useState, useCallback } from "react";

function useInputs(initialForm) {
  const [form, setForm] = useState(initialForm);

  const onChange = useCallback((e) => {
    const { name, value } = e.target;
    setForm((form) => ({ ...form, [name]: value }));
  }, []);

  const reset = useCallback(() => setForm(initialForm), [initialForm]);
  return [form, onChange, reset];
}

export default useInputs;
```

## 실습

[custom hook 실습해보기](https://codesandbox.io/s/custom-hook-1-z84d5j?from-embed)

- 임의의 데이터로 작성한 public/data.json 파일에서 fetch를 받아오는 App.js가 있다.
- `useEffect`를 custom hook으로 만들어 분리한다.

  ```jsx
  // App.js
  import { useEffect, useState } from "react";

  export default function App() {
    const [data, setData] = useState();

    useEffect(() => {
      fetch("data.json", {
        header: {
          "Content-Type": "application/json",
          Accept: "application/json",
        },
      })
        .then((res) => {
          return response.json();
        })
        .then((myJson) => {
          setData(myJson);
        })
        .catch((err) => {
          console.log(err);
        });
    }, []);

    return (
      <>
        <div className="App">
          <h1>To do List</h1>
          <div className="todo-list">
            {data &&
              data.todo.map((el) => {
                return <li key={el.id}>{el.todo}</li>;
              })}
          </div>
        </div>
      </>
    );
  }
  ```

- fetch로 App.js에서 전달될 data.json을 받아온다.
- 받아온 데이터를 data에 넣어준 후 받아온 data를 리턴하는 custom hook을 만든다.

  ```jsx
  // src/util/useEffectHook.js
  import { useEffect, useState } from "react";

  const useFetch = (fetchUrl) => {
    const [data, setData] = useState();

    useEffect(() => {
      fetch(fetchUrl, {
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json",
        },
      })
        .then((response) => {
          return response.json();
        })
        .then((myJson) => {
          setData(myJson);
        })
        .catch((err) => {
          console.log(err);
        });
    }, [fetchUrl]);

    return data;
  };

  export default useFetch;
  ```

- App.js에서 data를 받아온다.

  ```jsx
  import useFetch from "./util/useFetch";

  export default function App() {
    const data = useFetch("data.json");

    return (
      <>
        <div className="App">
          <h1>To do List</h1>
          <div className="todo-list">
            {data &&
              data.todo.map((el) => {
                return <li key={el.id}>{el.todo}</li>;
              })}
          </div>
        </div>
      </>
    );
  }
  ```

## 실습 2

[custom hook 실습해보기](https://codesandbox.io/s/custom-hooks-silseub-2-h3iepv?from-embed)

- 작성되어 있는 App.js에서 input 요소를 ./component/Input 컴포넌트와 custom hooks인 ./util/useInput으로 분리한다.

  ```jsx
  import { useState } from "react";
  import useInput from "./util/useInput";
  import Input from "./component/Input";
  import "./styles.css";

  export default function App() {
    const [firstName, setFirstName] = useInput("");
    const [lastName, setLastName] = useInput("");
    const [nameArr, setNameArr] = useState([]);

    const handleSubmit = (e) => {
      e.preventDefault();
      setNameArr([...nameArr, `${firstNameValue} ${lastNameValue}`]);
      setFirstName("");
      setLastName("");
    };

    return (
      <>
        <div className="App">
          <h1>Name List</h1>
          <div className="name-form">
            <form onSubmit={handleSubmit}>
              <Input label={"성"} value={firstName} onChange={setFirstName} />
              <Input label={"이름"} value={lasttName} onChange={setLasttName} />
              <button>제출</button>
            </form>
          </div>
          <div className="name-list-wrap">
            <div className="name-list">
              {nameArr.map((el, idx) => {
                return <p key={idx}>{el}</p>;
              })}
            </div>
          </div>
        </div>
      </>
    );
  }
  ```

- Input 컴포넌트로 input 요소들을 분리한다.

  ```jsx
  // Input.js
  const Input = ({ label, value, onChange }) => {
    return (
      <>
        <div className="name-input">
          <label>{label}</label>
          <input
            type="text"
            value={value}
            onChange={(e) => onChange(e.target.value)}
          />
        </div>
      </>
    );
  };
  ```

- useInput으로 cunstom hooks를 작성해준다.

  ```jsx
  import {useState} from "react";

  function useInput(value){
    const [value, setValue] = useState(value);

    const handleCange = (newValue) = {
        setValue(newValue);
    }

    return [value, handleChange];
  }
  ```
