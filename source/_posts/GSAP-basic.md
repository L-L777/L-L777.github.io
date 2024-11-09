---
title: GSAP基础入门
date: 2024-11-04 16:27:36
tags: ["GSAP","动画库"]
categories: 
    - ["前端"]
    - ["GSAP"]
top_img: https://img2.baidu.com/it/u=3205109182,3319667232&fm=253&fmt=auto&app=120&f=JPEG?w=750&h=50
cover: https://img0.baidu.com/it/u=2064138448,2586463179&fm=253&fmt=auto&app=138&f=JPEG?w=1000&h=500
---
## **补间动画：**

这里有四种类型的动画补间：

• `gsap.to()`这个是最常见的，指元素从当前状态开始，到定义的状态结束的动画

```jsx
gsap.to(".circle", { x: 40, fill: 'blue', })
```

• `gsap.from()`元素从from定义的状态开始移动到当前现有状态结束动画

```jsx
gsap.from(".circle", { x: -40, fill: 'blue', });

```

• `gsap.fromTo()`定义起始点状态和终点状态进行动画

```jsx
gsap.fromTo( ".circle",{ x: -40, fill: 'blue', }, { x: 40, fill: 'green' });

```

• `gsap.set()`直接跳转到set的状态，无动画化

```jsx
gsap.set(".circle", { x: 40, fill: 'blue', });

```

## **目标元素获取：**

target可以使用选择器或者获取的[dom](https://so.csdn.net/so/search?q=dom&spm=1001.2101.3001.7020)元素，甚至一个包含多个dom元素数组

```jsx
gsap.to(".box",{x:20});

let box = document.querySelector(".box");
gsap.to(box,{x:20});

let box1 = document.querySelector(".box1");
let box2 = document.querySelector(".box2");
gsap.to([box1,box2],{x:200});

```

## **变量参数：**

变量参数可以是影响动画的任意属性或者特殊行为，例如duration\onComplete\repeat

```jsx
gsap.to(target, {
  // this is the vars object
  // it contains properties to animate
  x: 200,
  rotation: 360,
  // and special properties
  duration: 2
})

```

gsap可以改变几乎所有的动画有关的属性，包括css属性，自定义的对象的属性，甚至css变量和复杂的字符串

这里是一张GSAP属性和css动画属性对应的表

| GSAP变量参数                  | 等于css属性                                    |
| ----------------------------- | ---------------------------------------------- |
| x: 100                        | transform: translateX(100px)                   |
| y: 100                        | transform: translateY(100px)                   |
| xPercent: 50                  | transform: translateX(50%)                     |
| yPercent: 50                  | transform: translateY(50%)                     |
| scale: 2                      | transform: scale(2)                            |
| scaleX: 2                     | transform: scaleX(2)                           |
| scaleY: 2                     | transform: scaleY(2)                           |
| rotation: 90                  | transform: rotate(90deg)                       |
| rotation: “1.25rad”           | 这里使用弧度则没有对应的css                    |
| skew: 30                      | transform: skew(30deg)                         |
| skewX: 30                     | transform: skewX(30deg)                        |
| skewY: “1.23rad”              | 这里使用弧度则没有对应的css                    |
| transformOrigin: “center 40%” | transform-origin: center 40%                   |
| opacity: 0                    | adjust the elements opacity                    |
| autoAlpha: 0                  | 不透明度和可见性的简写                         |
| duration: 1                   | animation-duration: 1s                         |
| **repeat: -1**                | animation-iteration-count: infinite 重复无限次 |
| repeat: 2                     | animation-iteration-count: 2                   |
| delay: 2                      | animation-delay: 2                             |
| yoyo: true 来回往复动画       | animation-direction: alternate                 |

## **单位**

GSAP默认单位是px和deg，可以使用字符串形式设定自己想要的单位，甚至相对计算

```jsx
x: 200, // 默认px
x: "+=200" // 相对计算
x: '40vw', // 字符串
x: () => window.innerWidth / 2, //用函数值计算
rotation: 360 // 默认deg
rotation: "1.25rad" // 使用弧度单位rad

```