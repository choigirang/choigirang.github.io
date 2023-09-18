---
layout: post
title: React 25장 - Axios
author: admin
date: 2023-02-26 00:00:00 +900
lastmod: 2023-02-26 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, axios, api]
---

# React

## Axios

- 브라우저, Node.js를 위한 `Promise API`를 활용하는 HTTP 비동기 통신 라이브러리이다.
- 일반적으로 자바스크립트에서 API를 연동하기 위헤 `fetch`를 사용하지만, 활용도가 높고 장점이 많은 `axios`를 주로 사용한다.
- 백엔드와 프론트엔드가 통신을 쉽게하기 위해 Ajax와 더불어 사용한다.
- 프론트엔드는 백엔드에게 정보를 요청할 때 `Axios`를 사용하여 정보를 요청하고, 반대로 백엔드는 프론트엔드에게 정보를 넘겨줄 때 `Axios`를 사용한다.
- `fetch`는 Response 객체를 반환하지만 `axios`는 API를 제공하기 때문에 HTTP 요청을 보다 쉽게 처리할 수 있도록 도와준다.
- `axios`는 요청 취소와 같은 기능을 지원하며, `fetch`는 이를 지원하지 않는다.
- `axios`는 JSON형식의 데이터를 자동으로 반환하지만, `fetch`는 받은 데이터를 별도의 가공을 해야 한다.
- `axios`는 오류를 반환하는 `promise`를 생성할 수 있지만 `fetch`는 오류를 반환하지 않기 때문에 상태 코드를 따로 확인해야 한다.

## 코드 비교

- 동일한 기능을 수행하는 코드이지만 `fetch`와 `axios`의 차이가 있다.
  - `fetch`는 `body`프로퍼티를 사용하고, `axios`는 `data`프로퍼티를 사용한다.
  - `fetch`는 `url`이 함수의 인자로 들어가고, `axios`는 `url`이 `option`의 객체로 들어간다.
  - `fetch`의 `body`부분은 `stringify`된다.
- `fetch`

```js
const url = "http://localhost3000/test"
const option = {
    method: "POST",
    header: {
        "Accept" : "application/json",
        "Content-Type" : "application/json";charset=UTP-8
    },
    body:JSON.stringify({
        name:"choi",
        age:20
    })

    fetch(url, options)
        .then(response => console.log(response))
}
```

- `axios`

```js
const option = {
    url = "http://localhost3000/test"
    method: "POST",
    header: {
        "Accept" : "appliction/json",
        "Content-Type" : "application/json"; charset=UTP-8
    },
    data: {
        name: "choi",
        age:20
    }

    axios(options)
        .then(response => console.log(response))
}
```

## 사용방법

- `axios`모듈을 설치한다.
  - `npm install axios --save`
- `import`해서 불러온다.
  - `import axios from "axios"`
