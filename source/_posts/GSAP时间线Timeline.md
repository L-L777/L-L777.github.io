---
title: GSAP时间线Timeline
date: 2024-11-04 20:43:53
tags: ["GSAP","动画库"]
categories: 
    - ["前端"]
    - ["GSAP"]
top_img: https://img2.baidu.com/it/u=3205109182,3319667232&fm=253&fmt=auto&app=120&f=JPEG?w=750&h=50
cover: https://img0.baidu.com/it/u=2064138448,2586463179&fm=253&fmt=auto&app=138&f=JPEG?w=1000&h=500
---
# 时间轴

[**时间轴**](https://gsap.com/docs/v3/GSAP/Timeline)是一种功能强大的排序工具，可充当补间和其他时间轴的容器，从而可以轻松地整体控制它们并精确管理它们的时间。如果没有时间轴，构建复杂的序列会更加麻烦，因为您需要为每个动画使用**`delay`** 。例如：

```
// WITHOUT Timelines (only using tweens with delays):
gsap.to("#id", { x: 100, duration: 1 });
gsap.to("#id", { y: 50, duration: 1, delay: 1 });//wait 1 second
gsap.to("#id", { opacity: 0, duration: 1, delay: 2 });//wait 2 seconds

```

如果您想让第一个动画更长怎么办？此后您需要调整*每次*延迟。如果您想**`pause()`**整个序列或**`restart()`**它或**`reverse()`**它即时或重复两次怎么办？这可能会变得相当混乱，但 GSAP 的时间表使其变得异常简单：

```
//WITH Timelines (cleaner, more versatile)
var tl = gsap.timeline({repeat: 2, repeatDelay: 1});
tl.to("#id", {x: 100, duration: 1});
tl.to("#id", {y: 50, duration: 1});
tl.to("#id", {opacity: 0, duration: 1});

// then we can control the whole thing easily...
tl.pause();
tl.resume();
tl.seek(1.5);
tl.reverse();
...

```

现在我们可以调整时间，而不必担心延迟的影响！增加第一个补间的持续时间，一切都会自动调整。

# 在[**时间轴**](https://gsap.com/docs/v3/GSAP/Timeline#positioning-animations-in-a-timeline)中定位动画

默认情况下，动画会添加到时间线的**末尾**，以便它们按顺序排列，但您可以使用[**位置参数**](https://gsap.com/resources/position-parameter)来精确控制事物的放置位置。它通常位于**vars**参数之后，并使用带有以下选项的灵活语法：

- 从时间线开始测量的**绝对时间**（以秒为单位），作为**数字，**如**`3`**

```
// insert exactly 3 seconds from the start of the timeline
tl.to(".class", { x: 100 }, 3);

```

- **标签**，例如**`"someLabel"`** 。*如果标签不存在，它将被添加到时间线的末尾。*

```
// insert at the "someLabel" label
tl.to(".class", { x: 100 }, "someLabel");

```

- **`"<"`**上一个动画的**开始***。*将**`<`**视为返回上一个动画开头的指针。*

```
// insert at the START of the  previous animation
tl.to(".class", { x: 100 }, "<");

```

- **`">"`** - 上一个动画的**结束***。*将**`>`**视为指向上一个动画结尾的指针。*

```
// insert at the END of the previous animation
tl.to(".class", { x: 100 }, ">");

```

- 复杂字符串，其中**`"+="`**和**`"-="`**前缀表示**相对**值。*当**`"<"`**或**`">"`**后面有数字时，它被解释为相对数字，因此**`"<2"`**与**`"<+=2"`**相同。*示例：
    - **`"+=1"`** - 时间线结束后 1 秒（创建间隙）
    - **`"-=1"`** - 时间线结束前 1 秒（重叠）
    - **`"myLabel+=2"`** - 标签**`"myLabel"`**过去 2 秒
    - **`"<+=3"`** - 上一个动画开始后 3 秒
    - **`"<3"`** - 与**`"<+=3"`**相同（见上文）（ **`"+="`**在**`"<"`**或**`">"`**后面时隐含）
    - **`">-0.5"`** - 上一个动画结束前 0.5 秒。就像说*“上一个动画的结尾加-0.5”*
- 基于**百分比**的复杂字符串。当紧跟在**`"+="`**或**`"-="`**前缀之后时，百分比基于所**插入动画**的[**总持续时间**](https://gsap.com/docs/v3/GSAP/Tween/totalDuration())。当紧跟在**`"<"`**或**`">"`**之后时，它基于上**一个动画**的[**总持续时间**](https://gsap.com/docs/v3/GSAP/Tween/totalDuration())。*注意：总持续时间包括重复/溜溜*。示例：
    - **`"-=25%"`** - 与时间线末尾重叠插入动画总持续时间的 25%
    - **`"+=50%"`** - 超出时间线末尾插入动画总持续时间的 50%，从而创建间隙
    - **`"<25%"`** - 进入上一个动画的 25%（从开始处算起）。与**`">-75%"`**相同，与上一个动画**结束**时相比负 75%。
    - **`"<+=25%"`** - 上一个动画开始之后插入动画总持续时间的 25%。与**`"<25%"`**不同，“<25%”的百分比基于**前一个动画的**总持续时间，而紧随**`"+="`**或**`"-="`**之后的任何内容都基于**插入动画的**总持续时间。
    - **`"myLabel+=30%"`** - 插入动画总持续时间超过标签**`"myLabel"`**的 30%。

# 特殊属性

以下是一些GSAP时间线的常用特殊属性：

- **`repeat`**：动画重复的次数。如果设置为 `1`，则动画会无限重复。
- **`repeatDelay`**：两次重复之间的间隔时间（以秒为单位）。
- **`yoyo`**：如果为 `true`，则每次重复播放都会前后交替进行，使得动画来回运动，类似于溜溜球。
- **`delay`**：时间轴开始之前的延迟（以秒为单位）。
- **`onComplete`**：时间线播放完毕后调用的函数。
- **`defaults`**：可以设置默认值，这样在时间线中创建的每个动画都会继承这些默认值，如 `duration` 和 `ease`。
- **`onRepeat`**：在每次重复时调用的函数。
- **`onReverseComplete`**：当时间线反向播放完成时调用的函数。

## Getter / Setter 方法

时间线提供了一些方法来获取或设置时间线的状态：

- **`time()`**：播放头的本地位置（当前时间，以秒为单位），不包括任何重复或 `repeatDelays`。
- **`progress()`**：介于0和1之间的值，指示播放头的位置，其中0处在开始位置，0.5处在中途完成，1处在结束位置。
- **`duration()`**：动画的持续时间（以秒为单位），不包括任何重复或 `repeatDelays`。
- **`delay()`**：动画的初始延迟（动画开始之前的时间长度，以秒为单位）。

## 嵌套时间线

你可以在时间线中嵌套另一个时间线，这样可以模块化你的代码，使其更易于维护。例如：

```jsx

var master = gsap.timeline();
master.add(intro()).add(middle(), "+=2").add(conclusion(), "-=1");

```

## 其他时间线特性

- **`timeScale()`**：可以加速或减慢整个时间线的播放速度。
- **`progress()` 或 `totalProgress()`**：获取时间线的进度，`totalProgress()` 包括任何重复。
- **`killTweensOf()`**：杀死特定对象内的时间线动画。
- **`getTweensOf()`**：获取特定对象的动画。
- **`getChildren()`**：获取时间线中的所有动画和时间线。
- **`currentLabel()`** 或 **`nextLabel()`** 和 **`previousLabel()`**：获取或查找时间线中特定位置的标签。

### 示例代码

```jsx

var tl = gsap.timeline({
  repeat: 3,
  repeatDelay: 1,
  onComplete: myFunction
});
tl.to(".class", { duration: 1, x: 200, y: 100 });
tl.addLabel("spin", 3);
tl.to(".class", { duration: 2, rotation: "+=360" }, "spin");

```

通过这些属性和方法，你可以创建复杂的动画序列，精确控制动画的播放、重复和回调。GSAP的时间线功能非常强大，能够满足大多数复杂的动画需求。