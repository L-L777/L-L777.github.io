---
title: 软件测试web
date: 2024-10-30 20:18:41
tags: ["web","软测"]
categories: 
    - ["前端"]
    - ["比赛"]
top_img: https://img0.baidu.com/it/u=974935380,271188204&fm=253&fmt=auto&app=138&f=JPEG?w=889&h=500
cover: https://img0.baidu.com/it/u=23143840,4226939095&fm=253&fmt=auto&app=138&f=JPEG?w=641&h=394
---
# 环境

代码编辑器：idea

配置环境

在`xml`文件导入依赖：

```xml
<!--    Junit5单元测试框架-->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.11.2</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-params</artifactId>
      <version> 5.11.3</version>
      <scope>test</scope>
    </dependency>
<!--    selenium自动化测试-->
    <dependency>
      <groupId>org.seleniumhq.selenium</groupId>
      <artifactId>selenium-java</artifactId>
      <version>3.141.59</version> <!-- 请使用最新的版本号 -->
    </dependency>
<!--    文件操作工具-->
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>1.4</version> <!-- 请使用最新的版本号 -->
    </dependency>
```

# 安装驱动

查看谷歌浏览器版本，下载对应版本的驱动（版本不必配会打不开浏览器）

```java
public static void main(String[] args) throws InterruptedException {
//系统打开对应驱动，后面的引号为驱动在本地对应的位置
   System.setProperty("webdriver.chrome.driver", "D:\\chromedriver-win64\\chromedriver-win64\\chromedriver.exe");
        ChromeDriver driver = new ChromeDriver();
        //用谷歌浏览器打开百度
        driver.get("https://www.baidu.com/");
    }
```

# **JUnit 单元测试**

1. **`@BeforeEach`**：
    - 这个注解用于标记在每个测试方法执行之前都要运行的方法。
    - 通常用于设置测试环境，比如初始化测试数据、启动服务器、打开浏览器等。
    - 可以有多个方法使用 `@BeforeEach` 注解，它们将按照定义的顺序执行。
2. **`@AfterEach`**：
    - 这个注解用于标记在每个测试方法执行之后都要运行的方法。
    - 通常用于清理测试环境，比如关闭数据库连接、停止服务器、关闭浏览器等。
    - 也可以有多个方法使用 `@AfterEach` 注解，它们将按照定义的顺序执行。
3. **`@Test`**：
    - 这个注解用于标记一个方法为测试方法。
    - 测试运行器将执行所有标记有 `@Test` 注解的方法。
    - 可以为 `@Test` 注解提供参数，如 `@Test(timeout = 100)` 来设置测试超时时间。
4. **`@ParameterizedTest`**：
    - 这个注解用于标记一个参数化测试方法。
    - 参数化测试允许你为同一个测试方法提供多组输入参数，这样可以在不同的输入条件下执行相同的测试逻辑。
    - 通常与 `@ValueSource`、`@CsvSource`、`@MethodSource` 等注解一起使用来提供测试数据。
5. **`@CsvSource`**：
    - 这个注解用于为 `@ParameterizedTest` 提供测试数据。
    - 数据以逗号分隔的值（CSV）格式提供，每个条目代表一组参数。
    - 例如：`@ParameterizedTest @CsvSource({"value1, value2", "value3, value4"})`。
        
        ```java
        package org.mooctest;
        
        import org.apache.commons.io.FileUtils;
        import org.junit.jupiter.api.AfterEach;
        import org.junit.jupiter.api.BeforeEach;
        import org.junit.jupiter.api.Test;
        import org.junit.jupiter.params.ParameterizedTest;
        import org.junit.jupiter.params.provider.CsvSource;
        import org.openqa.selenium.*;
        import org.openqa.selenium.chrome.ChromeDriver;
        import java.io.File;
        import java.util.Set;
        import java.util.concurrent.TimeUnit;
        
        public class TestTemplate {
            private WebDriver driver;
        
            @BeforeEach
            public void setup() {
                System.setProperty("webdriver.chrome.driver", "D:\\\\chromedriver-win64\\\\chromedriver-win64\\\\chromedriver.exe");
                driver = new ChromeDriver();
                driver.get("https://www.12306.cn/index/index.html");
                driver.manage().window().maximize();
                driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
        
            }
        
            // 请在这里插入JUnit5测试代码
        @ParameterizedTest
        @CsvSource({
                "北京北,南京,2024-10-16,12306_search_001.jpg",
                "北京北,南京,2024-10-24,12306_search_002.jpg",
                "北京北,南京,2024-10-25,12306_search_003.jpg",
                "北京北,南京,2024-10-31,12306_search_004.jpg",
                "北京北,南京,2024-11-08,12306_search_005.jpg",
        })
        void searchNormal(String from,String to,String date,String filename) throws InterruptedException {
            //        输入出发地
            WebElement from_input = driver.findElement(By.id("fromStationText"));
            Thread.sleep(2000);
            from_input.clear();//清空输入框
            Thread.sleep(1000);
            from_input.sendKeys(from+Keys.ENTER);
            Thread.sleep(3000);
        //        输入目的地
            WebElement to_input = driver.findElement(By.id("toStationText"));
            to_input.clear();
            to_input.sendKeys(to+Keys.ENTER);
        //输入出发日期 合法出发日期下的早于当前日期车票的查询
            WebElement train_date = driver.findElement(By.id("train_date"));
            train_date.clear();
            train_date.sendKeys(date);
        //点击查询
            driver.findElement(By.id("search_one")).click();
            Thread.sleep(2000);
        //        切换标签页
            Set<String> windowHandles = driver.getWindowHandles();
            driver.switchTo().window(windowHandles.toArray()[1].toString());
        
            takeScreenshot(filename);
        }
        
            @AfterEach
            public void teardown() {
                this.driver.quit();
            }
        
            private void takeScreenshot(String fileName) {
                File screenshotFile = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
                try {
                    FileUtils.copyFile(screenshotFile, new File(fileName));
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
        
        ```
        

