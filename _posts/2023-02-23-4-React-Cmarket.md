---
layout: post
title: React 19장 - Cmarket props drilling
author: admin
date: 2023-02-23 00:00:00 +900
lastmod: 2023-02-23 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]  # 대문자로 작성
tags: [react, components, jsx, state, ui, props, drilling]
---
# React
- 장바구니에 상품을 추가하거나 삭제하고,`props`를 활용하여 총 주문 금액의 상태값이 바뀌는 기능을 구현한다.


## state.js
- state 파일에 임의의 값을 작성해 놓는다.



```jsx
export const initialState = {
    "items": [
        {
            "id" : 1,
            "name" : "노른자 분리기",
            "img" : "../images/egg.png",
            "price" : 9900
        },
        {
            "id" : 2,
            "name" : "2020년 달력",
            "img" : "../images/2020.png",
            "price" : 12000
        },
        {
            "id" : 3,
            "name" : "개구리 안대",
            "img" : "../images/frog.png",
            "price" : 2900
        },
        {
            "id" : 4,
            "name" : "뜯어온 보도블럭",
            "img" : "../images/block.png",
            "price" : 4900
        },
        {
            "id" : 5,
            "name" : "칼라 립스틱",
            "img" : "../images/lip.png",
            "price" : 2900
        },
        {
            "id" : 6,
            "name" : "잉어 슈즈",
            "img" : "../images/fishegg.png",
            "price" : 3900
        },
        {
            "id" : 7,
            "name" : "웰컴 매드",
            "img" : "../images/welcome.png",
            "price" : 6900
        },
        {
            "id" : 8,
            "name" : "강시 모자",
            "img" : "../images/hat.png",
            "price" : 9900
        },
    ],
    "cartItems":[
        {
            "itemId" : 1,
            "quantity" : 1
        },
        {
            "itemId" : 5,
            "quantity" : 7
        },
        {
            "itemId" : 2,
            "quantity" : 3
        },
    ]
}
```


## App.js
- App 컴포넌트에서 상태값을 작성한다.
  - 하나의 상태값을 여러 컴포넌트에서 공유하기 때문에 상위 컴포넌트에 작성한다.
- 임의로 작성한 기존값을 받아와, `initialState`의 `items`키가 가진 객체들을 상태값인 `items`에 지정해준다.
- 임의로 작성한 기존값을 받아와, `initialState`의 `cartItems`키가 가진 객체들을 상태값인 `cartItems`에 지정해준다.
- Router을 사용하여 Link에 따른 페이지를 보여준다.
- ItemListContainer 컴포넌트에 `items`,`cartItems`,`setCartItems`를 인자로 넘겨준다.
  - 상태값을 설정하는 `useState`자체를 인자로 넘겨줘서 사용할 수 있다.
- ShoppingCart 컴포넌트에도 마찬가지로 인자를 넘겨준다.



```jsx
import React, { useState } from "react";
import Nav from "./components/Nav";
import ItemListContainer from "./pages/ItemListContainer";
import "./App.css";
import "./variables.css";
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import ShoppingCart from "./pages/ShoppingCart";
import { initialState } from "./assets/state";

function App() {
  const [items, setItems] = useState(initialState.items);
  const [cartItems, setCartItems] = useState(initialState.cartItems);

  return (
    <Router>
      <Nav cartItems={cartItems} />
      <Routes>
        <Route
          path="/"
          element={
            <ItemListContainer
              items={items}
              cartItems={cartItems}
              setCartItems={setCartItems}
            />
          }
        />
        <Route
          path="/shoppingcart"
          element={
            <ShoppingCart
              cartItems={cartItems}
              items={items}
              setCartItems={setCartItems}
            />
          }
        />
      </Routes>
      <img
        id="logo_foot"
        src={`${process.env.PUBLIC_URL}/codestates-logo.png`}
        alt="logo_foot"
      />
    </Router>
  );
}

export default App;
```

## Nav.js
- Nav 컴포넌트는 Link 주솟값을 가진 상품리스트와 장바구니 메뉴를 갖고 있다.
- 장바구니 메뉴에는 우리가 장바구니에 담은 목록의 갯수만큼 숫자로 보여준다.
- App 컴포넌트에서 `cartItems`를 입력받아, 입력받은 객체의 갯수만큼 보여준다.
- `cartItems`는 state에서 불러온 `initialState.cartItems`이다.

```jsx
import React from "react";
import { Link } from "react-router-dom";

function Nav({ cartItems }) {
  return (
    <div id="nav-body">
      <span id="title">
        <img id="logo" src="../logo.png" alt="logo" />
        <span id="name">CMarket</span>
      </span>
      <div id="menu">
        <Link to="/">상품리스트</Link>
        <Link to="/shoppingcart">
          장바구니<span id="nav-item-counter">{cartItems.length}</span>
        </Link>
      </div>
    </div>
  );
}

export default Nav;
```


