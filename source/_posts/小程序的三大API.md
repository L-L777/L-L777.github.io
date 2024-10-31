---
title: 小程序的三大API
date: 2024-10-30 20:53:49
tags: ["小程序"]
categories: 
    - ["前端"]
    - ["小程序"]
top_img: https://img0.baidu.com/it/u=3291332156,2373920352&fm=253&fmt=auto&app=138&f=JPEG?w=890&h=500
cover: https://img2.baidu.com/it/u=534490904,3498620580&fm=253&fmt=auto&app=120&f=JPEG?w=750&h=500
---
# 小程序3大API

微信小程序提供了三大类 API，涵盖了各种功能，帮助开发者实现业务需求。具体如下：

### 1. **界面 API（UI APIs）**

这些 API 主要用于控制小程序的界面行为和视觉效果，如导航、交互反馈、菜单控制等。

**常用的界面 API 包括：**

- **导航栏相关**
    - `wx.setNavigationBarTitle`：设置当前页面的标题。
    - `wx.setNavigationBarColor`：设置导航栏颜色。
- **界面交互**
    - `wx.showToast`：显示提示框，短暂提示用户某个操作成功或失败。
    - `wx.showLoading`：显示加载中的提示框，常用于异步请求时提示用户等待。
    - `wx.hideLoading`：隐藏 `wx.showLoading` 触发的加载框。
    - `wx.showModal`：显示模态弹窗，提示用户确认或取消某个操作。
    - `wx.showActionSheet`：显示操作菜单，允许用户从多个选项中选择。
- **导航控制**
    - `wx.navigateTo`：跳转到新页面，保留当前页面。
    - `wx.redirectTo`：跳转到新页面，关闭当前页面。
    - `wx.switchTab`：跳转到指定的 Tab 页面。
    - `wx.navigateBack`：返回上一个页面，支持返回多级页面。
    - `wx.reLaunch`：关闭所有页面并打开到指定页面。
- **下拉刷新**
    - `wx.startPullDownRefresh`：主动触发页面下拉刷新效果。
    - `wx.stopPullDownRefresh`：停止当前页面的下拉刷新效果。
- **背景及菜单**
    - `wx.setBackgroundColor`：设置页面的背景颜色。
    - `wx.setBackgroundTextStyle`：设置下拉刷新背景文字样式。
    - `wx.showShareMenu`：显示分享按钮。
    - `wx.hideShareMenu`：隐藏分享按钮。

**示例代码：**

```jsx
javascript
复制代码
wx.showToast({
  title: '操作成功',
  icon: 'success',
  duration: 2000
});

```

### 2. **数据 API（Data APIs）**

数据 API 涵盖了获取和存储数据的功能，主要用于与后端服务器通信、缓存数据、本地存储等。

**常用的数据 API 包括：**

- **网络请求**
    - `wx.request`：发送 HTTP 请求，支持 GET、POST 等方法。
    - `wx.uploadFile`：将本地文件上传到服务器。
    - `wx.downloadFile`：从服务器下载文件。
- **WebSocket**
    - `wx.connectSocket`：创建 WebSocket 连接，用于实时通讯。
    - `wx.onSocketOpen`：监听 WebSocket 连接打开事件。
    - `wx.sendSocketMessage`：通过 WebSocket 发送消息。
    - `wx.closeSocket`：关闭 WebSocket 连接。
- **文件系统**
    - `wx.getFileSystemManager`：获取文件系统管理器，用于管理文件读写操作。
    - `wx.saveFile`：将文件保存到本地。
    - `wx.getSavedFileList`：获取本地已保存的文件列表。
- **本地存储**
    - `wx.setStorageSync`：同步设置本地存储的数据。
    - `wx.getStorageSync`：同步获取本地存储的数据。
    - `wx.clearStorageSync`：清空本地存储的所有数据。
- **数据库**
    - 小程序还支持云开发，利用云数据库进行数据存储和查询。
    - `wx.cloud.database`：获取云数据库的引用。
    - `db.collection()`：获取云数据库中的集合。
    - `collection.add()`：向集合中添加数据。
    - `collection.get()`：从集合中查询数据。

**示例代码：**

```jsx
javascript
复制代码
wx.request({
  url: 'https://api.example.com/data',
  method: 'GET',
  success(res) {
    console.log(res.data);
  },
  fail(err) {
    console.log(err);
  }
});

```

### 3. **设备 API（Device APIs）**

设备 API 提供了与用户设备交互的功能，如系统信息、网络状态、蓝牙、Wi-Fi 等操作。

**常用的设备 API 包括：**

- **系统信息**
    - `wx.getSystemInfo`：获取设备系统信息，如屏幕尺寸、操作系统、微信版本等。
    - `wx.getSystemInfoSync`：同步获取系统信息。
- **网络状态**
    - `wx.getNetworkType`：获取设备的网络类型（如 Wi-Fi、2G、3G、4G）。
    - `wx.onNetworkStatusChange`：监听网络状态变化事件。
- **位置**
    - `wx.getLocation`：获取用户的当前位置，经纬度、速度等信息。
    - `wx.chooseLocation`：打开地图选择位置。
    - `wx.openLocation`：使用内置地图打开指定的地理位置。
- **蓝牙**
    - `wx.openBluetoothAdapter`：初始化蓝牙模块。
    - `wx.startBluetoothDevicesDiscovery`：开始搜索附近的蓝牙设备。
    - `wx.connectBLEDevice`：连接低功耗蓝牙设备。
- **Wi-Fi**
    - `wx.startWifi`：初始化 Wi-Fi 模块。
    - `wx.getConnectedWifi`：获取当前连接的 Wi-Fi 信息。
- **电量**
    - `wx.getBatteryInfo`：获取设备电量信息。
    - `wx.onBatteryInfoChange`：监听电量变化。
- **震动**
    - `wx.vibrateLong`：使手机发生长震动。
    - `wx.vibrateShort`：使手机发生短震动。
- **扫码**
    - `wx.scanCode`：调用相机扫码功能，支持二维码和条形码。

**示例代码：**

```jsx
javascript
复制代码
wx.getSystemInfo({
  success(res) {
    console.log(res.model);
    console.log(res.pixelRatio);
    console.log(res.windowWidth);
    console.log(res.windowHeight);
  }
});

```

---

### 总结

微信小程序的三大类 API 提供了从界面控制、数据交互到设备访问的广泛功能支持。通过灵活运用这些 API，开发者可以实现小程序的各种功能，如页面导航、网络请求、系统状态获取等，构建功能强大且体验流畅的小程序应用。