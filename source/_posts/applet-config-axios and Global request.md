---
title: 小程序原生封装axios以及配置全局http请求
date: 2024-11-11 13:46:05
tags: ["小程序","配置","封装axios"]
categories: 
    - ["前端"]
    - ["小程序"]
    - ["项目配置"]
top_img: https://img0.baidu.com/it/u=1421480513,129599327&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
cover: https://img1.baidu.com/it/u=3719565749,2440019383&fm=253&fmt=auto&app=138&f=PNG?w=320&h=320
---
## 1. 在`utils`文件夹里创建一个`request.js`文件，创建一个`request`类封装`axios`并且导出
    
    ```jsx
   // 封装axios
class axios {
  constructor(options = {}) {
    // 请求的根路径
    this.baseUrl = options.baseUrl || '';
    // 请求的 url 地址
    this.url = options.url || '';
    // 请求方式
    this.method = 'GET';
    // 请求的参数对象
    this.data = null;
    // header 请求头
    this.header = options.header || {};
    this.beforeRequest = null;
    this.afterRequest = null;
    this.errRequest = null;
  }

  // 添加对header的支持
  _mergeHeaders(customHeader = {}) {
    return Object.assign({}, this.header, customHeader); // 合并默认header和自定义header
  }

  get(url, data = {}, header = {}) {
    // 清空 header 对象
    this.header = {};
    this.method = 'GET';
    this.url = this.baseUrl + url;
    this.data = data;
    this.header = this._mergeHeaders(header); // 合并header
    return this._sendRequest();
  }

  post(url, data = {}, header = {}) {
    // 清空 header 对象
    this.header = {};
    this.method = 'POST';
    this.url = this.baseUrl + url;
    this.data = data;
    this.header = this._mergeHeaders(header); // 合并header
    return this._sendRequest();
  }

  put(url, data = {}, header = {}) {
    // 清空 header 对象
    this.header = {};
    this.method = 'PUT';
    this.url = this.baseUrl + url;
    this.data = data;
    this.header = this._mergeHeaders(header); // 合并header
    return this._sendRequest();
  }

  delete(url, data = {}, header = {}) {
    // 清空 header 对象
    this.header = {};
    this.method = 'DELETE';
    this.url = this.baseUrl + url;
    this.data = data;
    this.header = this._mergeHeaders(header); // 合并header
    return this._sendRequest();
  }

  _sendRequest(beforeResolve, beforeReject) {
    // console.log( beforeResolve);
    // 请求之前做一些事
    if (this.beforeRequest && typeof this.beforeRequest === 'function') {
      this.beforeRequest(this);
    }
    // 发起请求
    return new Promise((resolve, reject) => {
      // console.log(resolve);
      if(typeof(beforeResolve)!=='function'){
        beforeResolve=resolve
        beforeReject=reject
      }
      wx.request({
        url: this.url,
        method: this.method,
        data: this.data,
        header: this.header,
        success: (res) => {
          // console.log(res);
          // console.log( beforeResolve);
          if (res.statusCode === 200) {
            beforeResolve(res.data);
          } else if(res.statusCode === 401){
            const config = {
              url: this.url,
              method: this.method,
              data: this.data,
              header: this.header,
            };
            res.config = config;
          }else {
            const config = {
              url: this.url,
              method: this.method,
              data: this.data,
              header: this.header,
            };
            res.config = config;
            beforeReject(res);
          }
        },
        fail: (err) => {
          // console.log(err);
          const config = {
            url: this.url,
            method: this.method,
            data: this.data,
            header: this.header,
          };
          err.config = config;
          beforeReject(err);
        },
        complete: (res) => {
          // 请求完成以后做一些事情
          if (this.afterRequest && typeof this.afterRequest === 'function') {
            this.afterRequest(res, beforeResolve, beforeReject);
          }
        }
      });
    });
  }
}

// const $http = new axios();
export default axios;
    ```
    

