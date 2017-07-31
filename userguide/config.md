## 配置 Robolectric

一些 Robolectric 的行为可以在运行时配置. 使用 `robolectric.properties` 文件进行包版本配置或者使用 `@Config` 注解配置类和方法.

#### `@Config` 注解

为一个单一测试类或者方法配置 Robolectric, 使用 `@Config` 注解. 这个注解可以被应用于类和方法; 方法上的值会覆盖类上的值.

基类注解可以被查找, 所以如果你发现你自己在大量测试中有相同的值, 你可以创建一个基类把 `@Config` 注解到那个类.

```java
@Config(sdk=JELLYBEAN_MR1,
  manifest="some/build/path/AndroidManifest.xml",
  shadows={ShadowFoo.class, ShadowBar.class})
public class SandwichTest {
}
```

#### `robolectric.properties` 文件

可以在一个适当的包内创建一个 `robolectric.properties` 文件为在一个包内或者一组包内的所有 Robolectric 测试做配置. 通常来说, 这个文件将会建立在工程树的 `src/test/resources` 目录下. Robolectric 将会按层级搜寻这个文件, 在更深层次的包内配置会重写更浅层次的包内配置. 当测试类或者方法有 `@Config` 注解, 这将会重写在所有属性文件中的 config.

下面是一个例子:

```xml
# src/test/resources/com/mycompany/app/robolectric.properties
sdk=18
manifest=some/build/path/AndroidManifest.xml
shadows=my.package.ShadowFoo,my.package.ShadowBar
```

版本提示: 在 Robolectric 3.1.3 之前的版本, 只有顶级的 `robolectric.properties` 文件能够被制定.

#### 全局配置

如果你希望为你的测试去改变任何的默认配置, 你必须继承 `RobolectricTestRunner` 和重写 `buildGlobalConfig()` 方法, 然后在 `@RunWith` 注解中指定你自定义的 test runner.

### 可配置的

接下来的例子向你展示如何处理常见配置任务. 为了清楚方便, 这里使用 `@Config` 注解, 但是这些值应该被配置在属性文件中的.

#### 配置 SDK 版本

默认 Robolectric 将会以你在 manifest 中制定的 `targetSdkVersion` 运行代码. 如果你想在不同的 SDK 下测试代码, 你可以制定 SDK 使用 `sdk`, `minSdk` 和 `maxSdk` 进行配置.

```java
@Config(sdk = { JELLY_BEAN, JELLY_BEAN_MR1 })
public class SandwichTest {

    public void getSandwich_shouldReturnHamSandwich() {
      // will run on JELLY_BEAN and JELLY_BEAN_MR1
    }

    @Config(sdk = KITKAT)
    public void onKitKat_getSandwich_shouldReturnChocolateWaferSandwich() {
      // will run on KITKAT
    }
    
    @Config(minSdk=LOLLIPOP)
    public void fromLollipopOn_getSandwich_shouldReturnTunaSandwich() {
      // will run on LOLLIPOP, M, etc.
    }
}
```

值得注意当 `sdk` 和 `minSdk`/`maxSdk` 可能不会在相同的注解或者文件中配置; 然而 `minSdk` 和 `maxSdk` 必须同时指定. 如果它们中的任何一个存在, 它们会从较不具体的配置位置覆盖所有 SDK 指定配置. 

版本提示: 在 Robolectric 3.2 之前的版本, `minSdk` 和 `maxSdk` 是忽略的, `NEWEST`, `OLDEST`, 和 `TARGET` 是不支持的. 只能在属性文件中指定对应于API级别的整数.

#### 配置应用类

Robolectric 将会尝试去根据 manifest 的指定为应用类创建一个实例. 如果你想提供一个自定义实现, 你可以通过 setting 指定它.

```java
@Config(application = CustomApplication.class)
public class SandwichTest {

    @Config(application = CustomApplicationOverride.class)
    public void getSandwich_shouldReturnHamSandwich() {
    }
}
``` 

#### 配置 Resource 和 Asset 路径

Robolectric 为 Gradle 和 Maven 提供了默认的配置, 但是也允许你自定义 manifest, resource 文件夹 和 assets 文件夹路径. 如果你有一个自定义构建系统, 这将会很有用. 你可以指定这些值通过 setting. 

```java
@Config(resourceDir = "some/build/path/res")
public class SandwichTest {

    @Config(resourceDir = "other/build/path/ham-sandwich/res")
    public void getSandwich_shouldReturnHamSandwich() {
    }
}
```

默认情况下, Robolectric 将假定你的 resources 和 assets 将各自位于 res 和 assets 的文件夹下. 这些路径假定都是相对于 manifest 路径的. 你可以通过添加 `resourceDir` 和 `assetDir` 选项到 `@Config` 去改变这些值.

#### 配置限定词(Qualifiers)

你可以显式地配置测试的资源限定符集: 

```java
public class SandwichTest {

    @Config(qualifiers = "fr-xlarge")
    public void getSandwichName() {
      assertThat(sandwich.getName()).isEqualTo("Grande Croque Monégasque");
    }
}
```

详情请看[使用限定词](http://robolectric.org/using-qualifiers/)

### 系统属性

通过设置系统属性来配置一些全局的附加选项:

- robolectric.enabledSdks - 逗号分割的 SDK 版本或名字(例: `19, 21` 或者 `KITKAT, LOLLIPOP`)是生效的. 测试只会在被指定列表里的 SDK 内运行. 默认所有 SDK 都是启用的.
- robolectric.offline - 设置为 true 禁用运行时获取 jar 包.
- robolectric.dependency.dir - 在离线模式时, 指定运行时依赖的所在目录.
- robolectric.dependency.repo.id - 设置 Maven 仓库 ID 为运行时提供依赖. (默认 `sonatype`)
- robolectric.dependency.repo.url - 设置 Maven 仓库 URL 为运行时提供依赖. (默认 `https://oss.sonatype.org/content/groups/public/`)
- robolectric.logging.enabled - 设置 ture 启用调试日志.

当使用 Gradle 时, 你可以用 `all` 块(看[这里](https://developer.android.com/training/testing/start/index.html))为单元测试配置系统属性. 例如, 重写 Maven 仓库的 URL 和 ID, 从 Sonatype 之外的仓库下载运行时依赖.

```java
android {
  testOptions {
    unitTests.all {
      systemProperty 'robolectric.dependency.repo.url', 'https://local-mirror/repo'
      systemProperty 'robolectric.dependency.repo.id', 'local'
    }
  }
}
```

