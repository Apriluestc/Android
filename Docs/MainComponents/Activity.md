### 安卓四大组件之 Activity

&nbsp;&nbsp;&nbsp;&nbsp;
Activity 是一个应用组件，用户可与之交互，每个 Activity 都会获得一个与用户交互的界面窗口

##### 启动过程

##### 

##### 生命周期

- 什么是 Activity
  - Activity 可直接翻译为活动，在 Android 中更多的是代表手机屏幕，是 Android 四大组件之一，提供了与用户交互的可视化界
  面（GUI），大多数的 App 是由多个屏幕组成的
  - Android 系统使用 Task 栈来存储 Activity，可以理解为 Activity 栈，当一个 Activity 启动另外一个 Activity 时，第二个 Activity 压入第一
个 Activity 的栈中，此时两个 Activity 是放在同一个 Task 中的，当我们按下回退键时，第二个 Activity 就会从栈中弹出，第一个 Activity 回到
栈顶，即显示到当前屏幕

- 生命周期解析

&nbsp;&nbsp;&nbsp;&nbsp;