# **自动化测试selenium常用函数**

## **浏览器导航**

• 浏览器的前进、后退、刷新

```java
 driver.navigate().back();
 driver.navigate().forward();
 driver.navigate().refresh();

```

• 打开网站

```java
// 更⻓的⽅法
 driver.navigate().to("https://selenium.dev");
 // 简洁的⽅法
 driver.get("https://selenium.dev");
```

## 函数

- 在页面查找元素，返回值为Element
    
    ```java
    findElement(By);
    ```
    
- 在页面查找元素，返回值为List
    
    ```java
    findElements(By);
    ```
    
- 获取css选择器
    
    ```java
    //id选择器
    WebElement kw_input = driver.findElement(By.id("kw"));
    //类名选择器
     WebElement kw_input = driver.findElement(By.className("kw"));
     //name
      WebElement kw_input = driver.findElement(By.name("kw"));
    ```
- xpath 
```java
//用xpath获取节点
driver.findElement(By.xpath("/html/body/div[1]/div[3]/div/div/div[2]/div/div[1]/div/div[3]/button"));
//  XPath使用//选择符来选择当前节点下的所有后代节点，然后找到文本内容为“17”的<strong>标签。
 driver.findElement(By.xpath("/html/body/div[2]/div[2]/div/div/div/div/div[2]/div[1]/div[2]/div[1]/div/div[2]/div[1]/ul[2]//strong[text()='17']")).click();
```

## **操作测试对象**

1. `click()` 点击/提交对象
    
    ```java
     //找到百度⼀下按钮并点击
    driver.findElement(By.cssSelector("#su")).click();//页面绝大多数区域都可以点击，不只是按钮，页面影藏的标签、不可见的标签不能点
    ```
    
2. `sendKeys("")` 模拟按键输⼊
    
    ```java
    driver.findElement(By.cssSelector("#kw")).sendKeys("输⼊⽂字");
    ```
    
3.  `clear()` 清除文本内容
    
    ```java
    driver.findElement(By.cssSelector("#kw")).sendKeys("我爱游戏");
    driver.findElement(By.cssSelector("#kw")).clear();
    driver.findElement(By.cssSelector("#kw")).sendKeys("我爱学习");
    ```
    
4. `getText()` 获取文本信息
    
    ```java
    String bdtext = driver.findElement(By.xpath("//*[@id="title-content"]/span[1]")).getText();
    System.out.println("打印的内容是："+bdtext);
    ```
    
5. `getTitle()` 获取当前页面标题
6. `getCurrentUrl()` 获取当前页面URL

## **窗口**

### **切换窗口**

- 获取当前页面句柄：
    
    ```java
    driver.getWindowHandle(); //返回值String
    ```
    
- 获取所有页面句柄：
    
    ```java
    driver.getWindowHandles();//返回值Set<Sreing>
    ```
    
- 切换当前句柄为最新页面：
    
    ```java
     String curWindow = driver.getWindowHandle();
     Set<String> allWindow = driver.getWindowHandles();
     for( String w : allWindow){
     if(w!=curWindow){
     driver.switchTo().window(w);
     }
     }
    
    ```
    
    ```java
    Set<String> windowHandles = driver.getWindowHandles();
        driver.switchTo().window(windowHandles.toArray()[1].toString());
    ```
    

### **窗口设置大小**

```java
 //窗⼝最⼤化
 driver.manage().window().maximize();
 //窗⼝最⼩化
 driver.manage().window().minimize();
 //全屏窗⼝
 driver.manage().window().fullscreen();
 //⼿动设置窗⼝⼤⼩
 driver.manage().window().setSize(new Dimension(1024, 768));

```

