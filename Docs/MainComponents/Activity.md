### 安卓四大组件之 Activity

【安卓四大组件之一 Activity，本篇博客包括 Activity 的生命周期、启动过程、数据传递】

* [生命周期](#生命周期)
* [启动模式](#启动模式)
* [数据传递](#数据传递)

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

![](https://github.com/Apriluestc/Android/blob/master/Images/1673302-675030a52913d778_wps%E5%9B%BE%E7%89%87.png)

- 方法详解
  - onCreate()：第一次创建 Activity 时，会调用这个方法一般会在这个方法初始化 UI 元素
  - onRestart()：在一个 Activity 被遮挡又重新被显示时调用，他后面总是 onStart() 方法
  - onStart()：这个方法在呈现 Activity 给用户调用，该方法被执行便代表着 Activity 可以被感知即可见但不具备交互能力，onStart() -> onStop() 阶段
  的 Activity 都是被用户可见的称之为 visible lifetime，可以在这个方法中执行 UI 动画、播放音频或者其他需要在屏幕上出现的 Activity 的内容，一
  般除非有必要很少在该方法被调用时执行自己的命令
  - onResume()：在将一个 Activity 移到前台之前调用，该方法被执行便代表着 Activity 可以与用户交互，之后 onResume() -> onPause() 解读那的 Activity 可以响应
  用户交互称之为 foreground lifetime，在这个方法中播放动画、更新 UI 元素、其他功能或者数据组装、也可以初始化你在 onPause() 中释放的组件、资源等
  - onPause()：在进入后台之前调用，该方法被执行便代表着 Activity 不可以与用户交互，在这个方法中一般要停止和 Activity 有关的音效、UI 动画等等，如
  果 Activity 返回前台，则这个方法后面会调用 onResume() 方法，如果 Activity 被遮挡，则这个方法后面会调用 onStop()
  - onStop()：这个方法在 Activity onPause() 之后进入后台调用，该方法执行便代表着 Activity 不可以被感知、不可见，更不具备加交互能力，一般在该方法中执
  行释放部分资源，保存数据等等，如果 Activity 回到前台  ，他后面时 onRestart() 方法，如果它即将从内存中移除，则它后面是 onDestory()
  - onDestory()：这是 Activity 被销毁之前可以从系统接收到的最后一个回调，销毁一个 Activity 的方法之一，就是调用 finish() 方法，当系统需要回收内
  存时，或者按下 Back 键，也会销毁 Activity，如果 Activity 包含后台线程或者需要长时间运行的资源，此时 Activity 被销毁可能会导致内存泄
  漏，所以一定要在该方法中释放所有相关耗时或者其他资源，但是有一部分需要在显示下一个 Activity 时用到，可以不再 onDestory() 中进行，因为 onDestory() 方法
  不一定会及时执行，可以在 onStop() -> onPause() 适当执行
  - 另外还可以在 Application 中注册 Activity 的生命周期监听机制

```java
public void registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
    @Override
    public void onActivityStopped(Activity activity) {}

    @Override
    public void onActivityStarted(Activity activity) {}

    @Override
    public void onActivitySaveInstanceState(Activity activity, Bundle outState) {}

    @Override
    public void onActivityResumed(Activity activity) {}

    @Override
    public void onActivityPaused(Activity activity) {}
    
    @Override
    public void onActivityDestroyed(Activity activity) {}
    
    @Override
    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {}    
}
```

##### 启动模式

- Standard 模式：Standard 模式时 Android 的默认启动模式，在这种模式下，Activity 可以有多个实例，每次启动 Activity，无论任务栈中是否已经存在
这个 Activity 实例，系统都会创建一个新的实例，这个 Activity 它的 onCreate()、onStart()、onResume() 方法都会被调用

- SingleTop：当一个 SingeTop 模式的 Activity 已经位于栈顶时，再去启动他时，不在创建实例，如果不在栈顶就会创建实例，当启动的 Activity 已经位
于栈顶时，则直接使用它而不是创建新的实例，同时它的 onNewIntent() 方案会被调用，需要注意的是这个 Activity 的 onCreate()、onStart() 方法
不会被调用，因为它并没有发生改变

- 另外说明：Standard 和 SingleTop 启动模式都是在原任务栈中进行实例化，不会启动新的 Task，即便指定了 taskAffinity 属性，这个属性标识了一
个 Activity 所需任务栈的名字，默认情况下，所有 Activity 的任务栈的名字为应用的包名，但是也可以单独指定每一个 Activity 的 taskAffinity 属性覆盖其默
认值，一个任务栈的名字决定于这个任务栈的根 Activity（root activity）的 task-Affinity，在概念上具有相同的 taskAffinity 的 Activity（即设置了
相同 taskAffinity 属性的 activity）属于同一个任务栈，但是 task-Affinity 属性不会对 Standard 和 SingleTop 模式有任何影响，假如你指定了该属性为不同
于包名的值，这两种启动模式下也不会创建新的（不同于包名的）Task

- SingleTask：如果启动的 Activity 已经存在于栈中，则会将 Activity 移动到栈顶，并将上面的 Activity 出栈，否则会创建新的实例，如果
在指定的任务栈中有要启动的 Activity 的实例不管是否位于栈顶，不管栈内其他 Activity 的启动模式是不是 single-Task，就接使用该实例，回调该实
例的 onNewIntent 方法并将该 Activity 之上的所有 Activity 出栈，如果没有则创建一个新的实例，注意：该模式会先匹配所需任务栈，即如果有 Activity 不
管是 A 应用还是 B 应用设置了 taskAffinity 属性，就会到对应（如果没有设置就使用默认所属应用包名的）任务栈查找 Activity，如果没有该任务栈就创建一个

- SingleInstance：一个 Activity 一个栈，启动具有该模式的 Activity 会启动一个新的任务栈来管理这个 Activity，该模式具备 singleTask 模式的所有
特性，区别在于这种模式下的 Activity 会单独占用一个 Task 栈，具有全局唯一性，即整个系统中只有这一个实例，由于栈内复用的特性，后续均不会创建
新的 Activity 实例，除非这个特殊的任务栈被销毁了，singleInstance 模式启动的 Activity 在整个系统中是单例的，如果在启动这样的 Activiyt 时，已经存在
了一个实例，那么会把它所在的任务调度到前台，重用这个实例

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
