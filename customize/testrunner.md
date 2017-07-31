## 自定义Test Runner

这里有几种你想自定义 Robolectric 的 test runner 的情况: 在所有测试之前完成一些操作, 甚至是在每一个测试方法运行之前. 一个好的例子是初始化一个依赖注入框架, 并使用一组不同的依赖集进行测试. 幸运的是, Robolectric 有一种和测试生命周期挂钩的方式. 如果你在 AndroidManifest.xml 中定义一个 Application 类, Robolectric 首先会自动尝试和加载你的应用测试版本. 例如:

假如你已经定义了一个 FooApplication 类在你的 manifest 中:

```xml
<application android:name=".FooApplication">
```

如果你正在使用 RoboGuice, 你可能会在你的 Application 类中初始化注入:

```java
ublic class FooApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        ApplicationModule module = new ApplicationModule();
        setBaseApplicationInjector(this, DEFAULT_STAGE, newDefaultRoboModule(this), module);
    }
}
```

你可以为你名为 TestFooApplication 的应用定义一个测试版本:

```java
public class TestFooApplication extends FooApplication implements TestLifecycleApplication {
    @Override
    public void onCreate() {
        super.onCreate();

        TestApplicationModule module = new TestApplicationModule();
        setBaseApplicationInjector(this, DEFAULT_STAGE, newDefaultRoboModule(this), module);
    }

    @Override
    public void beforeTest(Method method) {
    }

    @Override
    public void prepareTest(Object test) {
        getInjector(this).injectMembers(test);
    }

    @Override
    public void afterTest(Method method) {
    }
}
```

Robolectric 将会加载应用的测试版本, 在测试期间, 你可以加载不同的绑定集.