---
title: 小程序项目配置---全局websocket
date: 2024-10-30 21:18:06
tags: ["小程序","配置"]
categories: 
    - ["前端"]
    - ["小程序"]
    - ["项目配置"]
top_img: https://img0.baidu.com/it/u=1421480513,129599327&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
cover: https://img1.baidu.com/it/u=3719565749,2440019383&fm=253&fmt=auto&app=138&f=PNG?w=320&h=320
---

```jsx
//websocket.js
let socket = null;
const listeners = [];
let heartbeatInterval = null;
const HEARTBEAT_INTERVAL = 10000; // 10秒心跳间隔
const WEB_SOCKET_URL = 'ws://117.72.95.156:8080/ws';
function connectWebSocket(url) {
  if(socket&& socket.readyState === 1){
    console.log('WebSocket已连接,无需重连');
    return;
  }
  const connectUrl=`${WEB_SOCKET_URL}/${url}`
  socket = wx.connectSocket({
    url: connectUrl // 确保这里是 url: connectUrl
  });
  if(socket){
    socket.onOpen(() => {
      console.log('WebSocket连接已打开');
      startHeartbeat(); // 连接成功后启动心跳
    });
  
    socket.onMessage((message) => {
      // console.log('收到原始消息:', message);
      try {
        // 确保消息是字符串格式
        if (typeof message.data === 'string') {
          const parsedData = JSON.parse(message.data);
          if(parsedData.event==='heartbeat'){
            // console.log("收到心跳消息");
        return;
          }
          // 处理解析后的数据
          listeners.forEach(listener => listener(parsedData));
        } else {
          console.error('接收到的消息不是字符串格式');
        }
        // 重置心跳
        resetHeartbeat();
      } catch (error) {
        // console.error('JSON parsing error:', error);
        // 处理解析错误
      }
    });
  
    socket.onError((error) => {
      console.error('WebSocket错误:', error);
      // 可能需要通知用户连接错误
      wx.showToast({
        title: 'WebSocket连接失败',
        icon: 'none',
        duration: 2000
      });
    });
  
    socket.onClose(() => {
      console.log('WebSocket连接已关闭');
      stopHeartbeat(); // 关闭时停止心跳
      // 可能需要重新连接或通知用户
    });
  }
}

function disconnectWebSocket() {
  if (socket) {
    socket.close();
    console.log('WebSocket连接已断开');
    stopHeartbeat(); // 停止心跳
  }
}

function sendMessage(msg) {
  if (socket && socket.readyState === 1) {
    socket.send({ data: msg });
  } else {
    console.error('WebSocket未连接');
  }
}

function registerMessageListener(listener) {
  listeners.push(listener);
}

function unregisterMessageListener(listener) {
  const index = listeners.indexOf(listener);
  if (index > -1) {
    listeners.splice(index, 1);
  }
}

function startHeartbeat() {
  heartbeatInterval = setInterval(() => {
    sendMessage('ping'); // 发送心跳消息
    console.log('发送心跳: ping');
  }, HEARTBEAT_INTERVAL);
}

function resetHeartbeat() {
  if (heartbeatInterval) {
    clearInterval(heartbeatInterval); // 清除当前心跳定时器
    startHeartbeat(); // 重新启动心跳
  }
}

function stopHeartbeat() {
  if (heartbeatInterval) {
    clearInterval(heartbeatInterval); // 停止心跳
    heartbeatInterval = null;
  }
}

export { connectWebSocket, sendMessage, registerMessageListener,unregisterMessageListener, disconnectWebSocket };
```