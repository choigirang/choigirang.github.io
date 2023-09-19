---
layout: post
title: Next 4장 - 프로젝트 "사이드 퀘스트" (1)
author: admin
date: 2023-05-10 00:00:00 +900
lastmod: 2023-05-10 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [NEXT]
tags: [next, react, pagination, project, msw, router]
---

# Next.js

## API

### 상태관리

- 서버 관련 상태 관리는 쿼리를 사용했다.
- pagination 구현하기 위해 useInfiniteQuery를 사용했고(데이터 캐싱),pagination 버튼에게 setPage를 props로 넘겨 page의 숫자가 바뀔 때마다 그에 해당하는 데이터를 받아오도록 했다.

```jsx
const page_limit = 10;
const { isLoading, isError, data, hasNextPage, isFetching, refetch } =
  useInfiniteQuery(
    queryKey,
    ({ pageParam = page }) => {
      const endpoint = id
        ? `/community/${id}?size=${page_limit}&page=${pageParam}&search=${searchTitle}`
        : `/community?size=${page_limit}&page=${pageParam}&search=${searchTitle}`;
      return api(endpoint).then((res) => res.data);
    },
    {
      getNextPageParam: (lastPage, allPages) => {
        if (lastPage.data.length < page_limit) {
          return null;
        }
        return allPages.length + 1;
      },
    }
  );
```

### 데이터 응답,요청

- msw 라이브러리를 사용하여 클라이언트 측에서 서버를 사용할 수 있도록 설정했다.
- req로부터 url을 입력받고, 입력받은 url 주소에서 size(페이지 데이터 갯수)와 page(페이지 번호)에 대한 params를 찾아 데이터를 계산한다.
- 계산된 데이터 갯수만큼 res로 전송하여 데이터를 사용한다.
- 이는 쿼리로 기억된다.

```jsx
rest.get('/community', async (req, res, ctx) => {
    const url = new URL(req.url);
    const size = Number(url.searchParams.get('size'));
    const page = Number(url.searchParams.get('page'));
    const start = (page - 1) * size;
    const end = page * size;
    const search = url.searchParams.get('search');

    let filteredData = post;
    if (search !== null) {
      filteredData = post.filter((p) => p.title.includes(search));
      console.log(search);
      console.log(filteredData);
    } else {
      filteredData = post;
    }

    const data = filteredData.slice(start, end);
    const total = filteredData.length;
    return res(ctx.status(200), ctx.json({ data, total }));
}),
```

### props 내려주기

- query를 통해 data를 가져왔다.
- data는 pages와 pageParams로 이루어진 객체이며 pages안에는 내가 가져온 데이터가 있다.
- map을 이중으로 활용해야 하는데, 받아온 데이터 전부를 하위 컴포넌트에 내려주고 싶을 땐 props 자체를 내려주면 된다.

```jsx
return (
  <Container>
    {filteredData &&
      filteredData.pages.map((page, idx: number) =>
        page.data.map((article: Article) => (
          <ContentItem {...article} key={article.id} />
        ))
      )}
    <ContentPageNation
      totalData={totalData}
      currentPage={page}
      setPage={setPage}
    />
  </Container>
);
```

### 페이지네이션

- 서버에서 데이터를 넘겨줄 때 data의 총 갯수인 totalData도 함께 내려준다.
- pagination 구현할 컴포넌트에서 이 갯수를 받아, 원하는 게시글 갯수로 전부를 나눈다.
- 그렇게 page 버튼을 구현하고, 버튼을 눌렀을 때 받아올 데이터 page의 숫자를 변경시켜, 버튼을 클릭했을 때 해당하는 번호의 데이터 목록을 받아오도록 한다.

```jsx
export default function ContentPageNation({
  totalData,
  currentPage,
  setPage,
}: ContentPageNationProps) {
  const router = useRouter();
  const [pageNum, setPageNum] = useState<number[]>([]);

  useEffect(() => {
    const pageCount = Math.ceil(totalData / 10);
    const arr = [];
    for (let i = 1; i <= pageCount; i++) {
      arr.push(i);
    }
    setPageNum(arr);
  }, [totalData]);

  const getPageNumsToShow = () => {
    if (pageNum.length <= 7) {
      return pageNum;
    }

    if (currentPage < 4) {
      return [...pageNum.slice(0, 4), '...', pageNum.length];
    }

    if (currentPage > pageNum.length - 3) {
      return [1, '...', ...pageNum.slice(-4)];
    }

    return [
      1,
      '...',
      currentPage - 2,
      currentPage - 1,
      currentPage,
      currentPage + 1,
      currentPage + 2,
      '...',
      pageNum.length,
    ];
  };

  return (
    <Container>
      <PageContainer>
        {getPageNumsToShow().map((el, idx) =>
          el === '...' ? (
            <PageEllipsis key={idx}>...</PageEllipsis>
          ) : (
            <PageButton
              key={idx}
              active={el === currentPage}
              onClick={() => setPage(Number(el))}
            >
              {el}
            </PageButton>
          )
        )}
      </PageContainer>
    </Container>
  );
}
```

### select 디자인 수정하기

- 일반적인 select 태그는 디자인이 너무 밋밋하기 때문에 수정을 해줬다.
- select 자체를 사용하지말고 button을 styled-components로 만든 후, 객체에 값을 담아 ul과 li 태그를 활용하여 클릭했을 때에 className에 변화를 주었다.
- 마치 슬라이드를 하는 것과 같은 효과를 냈다.

```jsx
export default function CustomSelect() {
  const [isOpen, setIsOpen] = useState(false);
  const [selected, setSelected] = useState("검색 구분");

  const options = [
    { value: "sorted", label: "최신순" },
    { value: "star", label: "스크랩순" },
    { value: "view", label: "조회수순" },
    { value: "comment", label: "댓글순" },
  ];

  const handleSelect = (option: any) => {
    setSelected(option.label);
    setIsOpen(false);
  };

  return (
    <CustomSelectWrapper>
      <CustomSelectButton onClick={() => setIsOpen(!isOpen)}>
        {selected} <span className="icon">▼</span>
      </CustomSelectButton>
      <CustomSelectOptions className={isOpen ? "open" : ""}>
        {options.map((option) => (
          <CustomSelectOption
            key={option.value}
            onClick={() => handleSelect(option)}
          >
            {option.label}
          </CustomSelectOption>
        ))}
      </CustomSelectOptions>
    </CustomSelectWrapper>
  );
}
```
