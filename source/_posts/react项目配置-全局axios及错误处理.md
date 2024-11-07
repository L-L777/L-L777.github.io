---
title: react项目配置---全局axios及错误处理
date: 2024-10-30 21:11:11
tags: ["react","配置"]
categories: 
    - ["前端"]
    - ["react"]
    - ["项目配置"]
top_img: https://img0.baidu.com/it/u=2748342842,876019850&fm=253&fmt=auto&app=138&f=JPEG?w=800&h=500
cover:  https://img0.baidu.com/it/u=4217862599,249725046&fm=253&fmt=auto&app=138&f=JPEG?w=1500&h=500
---
# 全局axios及错误处理（react）

以下是对项目进行全局axios配置及错误处理，以react为例

```jsx
//src/utils/axios.js
import axios from "axios";
import handleErrors from "./handleError";

//创建一个axios实例
const service = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 5000,
  headers: {
    "Content-Type": "application/json",
  },
  responseType: "json",
});

//请求拦截器
service.interceptors.request.use(
  (config) => {
    //在请求前做些什么
    const token = localStorage.getItem("token");
    if (token) {
      config.headers["Authorization"] = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    //对请求错误做些什么
    return Promise.reject(error);
  }
);

//响应拦截器
service.interceptors.response.use(
  (response) => {
    //对响应数据做些什么
    return response.data;
  },
  (error) => {
    //对响应错误做些什么
    handleErrors(error); // 使用普通函数处理错误
    return Promise.reject(error);
  }
);

export default service;

```

```jsx
// src/utils/handleError.js
import { message } from "antd";

const handleErrors = (error) => {
  if (error.response) {
    const { status } = error.response;
    switch (true) {
      case status >= 200 && status < 300:
        break;
      case status >= 500 && status < 600:
        message.error(`服务器错误: ${status}`);

        break;
      case status === 403:
        message.error("禁止访问: 403 Forbidden");

        break;
      case status === 404:
        message.error("未找到: 404 Not Found");

        break;
      default:
        message.error(`错误状态码: ${status}`);

        break;
    }
  } else if (error.request) {
    message.error("请求发出, 网络错误");
  } else {
    message.error("请求错误: " + error.message);
  }
};

export default handleErrors;

```