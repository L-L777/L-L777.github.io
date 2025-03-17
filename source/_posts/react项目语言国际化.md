---
title: react项目语言国际化
date: 2025-03-17 16:11:35
tags: ["react","配置"]
categories: 
    - ["前端"]
    - ["react"]
    - ["项目配置"]
top_img: https://img0.baidu.com/it/u=2748342842,876019850&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500
cover:  https://img0.baidu.com/it/u=4217862599,249725046&fm=253&fmt=auto&app=138&f=JPEG?w=1500&h=500
---

# 安装环境

需要同时安装 `i18next` 和 `react-i18next` 依赖：

```jsx
npm install react-i18next i18next --save
```

# 配置文件

在src下新建i18n文件夹，以存放国际化相关配置，i18n中分别新建三个文件夹

- `config.ts`：对 i18n 进行初始化操作及插件配置
- `en.json`：英文语言配置文件
- `zh.json`：中文语言配置文件

![](https://i-blog.csdnimg.cn/direct/984dee42bc304c2199635f421a544462.png)

## **2.1 `config.ts`**

```tsx
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import translation_en from './en.json';
import translation_zh from './zh.json';

// 获取浏览器首选语言
const getLanguageFn = () => {
    return navigator.language;
}

const resources = {
  en: {
    translation: translation_en,
  },
  zh: {
    translation: translation_zh,
  },
};

// 进行初始化
i18n.use(initReactI18next).init({
  // 我们自己的语言文件
  resources,
  // 默认语言  zh/en  中文/英文
  lng: localStorage.getItem('language') || (/cn/i.test(getLanguageFn()) && 'zh') || 'zh',
  interpolation: {
    escapeValue: false, // 不会为了xss攻击，而把我们的内容强行转成字符串
  },
}).then(() => {
    console.log('i18n initialized successfully');
}).catch((error) => {
    console.error('i18n initialization failed:', error);
});

export default i18n;
```

## 2.2 `zh.json`

```json
{
  "language": "语言",
  "switch": "选择",
}
```

## 2.3  `en.json`

```json
{
  "language": "Language",
  "switch": "Switch",
}
```

# 封装

在utils文件夹创建contexts文件夹，创建i18nContext.tsx文件，使用Context API来全局使用

## 3.1 `i18nContext.tsx`

```tsx
import React, { createContext, useContext, useState } from 'react';
import i18n from 'i18next';

// 定义上下文的类型
interface I18nContextType {
    currentLanguage: string;
    changeLanguage: (lng: string) => void;
}

// 默认上下文
const defaultContext: I18nContextType = {
    currentLanguage: i18n.language,
    changeLanguage: () => {},
};

// 创建上下文
const I18nContext = createContext<I18nContextType>(defaultContext);

// 定义自定义 Hook
export const useI18n = () => {
    const context = useContext(I18nContext);
    if (!context) {
        throw new Error('useI18n must be used within an I18nProvider');
    }
    return context;
};

// 定义 I18nProvider 组件
export const I18nProvider: React.FC<{ children: React.ReactNode }> = (props) => {
    // 确保 useState 在函数组件顶层调用
    const [currentLanguage, setCurrentLanguage] = useState(i18n.language);

    const changeLanguage = (lng: string) => {
        i18n.changeLanguage(lng)
          .then(() => {
                setCurrentLanguage(lng);
                localStorage.setItem('language', lng);
            })
          .catch((error) => {
                console.error('Failed to change language:', error);
            });
    };

    return (
        <I18nContext.Provider value={{ currentLanguage, changeLanguage }}>
            {props.children}
        </I18nContext.Provider>
    );
};
```

## 3.2 `main.tsx`

在项目main.tsx中引用封装好的context

```tsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom';
import {  ConfigProvider } from 'antd';
import { I18nProvider } from './utils/contexts/i18nContext';
import './index.css'
import App from './App.tsx'
import './i18n/config'; // 引入 i18n 配置
const theme = {
  token: {
    colorPrimary: "#652FFF",
  },
};
createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <BrowserRouter>
      <ConfigProvider theme={theme}>
          <I18nProvider>
        <App />
    </I18nProvider>
      </ConfigProvider>
    </BrowserRouter>
  </StrictMode>,
)
```

# 使用

## 4.1 layout组件使用

```tsx

import { useI18n } from '../../utils/contexts/i18nContext';//引入
const AppLayout: React.FC = () => {
   const {  changeLanguage } = useI18n();
    const [zhMode, setZhMode] = useState(false);
  //exist code
  //创建语言切换函数
  const toggleLangugeMode = (checked: boolean) => {
     setZhMode(checked);
      if (checked) {
      changeLanguage('zh')
    } else {
   changeLanguage('en')
    }
  }
//exist code
  return (
    <Layout style={{ minHeight: '100vh' }}>
   //exist code
   <div style={{ padding: '16px', textAlign: 'center', display: 'flex', flexDirection: 'column', alignItems: 'center',justifyContent:'center', gap: '8px' }}>
        //调用语言切换函数
            <Switch checked={zhMode} onChange={toggleLangugeMode} checkedChildren="zh" unCheckedChildren="en" />

          </div>
     //exist code
    </Layout>
  );
};

export default AppLayout;
```

## 4.2 在对应页面使用

以home页面为例
```tsx
import React, { useState, useRef } from 'react';
import { useTranslation } from 'react-i18next';


const Home: React.FC = () => {
  const { t } = useTranslation();


  return (
    <div className="p-4 space-y-4">
      <h1 className="text-2xl font-bold">{t('welcome')}</h1>
    </div>
  );
};

export default Home;
```