## 2. 在`api`文件夹里面，创建一个`http.js`文件，配置好全局的发送`http`请求
    1. 前向拦截器（请求拦截器），在发起请求之前配置好请求头如：放好`token` 
    2. 后向拦截器（响应拦截器），响应之后要做什么，如token无感刷新，token过期自动调用刷新token接口获取新的token，并且重新发送请求
    
    ```jsx
    import axios from './axios.js';
import handleErrors from "./handleError.js"
const $http = new axios({
  baseUrl: 'http://117.72.95.156:6100/api' // 设置请求根路径
});
wx.$http = $http; // 将$http实例挂载到wx对象上，方便全局访问
// 请求开始之前做一些事情
$http.beforeRequest = function(options) {
  // 获取存储在本地的token
  const token = wx.getStorageSync('accessToken');
  // 如果token存在，则添加到请求头中
  if (token) {
    this.header['accessToken'] = `${token}`;
    }
};
let isRefreshing = false;
let requestQueue=[];
let refreshCount=0
let Max_refresh=10;
// 请求完成之后做一些事情
$http.afterRequest = async function(res, resolve, reject) {
  // console.log('isRefreshing', isRefreshing);
  if (res.statusCode === 401 && !isRefreshing&&refreshCount<=Max_refresh) {
    console.log('endRequest', res);
    const config = res.config;
    console.log(resolve);
    requestQueue.push({ config, resolve, reject });
    // console.log(requestQueue[0]);
    let refreshToken = wx.getStorageSync('refreshToken');
    console.log('refreshToken');
    console.log(refreshToken);
    if (refreshToken === "" || refreshToken === undefined) {
      // 处理登录过期逻辑
      handleLoginExpired();
    } else if (!isRefreshing) {
      isRefreshing = true;
      try {
        const response = await wx.$http.get('/user/refreshToken', {}, { refreshToken });
        if(response.code===1){
          refreshCount++
          console.log(response.data.accessToken);
          wx.setStorageSync('accessToken', response.data.accessToken);
          wx.setStorageSync('refreshToken', response.data.refreshToken);
          console.log(111);
          // 重试队列中的请求
          retryQueue();
        }else{
          handleLoginExpired() ;
        }
        isRefreshing = false;
      } catch (err) {
        handleLoginExpired();
        isRefreshing = false;
      }
    }
  } else if(res.statusCode === 401&&refreshCount>Max_refresh ){
    // 如果一直刷新token后，获取到的token万一还是过期的，直接退出登录
    handleLoginExpired();
  }
  else {
    // 正常处理响应
    if (res.statusCode === 200) {
      resolve(res.data);
    } else {
      handleErrors(res); // 处理请求错误
      reject(res);
    }
  }

  async function handleLoginExpired() {
    wx.removeStorageSync('refreshToken');
    wx.removeStorageSync('accessToken');
    refreshCount = 0;
    // await wait(500);
    wx.reLaunch({
      url: "/pages/index/index"
    });
    wx.showToast({
      title: "登录过期",
      icon: "error"
    });
  }

  async function retryQueue() {
    while (requestQueue.length > 0) {
      // console.log(requestQueue[0]);
      const { config, resolve, reject } = requestQueue.shift();
      // console.log(config);
      // console.log(resolve);
      sendBeforeRequest(config, resolve, reject);
    }
  }

  async function sendBeforeRequest(requestConfig,beforeResolve, beforeReject) {
    // console.log(beforeResolve);
    Object.assign($http,requestConfig)
    // console.log($http);
    $http._sendRequest(beforeResolve, beforeReject)
      .then((response) => {
        // console.log(response);
        // beforeResolve(response.data);
      })
      .catch((error) => {
        // console.log(error);
        // beforeReject(error);
      });
  }
};

export default $http;
    ```
    

## 3. 在`api`文件夹里面创建一个`api.js`文件用来对每个请求进行封装，如：
    
    ```jsx
    import $http from "./http.js"
    // 发送浇水命令
    export function waterFlower(plantTypeId) {
      const data = {
        plantTypeId
      };
      // return request({
      //   endpoint: '/watering',
      //   method: "POST",
      //   data,
      // });
      return $http.post('/watering', data);
    	//return wx.$http.post('/watering', data);//或者用wx.$http
    }
    ```