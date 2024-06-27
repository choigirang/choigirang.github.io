---
layout: post
title: Interview 24장 - debounce / throttle
author: admin
date: 2024-06-27 00:00:00 +900
lastmod: 2024-06-27 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [INTERVIEW]
tags: [debounce, throttle]
---

# Interview

## Debounce / Throttle

- 일정 시간동안 동일한 이벤트가 발생했을 때, 이벤트 횟수를 한 번으로 제한하는 것을 말한다.
- 불필요한 이벤트 핸들러가 동일하게 호출되는 것을 방지하여 앱 성능을 개선할 수 있다.
- resize, keyboard 등의 단 시간에 반복적으로 발생하는 이벤트에 사용할 수 있다.

### Debounce

- 마지막에 발생한 이벤트를 처리한다.
- 검색어 자동 완성과 같은 기능을 구현할 때, 만약 키보드 입력을 기준으로 API 요청을 보내는 상황이라면, 단어가 입력될 때마다 API요청을 보내게 된다.
- 웹 성능이 당연히 떨어지고 마지막에 입력된 검색어만을 가지고 API요청을 보내야 한다.
- 의도적으로 UI 갱신을 지연시키며, 사용자 경험을 헤치지 않기 위해 사용자가 인식하지 못할만큼 짧은 시간을 설정하는 것이 일반적이다.

```jsx
function KeyboardApi() {
  const [result, setResult] = useState([]);
  const [keywords, setKeywords] = useState("");

// 키보드가 입력될 때마다 API 요청 => 효율 저하
  const keywordHandler = async (e) => {
    setKeywords(e.target.value);
    const res = await fetch("...").then((res) => setResult(res.data));
  };

  return (
    <div>
      <input onChange={(e) => setKeywords(e)} />
      <ul>{result.map(...검색어 결과)}</ul>
    </div>
  );
}
```

```jsx
function useDebounce(value, delay){
    const [debounceValue, setDebounceValue] = useState(value);

    useEffect(() => {
        const handler = setTimeout(() => {
            setDebounceValue(value)
        }, delay)

        return () => {
            clearTimeout(handler)
        }
    },[value, delay])
}

function KeyboardApi(){
    const [result, setResult] = useState([]);
    const [keywords, setKeywords] = useState("");

    const debounce = useDebounce(keywords);

    const keywordHandler = (e) => {
        setKeywords(e.target.value)
    }

    useEffect(() => {
        const searchResult = async () => {
            if(debounce){
                const res = await fetch("/").then(res => res.data)
            }
        }
    },[debounce])

    return (
        <ul>{result.map(...검색어 결과)}</ul>
    )
}
```

### Throttle

- resize, scroll, button 등에 주로 사용되며 첫 번째 발생한 이벤트로 제한한다.
- 주로 즉각적인 레이아웃 변화 등의 피드백이 필요한 경우 사용된다.
- 만약 검색어 자동 완성 기능에 사용된다면, 처음 입력된 검색어를 기준으로 자동 완성되며, 작성이 완료된 검색어에 대한 자동 완성이 되지 않는다.
- 버튼을 클릭하여 API요청을 보내는 예시

```jsx
function ClickBtn(){
    const [result, setResult] = useState([]);
    const [keyword, setKeyword] = useState("");

    // 사용자가 버튼을 반복 클릭 시 API요청 중복 발생
    const getResult = async () => {
        const res = await fetch("/").then(res => setResult(res.data))
    }

    return (
        <div>
        <input onChange={...}/>
            <ul>{result.map(...결과)}</ul>
            <button onClick={getResult}>검색</button>
        </div>
    )
}
```

```jsx
function useThrottle(callback, delay) {
  const throttleRef = useRef(false);

  return useCallback(() => {
    if (!throttleRef.current) {
      callback();
      throttleRef.current = true;
      setTimeout(() => {
        throttleRef.current = false;
      }, delay);
    }
  }, [callback, delay]);
}

function ClickBtn() {
  const [result, setResult] = useState([]);
  const [keyword, setKeyword] = useState("");

  const getResult = async () => {
    // ...동일
  };

  const throttle = useThrottle(getResult, 2000); // 2초 간격으로 쓰로틀링

  const handleInputChange = (e) => {
    setKeyword(e.target.value);
  };

  return (
    <div>
      <button onClick={throttle}>검색</button>
    </div>
  );
}
```
