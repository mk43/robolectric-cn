

在 Android 模拟器和设备上运行测试是很慢的!构建,部署和启动应用通常会花费一分钟甚至更多.这不是做测试驱动开发.肯定有更好的方法.

[Robolectric](http://robolectric.org/) 是一个单元测试工具, 去除 Android SDK Jar 包. 所以你能够(test-drive)你的 Android 应用程序的开发. 测试程序在你工作站的 JVM 内运行只需要几秒. 你可以用 Robolectric 这样写测试文件:


```java
@RunWith(RobolectricTestRunner.class)
public class MyActivityTest {

  @Test
  public void clickingButton_shouldChangeResultsViewText() throws Exception {
    MyActivity activity = Robolectric.setupActivity(MyActivity.class);

    Button button = (Button) activity.findViewById(R.id.button);
    TextView results = (TextView) activity.findViewById(R.id.results);

    button.performClick();
    assertThat(results.getText().toString()).isEqualTo("Robolectric Rocks!");
  }
}
```

Robolectric 通过重写 Android SDK 类重新加载, 使他们在一个普通 JVM 上运行成为可能.

#### SDK, Resources, & Native Method Emulation

Robolectric 用 C 语言实现了在 Android 设备上引入布局, 资源加载和大量其他的功能. 这使得测试能够做很多你可以在真机上做的事. 去提供我们自己实现的具体 SDK 也是很简单的, 所以你可以模拟错误的条件或者真实世界感应行为, 例如.

#### 在模拟器之外运行测试

Robolectric 让你的测试不需要模拟器而直接运行在你的工作站或者你集成环境中的普通 JVM 上.正是由于这样, 编译成.dex(Dalvik运行dex文件，而JVM运行java字节码), 打包和在模拟器上安装应用都是没有必要的, 一分一秒的减少测试周期, 所以你能自信的快速迭代和重构你的代码.

#### 不需要 Mock 框架

Robolectric 另一种方法是用类似 [Mockito](com/archive/p/mockito/) 这样的 Mock 框架或者去模拟 Android SDK. 虽然这是一个有效的方法, 但是它经常产生和本质上相反的应用程序代码实现.

Robolectric 允许一个接近黑盒测试的测试风格, 使得测试对于重构来说更有效和允许测试去关注应用的行为而不是 Android 自身的实现. 如果你想的话你依然可以在 Robolectric 中使用 Mock 框架.