- HTTP Methods

  - GET

    - `axios(get(url,[,config]))`
    - 서버에서 어떤 데이터를 가져와서 보여주는 역할을 한다.
    - 주소에 있는 쿼리스트링을 활용해서 정보를 전달할 뿐, 값이나 상태를 바꿀 수 없다.

    ```js
    import axios from "axios";

    axios
      .get("https://localhost:3000/choi/user")
      .then((Response) => {
        console.log(Response.data);
      })
      .catch((Error) => {
        console.log(Error);
      });
    ```

    ```js
    [
      { id: 1, pw: "1234", name: "choi" },
      { id: 2, pw: "1234", name: "kim" },
      { id: 3, pw: "1234", name: "park" },
    ];
    ```

  - POST

    - `axios.post("url주소",{data객체},[,config])`
      - 새로운 리소스를 생성할 때 쓴다.
      - 로그인, 회원가입 등 사용자가 생성한 파일을 서버에다가 업로드할 때 사용된다.
      - POST를 사용하면 주소창에 쿼리스트링이 남지 않기 때문에 GET보다 안전하다.
      - POST 메서드의 두 번째 인자에는 본문으로 보낼 데이터를 설정한 객체 리터럴을 전달한다.

    ```js
    axios
      .post(
        "url",
        {
          contact: "choi",
          email: "choi@gmail.com",
        },
        {
          headers: {
            "Content-type": "application/json",
            Accept: "application/json",
          },
        }
      )
      .then((reponse) => {
        console.log(response.data);
      })
      .catch((reponse) => {
        console.log("Error");
      });
    ```

  - DELETE

    - `axios.delete(url,[,config])`
    - REST 기반 API 프로그램에서 데이터베이스에 저장되어 있는 내용을 삭제하는 목적으로 사용한다.
    - DELETE 메서드는 HTML Form 태그에서 기본적으로 지원하는 HTTP 메서드가 아니다.
    - 서버에 있는 데이터베이스의 내용을 삭제하는 것을 주 목적으로 하기에 두 번째 인자를 아예 전달하지 않는다.

    ```jsx
    axios
      .delete("/thisisExample/list/30")
      .then((response) => {
        console.log(response);
      })
      .catch((ex) => {
        throw new Error(ex);
      });
    ```

  - PUT
    - `axios.put(url[,data[,config]])`
    - REST 기반 API 프로그램에서 데이터베이스에 저장되어 있는 내용을 갱신하는 목적으로 사용된다.
    - PUT 메서드는 HTML Form 태그에서 기본적으로 지원하는 HTTP 메서드가 아니다.
    - PUT 메서드는 서버에 있는 데이터베이스의 내용을 변경하는 것을 주 목적으로 사용하고 있다.

## 예시

- API 데이터를 받아온다.

  - [API 데이터 예시](https://jsonplaceholder.typicode.com/user){:target="\_blank"}

  ```jsx
  import axios from "axios";

  axios.get("/users/1");
  ```

### Users.js

- 데이터를 불러오는 상태값을 설정한다.
- 리액트가 처음 렌더링 될 때에 `useEffect`로 전달받은 함수 `fetchUsers`가 실행되는데, 이는 `async`로 인해 비동기 함수로 실행된다.
- `try`를 통해 요청이 실행됐을 때 `users`,`error`값은 `null`로 초기화되고, `loading`의 상태값은 `true`로 바뀐다.
- `axios.get`으로 API에서 데이터를 받아온 것을 `reponse`에 담아, 전달받은 데이터를 `setUsers`를 통해 상태값으로 담는다.
- 만약 에러가 발생하면 에러에 발생한 에러를 전달한다.
- `loading`과 `error` 값이 존재하는 차이에 따라 출력하는 `div`값이 다르다.
- `users`상태가 존재하면 `map()`함수를 통해 사용자 목록을 출력한다.
- `button`을 사용하여 API 재요청 또한 가능하다.
  `<button onClick={fetchUsers}>다시 불러오기</button>`

```jsx
import React,{useState, useEffect} from "react"
import axios from "axios"

function Users(){
    const [usersm setUsers] = useState(null)
    const [loading, setLoading] = useState(false)
    const [error, setError] = useState(null)

    useEffect(()=>{
        const fetchUsers = async() => {
            try{
                setError(null)
                setUsers(null)
                setLoading(true)
                const response = await axios.get(
                    "https://jsonlaceholder.typicode.com/users"
                )
                setUsers(response.data)
            } catch(e){
                setError(e)
            }
            setLoading(false)
        }

        fetchUsers()
    },[])

    if(loading) return <div>로딩중..</div>
    if(error) return <div>에러가 발생했습니다.</div>
    if(!users) return null
    return (
        <ul>
            {users.map(user => (
                <li key={user.id}>
                    {user.username}
                </li>
            ))}
        </ul>
    )
}

export default Users
```

### App.js

```jsx
import React from "react";
import Users from "./Users";

function App() {
  return <Users />;
}

export default App;
```
