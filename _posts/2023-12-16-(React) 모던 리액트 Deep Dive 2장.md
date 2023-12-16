---
layout: post
title: (React) 모던 리액트 Deep Dive 2장
categories: Frontend
published: true
---

<br>

## 목차

  1. [JSX란?](#JSX란?)

<br>

## JSX란?
XML과 유사한 내장형 구문으로, 자바스크립트 표준 코드가 아닌 페이스북이 임의로 만든 새로운 문법이다. JSX는 반드시 트랜스파일러를 거쳐야 자바스크립트 런타임이 이해할 수 있는 자바스크립트 코드로 변환된다. JSX의 설계 목적은 다양한 트랜스파일러에서 다양한 속성을 가진 트리 구조를 토큰화해 ECMAScript로 변환하는데 초점을 두고 있다. 트랜스파일이라는 과정을 거쳐 자바스크립트(ECMAScript)가 이해할 수 있는 코드로 변경하는 것이 목표다. 

JSX는 HTML, XML 외에도 다른 구문으로도 확장될 수 있게 고려돼있고, 최대한 구문을 간결하고 쉽게 작성될 수 있게 설계됐다.

- **JSX 정의** <br>
  JSX는 기본적으로 JSXElement, JSXAttributes, JSXChildren, JSXStrings 네 가지 컴포넌트를 기반으로 구성되어있다.
  - JSXElement : 가장 기본 요소로 HTML의 요소(element)와 비슷한 역할이다. HTML 구문 이외에 사용자가 컴포넌트를 만들어 사용할 떄는 반드시 대문자로 시작하는 컴포넌트를 만들어야 사용 가능하다. 이유는 리액트에서 HTML 태그명과 사용자가 만든 컴포넌트명을 구분 짓기 위해서이다. 
  - JSXStrings : 자바스크립트와는 한가지 중요한 차이점이 있는데 `\`는 자바스크립트에서 특수문자를 처리할 때 사용된다. (`\`를 표현하고자 한다면 `\\`로 이스케이프 해야 한다.)

- **JSX가 자바스크립트에서 변환되는 법** <br>
  자바스크트에서 JSX가 변환되는 방식을 알기 전에, 리액트에서 JSX를 변환하는 `@babel/plugin-transform-react-jsx` 플러그인을 알아야 한다. 이 플러그인은 JSX 구문을 자바스크립트가 이해할 수 있는 형태로 변환된다. 

  ```javascript
  const ComponentA = <A required={true}>Hello World</A>
  const ComponentB = <>Hello World</>
  const ComponentC = (
    <div>
      <span>hello world</span>
    </div>
  )

  // 위 코드를 @babel-plugin-transform-react-jsx로 변환한 결과
  'use strict'

  var ComponentA = React.createElement(
    A,
    { required: true },
    'Hello World'
  )

  var ComponentB = React.createElement(React.Fragment, null, 'Hello world')
  var ComponentC = React.createElement(
    'div',
    null,
    React.CreatElement('span', null, 'hello world')
  )
  ```
