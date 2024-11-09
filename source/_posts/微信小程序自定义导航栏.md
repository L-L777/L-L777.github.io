---
title: 微信小程序自定义导航栏和TabBar（用了Tdesign组件库）
date: 2024-11-07 19:45:16
tags: ["小程序","微信"]
categories: 
    - ["前端"]
    - ["小程序"]
top_img: https://img2.baidu.com/it/u=1270662364,2069731781&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
cover: https://img2.baidu.com/it/u=534490904,3498620580&fm=253&fmt=auto&app=120&f=JPEG?w=750&h=500
---

## 自定义导航栏

1. 现在`app.json` 文件中把导航栏属性改为自定义

```json
 
 "window": {
    "navigationStyle": "custom"
  },
```

2. 然后再`app.js`文件中调用`wx.getWindowInfo()` 接口，全局获取手机顶部状态栏的高度，如果要把高度大小单位换算成rpx，则还需获取屏幕宽度，运用公式进行换算


```jsx
// app.js
App({
  onLaunch() {
    const windowInfo = wx.getWindowInfo()
    const statusBarHeight=windowInfo.statusBarHeight
    const screenWidth=windowInfo.screenWidth
    this.globalData.navBarHeight = statusBarHeight*750/screenWidth + 96;
    // console.log(statusBarHeight*750/screenWidth + 96);
    
  },
  globalData: {
    navBarHeight: 0
  }
})

```

这里设置自定义导航栏高度为96rpx

3. 在对应页面的`js`文件中获取`app.js`中的全局导航栏+手机状态栏高度大小
    
    ```jsx
    // pages/home/home.js
    const app = getApp();
    Page({
    
      /**
       * 页面的初始数据
       */
    data: {
        navBarHeight: app.globalData.navBarHeight,
        tabBarHeight: app.globalData.tabBarHeight
      },
    
     
    })
    ```
    

4. 在`navBar`组件的`wxml`文件中引入自定义导航栏
    
    ```html
    <t-navbar
      t-class-placeholder="t-navbar-placeholder"
      t-class-content="t-navbar-content"
      class="custom-navbar"
      title="标题文字"
    />
    //页面主要内容margin-top值为app.js中获取到的导航栏加手机状态栏的高度
    <view class="home" style="margin-top:{{navBarHeight}}rpx;">
      <text>pages/home/home.wxml</text>
    </view>
    ```
    
    ```css
    .custom-navbar{
      position: fixed;
      top: 0;
      left: 0;
      /* height: 100px !important; */
      --td-navbar-color: #fff;
      --td-navbar-bg-color: #0052d9;
    }
    
    ```
    

## 自定义`TabBar`

1. 现在`app.json` 文件中把`tabBa`r属性改为自定义

```json
 
  "tabBar": {
    "custom": true,
    "list": [
      {
        "pagePath": "pages/home/home",
        "text": "首页"
      },
      {
        "pagePath": "pages/form/form",
        "text": "工单"
      }
    ]
  },
```

2. 然后再`app.js`文件中调用`wx.getWindowInfo()` 接口，全局获取手机顶部状态栏的高度，如果要把高度大小单位换算成rpx，则还需获取屏幕宽度，运用公式进行换算

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/0dba967f-2d6c-43ba-b3c3-386e6a153bfa/80e1047f-a362-4028-b8cb-bce7525731fc/image.png)

```jsx
// app.js
App({
  onLaunch() {
     const windowInfo = wx.getWindowInfo()
    const statusBarHeight=windowInfo.statusBarHeight
    const screenWidth=windowInfo.screenWidth
    this.globalData.navBarHeight = statusBarHeight*750/screenWidth + 96;
    // console.log(statusBarHeight*750/screenWidth + 80);
    this.globalData.tabBarHeight=statusBarHeight*750/screenWidth + 80;
    
  },
   globalData: {
    navBarHeight: 0,
    tabBarHeight:0,
  },
})

```

这里设置自定义导航栏高度为96rpx

3. 在对应页面的`js`文件中获取`app.js`中的全局导航栏+手机状态栏高度大小
    
    ```jsx
    // pages/home/home.js
    const app = getApp();
    Page({
    
      /**
       * 页面的初始数据
       */
     data: {
        navBarHeight: app.globalData.navBarHeight,
        tabBarHeight: app.globalData.tabBarHeight
      },
    
     
    })
    ```
    

4. 在`tabBar`组件的`wxml`文件中引入自定义导航栏
    
    ```html
    <!--components/tabBar/tabBar.wxml-->
    <t-tab-bar t-class="t-tab-bar" value="{{active}}" bindchange="onChange" theme="tag" split="{{false}}">
      <!-- <t-tab-bar-item wx:for="{{list}}" wx:key="value" value="{{item.value}}" icon="{{item.icon}}" >
        {{item.label}}
      </t-tab-bar-item> -->
      <t-tab-bar-item   value="label_1" icon="home" data-key="1">
        首页
      </t-tab-bar-item>
      <t-tab-bar-item   value="label_2" icon="app" data-key="2">
      工单
      </t-tab-bar-item>
    </t-tab-bar>
    ```
    

5. 在tabBar组件的`js`文件中

```jsx
// components/tabBar/tabBar.js
Component({

  /**
   * 组件的属性列表
   */
  properties: {
   active: {
      type:String,
    }
  },

  /**
   * 组件的初始数据
   */
  data: {
    value: '',
    list: [
      { value: 'label_1', label: '首页', icon: 'home' },
      { value: 'label_2', label: '应用', icon: 'app' },
      { value: 'label_3', label: '聊天', icon: 'chat' },
      { value: 'label_4', label: '我的', icon: 'user' },
    ],
  },

  /**
   * 组件的方法列表
   */
  methods: {
    
    onChange(e) {
      this.setData({
        value: e.detail.value,
      });
      
      switch(e.detail.value){
        case "label_1":
          wx.switchTab({ url: '/pages/home/home', });
          // console.log(e.detail.value);
        break;
        case 'label_2':
          wx.switchTab({ url: '/pages/form/form', });
          // console.log(e.detail.value);
          break;
      default:
            break;
      }
    },
     /**
   * 在组件实例进入页面节点树时执行
   */
  attached() {
   
  }
    
  }
})
```

6. 在对应页面的wxml文件引入 ，传递状态

```html
<tabBar active="label_1"></tabBar>
```