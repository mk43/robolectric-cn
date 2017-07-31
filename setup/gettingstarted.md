## 开始

Robolectric 用 Gradle 或者 Maven 工作最好. 如果你是开始一个新的项目, 我们建议你首选 Gradle (因为它是Android Studio中选择的构建系统) 其次是 Maven. 不管怎样, 无论你选择什么构建工具, Robolectric 兼容性都会很好. 

#### 用 Gradle 构建

##### Android Studio 3.0+

从 Robolectric 3.3 开始, 现在与工具链有更紧密的集成, 构建系统处理和合并您的资源。您将需要 Android Studio 3.0 alpha 5 的版本号. 

添加如下代码到 build.gradle:

```java
testCompile "org.robolectric:robolectric:3.4"

android {
  testOptions {
    unitTests {
      includeAndroidResources = true
    }
  }
}  
```

为你的 Robolectric 测试添加注解:

```java
@RunWith(RobolectricTestRunner.class)
public class SandwichTest {
}
```

##### 早期版本

在早期 Robolectric 的图形加速接口你必须具体化指向这个被构建生成的 `BuildConfig.class` 的 `constants` 域. 当用 Gradle 构建你的工程时, Robolectric 使用这个类的常量去计算这个输出路径. 没有这些值, Robolectric 将无法找到你的 manifest, resources, or assets. 这在以后我们将不会支持, 所以我们强烈建议你使用我们的新 API.

```java
testCompile "org.robolectric:robolectric:3.4"
```

使用 Robolectric test runner 注解和配置 Robolectric 去找到你的资源.

```java
@RunWith(RobolectricTestRunner.class)
@Config(constants = BuildConfig.class)
public class SandwichTest {
}
```

#### 用 Maven 构建

添加下面代码到 pom.xml:

```java
<dependency>
   <groupId>org.robolectric</groupId>
   <artifactId>robolectric</artifactId>
   <version>3.4</version>
   <scope>test</scope>
</dependency>
```

使用基本的 test runner 注解:

```java
@RunWith(RobolectricTestRunner.class)
public class SandwichTest {
}
```

如果你的引用资源是在项目的外面(例如 arr 依赖), 你将需要为 Robolectric 提供一指向你的构建系统中的扩展 arr 的指针. 详情请见 "使用库资源".

#### 用 Android Studio 构建

Robolectric 能工作在 Android Studio 1.1.0 及以上的版本. 使用前面 Gradle 的构建步骤. 对于 Android Studio 2.0.0 以上的版本, 在 "Build Variants" 选项中有了单元测试的支持可以来运行你的单元测试. 这个特性在 Android Studio 2.0.0 默认是启用的, 你可以设置启用或者关闭通过以下方式: File Menu -> Settings -> Build, Execution, Deployment -> Build Tools -> Gradle -> Experimental.

![](http://robolectric.org/images/android-studio-enable-unit-tests.png)

#### 对 Linux 和 Mac 用户的提示

如果你正在使用 Linux 和 Mac. 为了解决一个 Bug, Android Studio没有将工作目录设置为正在测试的模块, 你可能需要配置默认的 JUnit test runner 配置. 这可以通过编辑这个运行配置, `Defaults -> JUnit` 和改变工作路径的值为 `$MODULE_DIR$`.

![](http://robolectric.org/images/android-studio-configure-defaults.png)

#### 用 Eclipse 构建

安装 [m2e-android](http://rgladwell.github.io/m2e-android/) 的 Eclipse 插件, 将项目导入为 Maven 项目. 导入 Eclipse 之后, 你必须将 consume-aar 目标标记为忽略, 因为 因为 m2e-android 尚不支持 AAR. 快速修复 "Plugin execution not covered by lifecycle configuration" 错误. 运行测试只需要右键 project -> Run as -> JUnit Test 然后选择  Eclipse JUnit Launcher. 

#### 项目实例

[Robolectric samples](https://github.com/robolectric/robolectric-samples) 这个项目告诉你 Android 应用的测试驱动开发有多快和多简单. 另外, 看看用 [Gradle](https://github.com/robolectric/deckard) 或者 [Maven](https://github.com/robolectric/deckard-maven) 构建的项目.