## ItemListContainer.js
- App 컴포넌트로부터 `items (initialState.items)`,`cartItems (initialState.cartItems)`,`setCartItems`를 전달받는다.
- 전달받은 `setCartItems`로 App 컴포넌트의 `cartItems`의 상태값을 변경할 수 있다.
- Item 컴포넌트로 App 컴포넌트에서 전달받은 `items`와 작성된 함수 `handleClick`을 `props`로 넘겨준다.
- `handleClick` 함수는 `cartItems`에 추가될 새로운 배열 `newCartItem`을 만들고, `newCartItem`의 `itemId` 키 값은 Item 컴포넌트에서 클릭 이벤트를 발생한 대상의 `id`값이 들어간다.
- `handleClick` 함수는 새롭게 작성할 `newCartItem`의 `quantity` 키 값에 1을 할당하는데, 그 이유는 `for문`을 돌려 만약 기존에 `cartItems` 안에 작성되어 있는 `id`값과 비교하여 중복되는 경우 갯수인 `quantity`를 증가시켜주고, 중복되지 않으면 장바구니에 담기지 않았다는 뜻이기에 클릭한 항목 자체를 추가해준다.
- `setCartItems`를 사용하여 새로운 `cartItems`를 만들어주는 이유는, 리액트는 상태의 주솟값이 변하지 않고 요소들만 바뀔 시, 값이 바뀌지 않았다고 인식하기 때문에 주솟값 자체가 바뀐 새로운 `cartItems`를 만들어 준다.
- `return`문이 없을 시, 이미 들어간 항목을 기존의 `cartItems`배열과 함께 추가하여 작성하는 `setCartItems([...cartItems, newCartItem])`까지 도달하기 때문에 중복된다.



```jsx
import React from "react";
import Item from "../components/Item";

function ItemListContainer({ items, cartItems, setCartItems }) {
  const handleClick = (e, id) => {
    let newCartItem = {};
    newCartItem.itemId = id;
    newCartItem.quantity = 1;
    console.log(id);
    for (let i = 0; i < cartItems.length; i++) {
      if (cartItems[i].itemId === id) {
        setCartItems([...cartItems]);
        cartItems[i].quantity++;
        return;
      }
    }
    setCartItems([...cartItems, newCartItem]);
  };
  return (
    <div id="item-list-container">
      <div id="item-list-body">
        <div id="item-list-title">쓸모없는 선물 모음</div>
        {items.map((item, idx) => (
          <Item item={item} key={idx} handleClick={handleClick} />
        ))}
      </div>
    </div>
  );
}

export default ItemListContainer;
```

## Item.js
- ItemListContainer 컴포넌트에게 전달받은 `item`,`handleClick`을 부여한다.
  - `item`은 ItemListContainer에서 기존의 값인 `items`를 `map`으로 뿌려준 각각의 객체이다.
  - `button`을 클릭 시, 클릭한 대상인 `e`와 각각의 `items`의 객체인 `item`의 `id`키 값을 `handleClick`으로 다시 넘겨준다.
  - `handleClick`함수의 결괏값이 어디로 가는지에 대해 생각하기보다 함수 자체가 리턴하는 것이 어느 것인지에 초점을 둔다.
  - `handleClick`함수는 장바구니에 아이템이 겹치는지를 확인하여 `cartItems`에 추가한다.


```jsx
import React from 'react'

export default function Item({ item, handleClick }) {

  return (
    <div key={item.id} className="item">
      <img className="item-img" src={item.img} alt={item.name}></img>
      <span className="item-name">{item.name}</span>
      <span className="item-price">{item.price}</span>
      <button className="item-button" onClick={() => handleClick(item.id)}>장바구니 담기</button>
    </div>
  )
}
```


## ShoppingCart.js
- App 컴포넌트에서 ShoppingCart 컴포넌트로 `items`,`cartItems`,`setCartItems`를 넘겨준다.
- `checkedItems`상태는 입력받은 `cartItems`의 각 객체의 `itemId`를 갖고 있다.
- `handleCheckChange`,`handleQuantityChange`,`handleDelete` 함수는 CartItem 컴포넌트에게 `props`로 전달해준다.
- CartItem 컴포넌트에게 전달된 `handleCheckChange`는 `input`의 `checkbox`를 클릭했을 때 발생하는 대상의 `checked`여부와 `items`의 `id`를 넘겨준다.
  - 만약 `checked`가 된 상태라면 `cartItems`의 `itemdId`를 `map`한 상태인 `checkedItems`에 전달받은 `item.id(선택한 상품의 id)`를 추가해준다.
  - 만약 선택한 체크 박스가 해제된 상태면 `filter`를 통해 선택한 상품의 `id`가 일치하는 항목을 `checkedItems`에서 빼준다.



- `handleAllCheck` 함수는 ShoppingCart의 `input`요소를 누르면 발생하며, 체크 박스가 선택된 상태라면 `cartItems`의 `id`를 `checkedItems`상태에 모두 담아주며 체크 박스의 선택을 해제한 상태라면 `checkedItems`를 모두 비워준다.