### **窗口切换**

```java
//获取所有句柄
//获取当前停留⻚⾯句柄
 String curWindow = driver.getWindowHandle();
 Set<String> allWindow = driver.getWindowHandles();
 for( String w : allWindow){
 if(w!=curWindow){
 driver.switchTo().window(w);
 }
 }

```

自动化一般不会存在打开好多个标签页

### **关闭窗口**

```java
driver.close();//关闭当前标签页
 注意：窗⼝关闭后driver要重新定义
     
driver.quit(); //关闭浏览器、释放driver对象

```

## 屏幕截图

```java
 private void takeScreenshot(String fileName) {
        File screenshotFile = ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
        try {
            FileUtils.copyFile(screenshotFile, new File(fileName));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

需要的依赖为：

```xml
<dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>1.4</version> <!-- 请使用最新的版本号 -->
    </dependency>
```

## **等待**

假如写自动化代码出现了`NoSuchElement`错误

- 第一步:在报错的代码前添加Thread. sleep(秒),设置的时间长一点
- 第二步:执行自动化,在自动化打开的页面里打开前端开发者工具,手动检查元素是否真的不存在
1. 自动化打开的页面确实不存在该元素
    1. 手动打开的页面和自动化打开的页面不一样(很可能是登陆和登录状态下页面不一样)
    2. 元素为动态元素
2. 自动化打开的页面确实存在该元素
    - 代码执行的速度比页面渲染的速度要快,页面还没渲染出来,程序已经开始找到了,导致元素没有找到解决办法 ---- 添加等待

### **强制等待**

- `Thread.sleep（）`
    - 优点：使用简单，调试的时候比较有效
    - 缺点：影响运行效率，浪费大量的时间

### **隐式等待**

隐式等待是⼀种智能等待，他可以规定在查找元素时，在指定时间内不断查找元素。如果找到则代码继续执⾏，直到超时没找到元素才会报错。

- `implicitlyWait（）` 参数：Duration类中提供的毫秒、秒、分钟等方法
    - 隐式等待作用域是整个脚本的所有元素
    - 优点：智能等待，作用于全局
    - 缺点：只能查找元素、每次查找元素都要等待

```java
 //隐式等待1000毫秒
 driver.manage().timeouts().implicitlyWait(Duration.ofMillis(1000));
 //隐式等待5秒
 driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));
```

### **显示等待**

显示等待也是⼀种智能等待，在指定超时时间范围内只要满⾜操作的条件就会继续执⾏后续代码

```java
new WebDriverWait(driver, Duration.ofSeconds(3)).until($express)
```

- $press：涉及到`selenium.support.ui.ExpectedConditions`包下的`ExpectedConditions`类
- 返回值：boolean

### **`ExpectedConditions`预定义方法的一些示例：**

- `elementToBeClickable(By locator)` ‒ 用于检查元素的期望是可见的并已启用，以便可以单击。
- `textToBe(Bylocator，String str)` - 检查元素。精确匹配
- `presenceOfElementLocated(Bylocator)` ‒ 检查页⾯的 `DOM` 上是否存在元素。
- `urlToBe(java.lang.String url)` ‒ 检查当前页面的 URL 是⼀个特定的 URL

```java
WebDriverWait wait = new WebDriverWait(driver,Duration.ofSeconds(10));
2 boolean ispass = wait.until(ExpectedConditions.textToBe(By.cssSelector("#s-topleft > a:nth-child(1)"), "新闻"));
 if(ispass){
 System.out.println("测试通过");
 }else {
 System.out.println("测试失败");
 }

```

- 优点：显式等待是智能等待，可以⾃定义显⽰等待的条件，操作灵活
- 缺点：写法复杂、*只做用在当前条件上*

**问题：显示等待和隐式等待一起使用会怎么样？**

可能会导致不可预测的等待时间，谨慎使用

## **弹窗**

`弹窗是在⻚⾯是找不到任何元素的，这种情况怎么处理？`

`使⽤selenium提供的Alert接⼝`

### **警告弹窗+确认弹窗**

```java
 //切换弹窗
Alert alert = driver.switchTo.alert();
 //确认
 alert.accept()
 //取消
 alert.dismiss()

```

### **提示弹窗**

```java
 Alert alert = driver.switchTo.alert();
 alert.sendKeys("hello");
 alert.accept();
 alert.dismiss();

```

## **文件上传**

点击文件上传的场景下会弹窗系统窗口，进行文件的选择。`selenium`无法识别非web的控件，上传文件窗口为系统自带，无法识别窗口元素但是可以使用`sendkeys`来上传指定路径的文件，达到的效果是⼀样的

```java
WebElement ele = driver.findElement(By.cssSelector("body > div > div > 
input[type=file]"));
 ele.sendKeys("D:\\selenium2html\\selenium2html\\upload.html");

```