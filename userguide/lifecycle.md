## 驱动活动生命周期

在  Robolectric 2.2 版本之前, 大多数测试创建 Activity 都是直接调用构造函数, (`new MyActivity()`)然后手动调用生命周期方法, 例如 `onCreate()`. 还广泛使用的是 `ShadowActivity` 中的一组方法(例如 `ShadowActivity.callOnCreate()` ), 它们是 `ActivityController` 的前身.

太混乱了. Robolectric API `ActivityController` 改变了这一切. 它的目标是去模拟 Android 创建 Activity 并且通过生命周期驱动它们.

`ActivityController` 是一个流畅的 API, 在 Robolectric 2.0 引入, 在 2.2 之后被需要. 除了调用 `onCreate()` 之类的方法, 它还可以确保活动的内部状态与生命周期一致. 这包括将 Activity 附加到 Window 并使系统服务像 `LayoutInflater` 一样可用. 

## 现在我该怎么做?

你不用直接创建一个 `ActivityController` 而是使用 `Robolectric.buildActivity()`. 对于大多数只需要初始化 Activity 的基本测试, 你可以使用下面这行代码: 

```java
Activity activity = Robolectric.buildActivity(MyAwesomeActivity.class).create().get();
```

这将会创建一个新的 `MyAwesomeActivity` 实例并且调用生命周期的 `onCreate()`方法.

如果你想着检测一些在 `onResume()` 而不是 `onCreate()` 时发生的事件该怎么办? 简单!

```java
ActivityController controller = Robolectric.buildActivity(MyAwesomeActivity.class).create().start();
Activity activity = controller.get();
// assert that something hasn't happened
activityController.resume();
// assert it happened!
```

相似的方法还有 `start()`, `pause()`, `stop()` 和 `destory()`. 所以, 如果你想测试完整的创建生命周期: 

```java
Activity activity = Robolectric.buildActivity(MyAwesomeActivity.class).create().start().resume().visible().get();
```

你可以模拟用 intent 开启一个 Activity.

```java
Intent intent = new Intent(Intent.ACTION_VIEW);
Activity activity = Robolectric.buildActivity(MyAwesomeActivity.class).withIntent(intent).create().get();
```

... 或者恢复保存的实例状态

```java
Bundle savedInstanceState = new Bundle();
Activity activity = Robolectric.buildActivity(MyAwesomeActivity.class)
    .create()
    .restoreInstanceState(savedInstanceState)
    .get();
```

查看[`ActivityController` Java 文档](http://robolectric.org/javadoc/latest/org/robolectric/util/ActivityController.html) 去了解更多对你测试可用的公开方法.

## 等下, `visible()` 是不是多余的?

原来在一个真正的 Android 应用, 在调用 `onCreate()` 之后的某个时刻, Activity 的视图层次结构不会添加到 Window. 直到发生这种情况, 否则Activity 的视图不会报告为可见. 这意味着你不能点击它们(其他意想不到的行为). 在 `onPostResume()` 调用之后, 设备或者模拟器上的 Activity 的层次结构才会添加到 Window. 而不是对何时更新可见性做出假设, Robolectric 把权力放在写测试文件的开发者手里.

所以, 什么时候调用它? 当你和在 Activity 里的视图交互时. 像 `Robolectric.clickOn()` 调用条件是视图是可见的并且正确调用函数. 你应该在调用 `create()` 之后调用 `visible()` 函数.  



