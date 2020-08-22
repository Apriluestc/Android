### 安卓四大组件之 Activity

Activity 是一个应用组件，用户可与之交互，每个 Activity 都会获得一个与用户交互的界面窗口

##### 生命周期

- 什么是 Activity
  - Activity 可直接翻译为活动，在 Android 中更多的是代表手机屏幕，是 Android 四大组件之一，提供了与用户交互的可视化界
  面（GUI），大多数的 App 是由多个屏幕组成的
  - Android 系统使用 Task 栈来存储 Activity，可以理解为 Activity 栈，当一个 Activity 启动另外一个 Activity 时，第二个 Activity 压入第一
个 Activity 的栈中，此时两个 Activity 是放在同一个 Task 中的，当我们按下回退键时，第二个 Activity 就会从栈中弹出，第一个 Activity 回到
栈顶，即显示到当前屏幕

- 生命周期解析
  - 当 Activity 首次被创建时，会调用 onCreate() 方法，接着当显示给用户时，调用 onStart() 方法，如果要让 Activity 位于前台的话就需要
  调用 onResume() 方法，此时 Activity 位于栈顶
  - 当有另一个 Activity 覆盖当前 Activity 时，这个时候调用 onPause() 方法，将前一个 Activity 的数据保存起来
  - 此时，如果你想让前一个 Activity 不会再显示的话，调用 onStop() 方法停止该 Activity，但是如果你想让他会到前台的话，重新获得该焦点可以
  调用 onResume() 方法
  - onStop() 之后，你可以调用 onDestory() 方法来销毁该 Activity，但是该 Activity 最后一次被调用了，可以通过 finish() 关闭 Activity
  - 当内存资源不足时，就可能杀死处于 onPause() 的 Activity 所在的进程，但是这种极端情况很少发生

- 生命周期图解

##### 启动模式

- Standard 模式：Standard 模式时 Android 的默认启动模式，在这种模式下，Activity 可以有多个实例，每次
启动 Activity，无论任务栈中是否已经存在这个 Activity 实例，系统都会创建一个新的实例

- SingleTop：当一个 SingeTop 模式的 Activity 已经位于栈顶时，再去启动他时，不在创建实例，如果不在栈顶就会创建实例

- SingleTask：如果启动的 Activity 已经存在于栈中，则会将 Activity 移动到栈顶，并将上面的 Activity 出栈，否则会创建新的实例

- SingleInstance：一个 Activity 一个栈

###### 数据传递

- 显示启动

Intent 内部直接生命要启动的 Activity 所对应的 class

```java
Intent intent = new Intent(MainActivity.this, target.class);
startActivity(intent);
```

- 隐式启动

隐式传递需要根据 Activity 的 action 和 category 进行匹配

```xml
<intent-filter>
    <action android:name="target" />
    <category android:name="android.intent.category.DEFAULT" />
</intent-filter>
```

```java
Intent intent = new Intent("target");
intent.addCategory(intent.CATEGORY_DEFAULT);
startActivity(intent);
```

- 数据回传
