---
layout: post
title: React 23장 - Cmarket Redux (2)
author: admin
date: 2023-02-25 00:00:00 +900
lastmod: 2023-02-25 00:00:00 +900
sitemap:
  changefreq: monthly
  priority: 0.5
categories: [REACT]
tags: [react, components, jsx, state, useDispatch, useSelector]
---

# React

## pages/ItemListContainer.js

- actions/index.js 로부터 `addToCart, notify` 함수를 받아온다.
- redux-hoox인 `useSelector,useDispatch`를 가져온다.
- `useSelector`를 사용하여 기존에 작성했던 reducer인 `itemReducer`의 상태를 지칭한다.
  - reducers/itemReducer.js 에 작성된 `state`는 초기값인 `initialState`를 갖고 있다.
  - `initialState`를 구조분해할당하여 `items`에는 상품 목록이, `cartItems`에는 장바구니에 담긴 상품 목록이 할당되어있다.
- `dispatch`를 선언하여 `useDispatch`를 사용한다.
- `handleClick`함수는 Item 컴포넌트의 `button`이 클릭됐을 때 실행되는데, 함수가 실행되면 `item`을 인자로 받고, 전달받는 `item`은 `item.id`이다.
  - 우리가 클릭 이벤트를 발생시킨 상품의 `id`를 전달받는다.
  - 함수를 실행시켜, 만약 `cartItems` 장바구니 안의 상품들을 `map`으로 새로운 배열로 갖고, 이 배열 안에 우리가 클릭한 상품의 `id`와 겹치는 것이 없으면 `dispatch`를 통해 `addToCart`액션을 발생시킨다.
    - `addToCart`액션은 ADD_TO_CART`type`을 갖고 있으며, `id`와 `quantity`를 추가시킨다.
  - `notify`액션 또한 실행시켜, 장바구니에 상품이 추가됐음을 알린다.
  - 만약 장부구니 안에 우리가 클릭한 상품과 동일한 `id` 를 가진 상품이 있다면, 이미 장바구니에 있는 제품이라는 말이기 때문에 새로운 경고 메시지를 출력한다.

```jsx
import React from "react";
import { addToCart, notify } from "../actions/index";
import { useSelector, useDispatch } from "react-redux";
import Item from "../components/Item";

function ItemListContainer() {
  const state = useSelector((state) => state.itemReducer);
  const { items, cartItems } = state;
  const dispatch = useDispatch();

  const handleClick = (item) => {
    if (!cartItems.map((el) => el.itemId).includes(item.id)) {
      dispatch(addToCart(item.id));
      dispatch(notify(`장바구니에 ${item.name}이(가) 추가되었습니다.`));
    } else {
      dispatch(notify("이미 추가된 상품입니다."));
    }
  };

  return (
    <div id="item-list-container">
      <div id="item-list-body">
        <div id="item-list-title">쓸모없는 선물 모음</div>
        {items.map((item, idx) => (
          <Item
            item={item}
            key={idx}
            handleClick={() => {
              handleClick(item);
            }}
          />
        ))}
      </div>
    </div>
  );
}

export default ItemListContainer;
```

## pages/ShoppingCart.js

- actions/index.js 로부터 `removeToCart, setQuantity` 함수를 받아온다.
- redux-hoox인 `useSelector,useDispatch`를 가져온다.
- `useSelector`를 사용하여 기존에 작성했던 reducer인 `itemReducer`의 상태를 지칭한다.
  - reducers/itemReducer.js 에 작성된 `state`는 초기값인 `initialState`를 갖고 있다.
  - `initialState`를 구조분해할당하여 `items`에는 상품 목록이, `cartItems`에는 장바구니에 담긴 상품 목록이 할당되어있다.
- `dispatch`를 선언하여 `useDispatch`를 사용한다.
- 장바구니의 선택 이벤트를 만들기 위해 ShoppingCart 컴포넌트에서 사용될 `checkItems` 상태값을 만든다.
  - 초기값은 장바구니에 담긴 `cartItems`의 `id`를 갖고 있다.
- `handleCheckChange`함수는 장바구니의 체크 박스가 선택되거나 해제댔을 때 발생하는 이벤트이다.
  - CartItem 컴포넌트로 `handleCheckChange`함수를 보내고, CartItem 컴포넌트에서는 `checkedItems`상태값에 선택이 되어있으면 `true`, 되어있지 않으면 `false`를 보낸다.
  - 이때, 이벤트를 발생시킨 `id`와 `checked`여부를 인자로 넘겨준다.
  - 먄약, `check`가 된 상황이라면 `checkedItems`의 초기값에 `id`를 더해주고,`check`가 해제된 상황이라면 `filter`를 통해 일치하는 `id`의 값을 제외시켜준다.
- `handleAllCheck`함수는 장바구니에 모든 상품의 `check`를 선택하거나 해제하는 역할을 한다.
  - ShoppingCart 컴포넌트에 있는 `input`태그가 이벤트를 발생시키며, 이벤트가 발생할 때 모든 `cartItems`의 길이와 `checkedItems`에 들어가있는 길이가 같다는 것은, 모든 것이 해제됐거나 선택됐다는 뜻이다.
  - 이 여부에 따라 `true`,`false`값을 보내고 만약 `checked` 가 `true`라면 `checkefdItems`의 상태를 모든 `item.id`를 갖고있게 하여 모든 상품을 체크해준다.
  - 만약 `checked`가 `false`라면 해제된 것이기 때문에, 상태값을 빈 배열로 바꿔 모든 상품을 해제한다.