- `handleQuantityChange`를 CartItem 컴포넌트에게 전달하여 숫자를 조절하는 `input`요소에 전달한다.
  - 이벤트를 발생시킨 요소의 `value`값인 `quantity`와 `items`를 `filter`한 `item`의 `id`를 함수의 인자로 넘겨준다.
  - 만약 `cartItems`의 요소 중 `itemId` 키 값과 우리가 전달받은 `filter`한 `item id`가 일치하면, 일치하는 요소의 `quantity`를 우리가 `input`으로 입력한 숫자로 설정해준다.
  - 우리가 조작한 숫자로 `quantity`를 설정한다.




- `handleDelete` 또한 CartItem 컴포넌트에게 전달하여 선택한 요소의 `id`를 전달인자로 받는다.
  - 전달인자로 입력받은 `id`를 `cartItems.filter`를 통해 일치하는 `id`를 `cartItems`에서 제외한다.
  - 장바구니에서 삭제한다.



- `getTotal` 함수는 `cartItems`의 `itemsId`를 가진 새로운 배열을 갖고 있는 `cartIdArr`를 선언한다.
  - `checkedItems`에 `cartIdArr`가 있을 경우 `cartIdArr`의 수량을 `total`의 `quantity`에 더해준다.
    - 총 수량 갯수 구하기
  - 가격은 `items`를 `filter` 하여 `cartIdArr`가 가르키는 `id`와 일치하는 상품의 가격을 받아와 `total`의 `price`에 더해준다.
    - 총 주문 금액 구하기

- `renderItems` 함수는 `items`를 `filter`하여 `items`의 `id`와 `cartItems`의 요소의 `id`를 비교해서 `index` 값이 -1보다 크다는 것은 값이 있다는 말이기 때문에 `items`의 요소를 남긴다.
  - `renderItems`는 카트에 들어가 있는 요소 들이고, 이 `renderItems`를 다시 `map`으로 돌리는데 갯수는 `cartItems`에서 일치하는 `id`를 가진 상품의 갯수로 할당해준다.
  - 할당된 `qauntity`를 CartItem 컴포넌트에 넘겨준다.



```jsx
import React, { useState } from "react";
import CartItem from "../components/CartItem";
import OrderSummary from "../components/OrderSummary";

export default function ShoppingCart({ items, cartItems, setCartItems }) {
  const [checkedItems, setCheckedItems] = useState(
    cartItems.map((el) => el.itemId)
  );

  const handleCheckChange = (checked, id) => {
    if (checked) {
      setCheckedItems([...checkedItems, id]);
    } else {
      setCheckedItems(checkedItems.filter((el) => el !== id));
    }
  };

  const handleAllCheck = (checked) => {
    if (checked) {
      setCheckedItems(cartItems.map((el) => el.itemId));
    } else {
      setCheckedItems([]);
    }
  };

  const handleQuantityChange = (quantity, itemId) => {
    setCartItems(
      cartItems.map((el) => {
        if (el.itemId === itemId) {
          el.quantity = quantity;
        }
        return el;
      })
    );
  };

  const handleDelete = (itemId) => {
    console.log(itemId);
    setCartItems(cartItems.filter((el) => el.itemId !== itemId));
  };

  const getTotal = () => {
    let cartIdArr = cartItems.map((el) => el.itemId);
    let total = {
      price: 0,
      quantity: 0,
    };
    for (let i = 0; i < cartIdArr.length; i++) {
      if (checkedItems.indexOf(cartIdArr[i]) > -1) {
        let quantity = cartItems[i].quantity;
        let price = items.filter((el) => el.id === cartItems[i].itemId)[0]
          .price;

        total.price = total.price + quantity * price;
        total.quantity = total.quantity + quantity;
      }
    }
    return total;
  };

  const renderItems = items.filter(
    (el) => cartItems.map((el) => el.itemId).indexOf(el.id) > -1
  );
  const total = getTotal();

  return (
    <div id="item-list-container">
      <div id="item-list-body">
        <div id="item-list-title">장바구니</div>
        <span id="shopping-cart-select-all">
          <input
            type="checkbox"
            checked={checkedItems.length === cartItems.length ? true : false}
            onChange={(e) => handleAllCheck(e.target.checked)}
          ></input>
          <label>전체선택</label>
        </span>
        <div id="shopping-cart-container">
          {!cartItems.length ? (
            <div id="item-list-text">장바구니에 아이템이 없습니다.</div>
          ) : (
            <div id="cart-item-list">
              {renderItems.map((item, idx) => {
                const quantity = cartItems.filter(
                  (el) => el.itemId === item.id
                )[0].quantity;
                return (
                  <CartItem
                    key={idx}
                    handleCheckChange={handleCheckChange}
                    handleQuantityChange={handleQuantityChange}
                    handleDelete={handleDelete}
                    item={item}
                    checkedItems={checkedItems}
                    quantity={quantity}
                  />
                );
              })}
            </div>
          )}
          <OrderSummary total={total.price} totalQty={total.quantity} />
        </div>
      </div>
    </div>
  );
}
```