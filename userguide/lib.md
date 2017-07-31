## 使用库资源

当 Robolectric 运行测试时, 它会尝试加载和索引应用程序提供的所有资源, 以便在调用 AssetManager 时返回这些资源. 对于由第三方库(像 aars 或 apklibs)提供的资源, 需要一些其它配置.

#### 用 Gradle 使用库

如果你使用 Gradle 构建你的应用并且使用 `RobolectricTestRunner` 去运行你的测试, 那么不需要其它额外配置. 这是因为 Android Gradle 插件会在构建时将第三方库和你的应用资源合并. 注意 `RobolectricGradleTestRunner` test runner 在 3.1 版本被[弃用](https://github.com/robolectric/robolectric/wiki/3.0-to-3.1-Upgrade-Guide)了. 

#### 用 Maven 使用库

如果你使用 Maven 构建那你的应用, 你需要告诉 Robolectric 每个库所使用的解压缩资源包是在哪里. 这也可以在 `@Config` 注解中指定: 

```java
@RunWith(RobolectricTestRunner.class)
@Config(libraries = {
    "build/unpacked-libraries/library1",
    "build/unpacked-libraries/library2"
})
public class SandwichTest {
}
```

或者在 `robolectric.properties` 文件中指定: 

```java
libraries=build/unpacked-libraries/library1,build/unpacked-libraries/library2
```

所有的路径都是相对于工程的根目录.

#### 调试资源加载问题

如果你不确定特定库的资源是否被加载, 可以通过设置系统属性 `robolectric.logging.enabled = true` 来启用调试日志并且运行你的测试. 你可以看到大量如下输出: 

```xml
Loading resources for 'com.foo' from build/unpacked-libraries/library1...
```

如果你在这个列表中没有看到特定的库, 仔细检查配置.