- `handleQuantityChange` 함수는 CartItem 컴포넌트의 `number` 타입의 `input`태그에서 이벤트가 발생할 시 실행되는 함수이다.
  - `input`의 값이 바뀔 때 `handleQuantityChange`로 이벤트를 발생시킨 요소의 `value`와 `id`를 보내준다.
  - 이를 `quantity`와 `id`로 전달받아 `dispatch`를 통해 기존에 작성한 reducer의 `setQuantity`를 실행시킨다.
  - `setQuantity`함수는 `SET_QUANTITY` 타입의 `action`을 갖고 있으며, 이 액션은 itemrReducer에서 우리가 변경한 상품의 `id`와 일치하는 상품을 찾아, 우리가 변경한 `quantity`값으로 설정되도록 배열을 재구성하는 역할을 한다.
- `handleDelete` 함수는 CartItem 컴포넌트의 `button`을 클릭하는 이벤트가 발생할 시 실행되는 함수이다.
  - CartItem 컴포넌트는 `renderItems.map`을 사용하여 장바구니를 구성하고 있으며, 각각의 `id`값을 갖고 있다.
  - 상품4번은, 4번에 해당하는 `id`를 `render.map`을 통해 전달받았고, `button`은 넘겨받은 각각의 `id`를 `handleDelete`에게 인자로 넘겨준다.
  - `disptach`를 통해 기존에 작성한 reducer의 `removeFromCart`를 실행시키고, `removeFromCart`를 실행하면 `REMOVE_FROM_CART` 타입의 액션을 실행시킨다.
  - 이 액션은 `id`와 전달받은 `payload`의 `id`를 비교하여 상품을 제외시키는 역할을 한다.

```jsx
import React, {useState} from "react"
import {useDispatch, useSelector} from "react-redux"
import {removeFromCart, setQuantity} from "../actions"
import CartItem from "../components/CartItem"
import OrderSummary from "../components/OrderSummary"

export default function ShoppingCart(){
    const state = useSelector((state)=> state.itemReducer)
    const {cartItems, items} = state
    const dispatch = useDispatch()
    const [checkedItems, setCheckedItems] = useState(cartItems.map((el) => el.itemId))

    const handleCheckChange = (checked, id) => {
        if(checked){
            setCheckedItems([...checkedItems, id])
        }else{
            setCheckedItems(checkedItems.filter((el)=> el !== id))
        }
    }

    const handleAllCheck = (checked) => {
        if(checked){
            setCheckedItems(cartItems.map((el)=> el.itemId))
        }else{
            setCheckedItems([])
        }
    }

    const handleQuantityChange = (quantity, itemdId) => {
        dispatch(setQuantity(itemId, quantity))
    }

    const handleDelete = (itemId) => {
        setCheckedItems(checkedItems.filter((el)=> el !== itemId))
        dispatch(removeFromCart(itemId))
    }

    const getTotal = () => {
        let cartIdArr = cartItems.map((el)=>el.itemId)
        let total = {
            price: 0,
            quantity: 0,
        }
        for(let i=0; i<cartIdArr.length; i++){
            if(checkedItems.indexOf(cartIdArr[i])> -1){
                let quantity = cartItems[i].quantity;
                let price = items.filter((el) => el.id === cartItems[i].itemId)[0].price
                total.price = total.price + quantity * price;
                total.quantity = total.quantity + quantity;
            }
        }

        return total
    }

    const renderItems = items.filter(
        (el) => cartItems.map((el) => el.itemId).indexOf(el.id) > -1
    )
    const total = getTotal()

    return (
        <div>
            <div>
                <div>장바구니</div>
                <span>
                    <input
                        type="checkbox"
                        checked={checkedItems.length === cartItems.length ? true : false}
                        onChange={(e)=>handleAllCheck(e.target.checked)}
                    ></input>
                    <lable>전체선택</label>
                </span>
                <div>
                {!cartItems.length? (
                    <div>장바구니에 아이템이 없습니다</div>
                ):(
                    <div>
                        {renderItems.map((item,idx)=>{
                            const quantity = cartItems.filter(
                                (el) => el.itemId === item.id
                            )[0].quantity;
                            return (
                                <CartItem
                                    ket={idx}
                                    handleCheckChange={handleCheckChange}
                                    handleQuantityChange={handleQuantityChange}
                                    handleDelete={handleDelete}
                                    item={item}
                                    checkedItems={checkedItems}
                                    quantity={quantity}
                                />
                            )
                        })}
                    </div>
                )}
                <OrderSummary total={total.price} totalQty={total.quantity} />
                </div>
            </div>
        </div>
    )
}

```

[다른 파일에 대한 설명보기](https://choigirang.github.io/react/4-React-Cmarket/){:target="\_blank"}
