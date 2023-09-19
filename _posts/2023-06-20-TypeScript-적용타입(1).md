---
title: "TypeScript 10장 - 적용타입 (1)"
excerpt: "타입스크립트"

categories: TypeScript
tags: [타입스크립트, TypeScript, 이벤트타입]

toc: true
toc_sticky: true

date: 2023-06-20
last_modified_at: 2022-07-03
---

# TypeScript

## Input 이벤트

- `useState`를 이용해 우리가 입력한 값을 기억하고 바꿔주고 싶을 때, 입력이 발생하는 `onChange`의 `event.target.value`에 대한 값에 대한 타입 지정이다.
- `input`에 이벤트가 발생하는 타입은 `HTMLInputElement`이다.

```jsx
const [example, setExample] = useState < string > "";
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setExample(e.target.value);
};

return <input type="text" onChange={(e) => hanldeChange(e)} />;
```

### 머리 깨지기

- `useInput`에 대한 커스텀훅을 작성했는데도 계속 타입 오류 발생, 많이 보는 구문이었지만 `호출에 필요한 오버로드가 일치...`와 같은 에러가 떴다.
- 아래 코드에서 `useInputs`의 리턴값에 상수화 시켜주지 않을 시, `Header`의 `Input value={} onChange={setKeywords}` 부분에 에러가 계속 발생한다.
- 아래 구문을 보면 현재 배열로 작성되어, 타입이 `(string | number | e:...)[]`으로 되어있는데, `as const`를 붙여 상수화시켜 각 자리를 고정시켜 해결할 수 있다.

```jsx
// 이 호출과 일치하는 오버로드가 없습니다.
//   오버로드 2/2('(props: StyledComponentPropsWithAs<"input", any, {}, never, "input", "input">): ReactElement<StyledComponentPropsWithAs<"input", any, {}, never, "input", "input">, string | JSXElementConstructor<...>>')에서 다음 오류가 발생했습니다.
//     'string | number | ((e: ChangeEvent<HTMLInputElement>) => void)' 형식은 'string | number | readonly string[] | undefined' 형식에 할당할 수 없습니다.

export function useInputs(initialData: string | number) {
  const [data, setData] = useState(initialData);

  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const {
      target: { value },
    } = e;
    setData(value);
  };

  return [data, onChange]; // as const;
}

export function Header() {
  // Header 카테고리
  const category = useMemo(() => Object.keys(HEADER_NAV), []);
  // 링크 연결
  const router = useRouter();
  // 검색어 저장
  const [keywords, setKeywords] = useInputs("");

  return (
    <>
      <Bar>
        <div className="logo">logo</div>
        <InputBox>
          <Input
            placeholder="게시글 검색"
            value={keywords}
            onChange={setKeywords}
          ></Input>
        </InputBox>
      </Bar>
      <Nav>
        {category.map((each) => (
          <li key={HEADER_NAV[each]} className="nav-item">
            <a onClick={() => router.push(HEADER_NAV[each])}>{each}</a>
          </li>
        ))}
      </Nav>
    </>
  );
}
```
