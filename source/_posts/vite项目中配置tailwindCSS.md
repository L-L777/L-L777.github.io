---
title: vite项目中配置tailwindCSS
date: 2025-03-17 17:01:01
tags: ["配置"]
categories: 
    - ["前端"]
    - ["项目配置"]
top_img: https://img0.baidu.com/it/u=1819934390,1292952564&fm=253&fmt=auto&app=120&f=JPEG?w=1422&h=800
cover:  https://th.bing.com/th/id/OIP.mFNmwiH8FJ-xUN1RXj-o-wHaDt?w=349&h=174&c=7&r=0&o=5&pid=1.7
---
# 在vite中tailwind css 4.0版

## **安装 Tailwind CSS**

```tsx
npm install tailwindcss @tailwindcss/vite
```

## **配置 Vite 插件**

```tsx
import { defineConfig } from 'vite'
import tailwindcss from '@tailwindcss/vite'
export default defineConfig({
  plugins: [
    tailwindcss(),
  ],
})
```

## **导入 Tailwind CSS**

在全局的css文件中导入

```css
@import "tailwindcss";
```

## 在页面中使用

举个例子，如：

```tsx

import './App.css'

function App() {

  return (
    <>
      <h1 className="text-3xl font-bold underline bg-gradient-to-r from-green-400 to-blue-500">
    Hello world!
  </h1>
    </>
  )
}

export default App
```

# 在vite中tailwind css 3.4.17版(如果使用到老版本)

## **安装 Tailwind CSS**

```tsx
npm install -D tailwindcss@3
npx tailwindcss init
```

## **配置 tailwind.config.js**

在运行好上面命令后就会项目目录里就会出现postcss.config.js和tailwind.config.js

```jsx
 //tailwind.config.js
 /** @type {import('tailwindcss').Config} */
 export default {
>   content: ["./src/**/*.{html,js}"],
    theme: {
      extend: {},
    },
    plugins: [],
  }
```

## **导入 Tailwind CSS**

在全局的css文件中导入

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## 在页面中使用

举个例子，如：

```tsx

import './App.css'

function App() {

  return (
    <>
      <h1 className="text-3xl font-bold underline bg-gradient-to-r from-green-400 to-blue-500">
    Hello world!
  </h1>
    </>
  )
}

export default App
```

# 注意版本

如果在4.0版本使用`npx tailwindcss init`命令会出现以下错误

```tsx
npm error could not determine executable to run
npm error A complete log of this run can be found in: C:\Users\ASUS\AppData\Local\npm-cache\_logs\2025-03-17T08_58_20_820Z-debug-0.log
```

请安装正确版本