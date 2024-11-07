---
title: 小程序项目配置---全局request及错误处理
date: 2024-10-30 21:15:24
tags: ["小程序","配置"]
categories: 
    - ["前端"]
    - ["小程序"]
    - ["项目配置"]
top_img: https://img0.baidu.com/it/u=1421480513,129599327&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
cover: https://img1.baidu.com/it/u=3719565749,2440019383&fm=253&fmt=auto&app=138&f=PNG?w=320&h=320
---
# 全局request及错误处理

以下是对小程序项目进行全局request及错误处理

```jsx
// request.js
import handleErrors from './handleErrors';

const baseURL = 'http://117.72.95.156:8080/api'; // 基础 URL

const request = (options) => {
  // console.log(options);
  return new Promise((resolve, reject) => {
    // 请求拦截器
    const token = wx.getStorageSync('token');
    if (token) {
      options.header = {
        ...options.header,
        'Authorization': `Bearer ${token}`,
      };
    }

    // 设置完整的 URL
    // options.url = baseURL + (endpoints[options.endpoint] || options.endpoint);
    options.url = baseURL + options.endpoint;
    options.timeout=options.timeout||5000;
    wx.request({
      ...options,
      success: (res) => {
        // 响应拦截器
        if (res.statusCode === 200) {
          resolve(res.data);
        } else {
          handleErrors(res); // 处理错误
          reject(res);
        }
      },
      fail: (error) => {
        handleErrors(error); // 处理请求错误
        reject(error);
      },
    });
  });
};

export default {
  request,
};

```
```jsx
// handleErrors.js
import Notify from '../miniprogram_npm/@vant/weapp/notify/notify';
const handleErrors = (error) => {
  console.log(error);
  if (error.data) {
    const { status } = error.data;
    // console.log(status);
    switch (true) {
      case status >= 500 && status < 600:
        Notify({
          type: 'warning',
          message:`服务器错误: ${status}`,
          duration: 2000,
        });
        // wx.showToast({
        //   title: `服务器错误: ${status}`,
        //   icon: 'error',
        //   duration: 2000
        // })
        break;
      case status === 403:
        Notify({
          type: 'warning',
          message:"禁止访问: 403 Forbidden",
          duration: 2000,
        });
        // wx.showToast({
        //   title: "禁止访问: 403 Forbidden",
        //   icon: 'error',
        //   duration: 2000
        // })
        break;
      case status === 404:
        Notify({
          type: 'warning',
          message:"未找到: 404 Not Found",
          duration: 2000,
        });
        // wx.showToast({
        //   title: "未找到: 404 Not Found",
        //   icon: 'error',
        //   duration: 2000
        // })
        break;
      default:
        Notify({
          type: 'warning',
          message: `错误状态码: ${status}`,
          duration: 2000,
        });
        // wx.showToast({
        //   title: `错误状态码: ${status}`,
        //   icon: 'error',
        //   duration: 2000
        // })
        break;
    }
  } else if (error.request) {
    Notify({
      type: 'warning',
      message: "请求发出, 网络错误",
      duration: 2000,
    });
    // wx.showToast({
    //   title: "请求发出, 网络错误",
    //   icon: 'error',
    //   duration: 2000
    // })
  } else {
    Notify({
      type: 'warning',
      message: "请求错误: " + error.errMsg,
      duration: 2000,
    });
    // wx.showToast({
    //   title: "请求错误: " + error.errMsg,
    //   icon: 'error',
    //   duration: 2000
    // })
  }
};

export default handleErrors;

```