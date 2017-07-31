## 扩展Robolectric

Robolectric 这个项目正在进行, 我们欢迎大家做出的贡献. 我们鼓励开发人员使用标准的[GitHub工作流程](https://help.github.com/articles/fork-a-repo/)去 fork, enhance 和 commit 请求给我们.

### Shadow 类

Robolectric 定义了许多修改和扩展 Android 操作系统行为的 Shadow 类. 当一个 Android 类被实例化, Robolectric 会找一类相应的 Shadow 类, 如果找到了, 就会创建一个 Shadow 对象与之关联.  每次一个 Android 类方法被调用, Robolectric 会保证 Shadow 类的相应方法首先被调用(如果存在的话), 所以它有机会去展现它的魔力. 这应用于所有的方法, 甚至是 static 和 final 修饰的方法, 因为 Robolectric 是非常谨慎思考的.

#### 名字有什么特殊意义?

为什么叫 "Shadow"? Shadow 对象不完全是 [Proxies](http://en.wikipedia.org/wiki/Proxy_pattern), [Fakes](http://c2.com/cgi/wiki?FakeObject), [Mocks 或者 Stubs](http://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs). Shadow 有时候是隐藏的, 有时候是可见的, 并且能够引导你到一个真正的对象. 至少我们没有叫它 "sheep", 不过我们以前确实这么想过.

#### 添加功能

如果 Robolectric 提供的 Shadow 类不能完成你的预期工作, 你可以去改变它们的行为以进行单次测试, 一组测试或整套套件. 简单的申明一个类(就叫 `ShadowFoo` 吧)然后用`@Implements(Foo.class)`注解它. 只要你想, 你的 Shadow 类就可以继承自一个常用的 Robolectric Shadow. 为了然你的 Robolectric 知道你的自定义 Shadow, 你需要用 `@Config(shadows=ShadowFoo.class)` 注解你的测试方法或类, 或者创建一个包含 `shadows=my.package.ShadowFoo` 这行代码的 `org.robolectric.Config.properties` 文件.

从 Robolectric 2.0 开始, Shadow 类的数量在大量减少, 因为真正的 Android 操作系统源码已经存在了. 只要你想, 你的 Shadow 类方法可以通过 `Robolectric.directlyOn()` 调用 Android 操作系统源码.

#### Shadow 类

Shadow 类总是需要一个公开的无参构造函数以至于 Robolectric 框架可以实例化它. 通过类申明 `@Implements` 注解来联系类与 Shadow. 一般来说, 它们应该从头开始实现, Shadow 类的灵活性(? facilities)几乎总是被移除, 并且它们的数据成员难以访问. Shadow 类中的方法要么是原始类的方法, 要么是通过设置返回值或提供对内部状态的访问或记录方法调用来方便测试. 

Shadow 类应该模仿生产类的继承层次结构. 例如, 如果你要为 `ViewGroup` 实现一个 `ShadowViewGroup` Shadow, 那么你的 Shadow 类就应该继承 `ViewGroup` 的超类 Shadow 也就是 `ShadowView`. (PS: 这里大概意思就是 ViewGroup 继承自 View, 为了保证继承层次一致, 所以 ShadowViewGroup 要继承自 ShadowView)

```java
...
@Implements(ViewGroup.class)
public class ShadowViewGroup extends ShadowView {
...
```

#### 方法

Shadow 对象的实现和 Android 类有相同的签名. 当调用 Android 对象方法时, Robolectric 将调用 Shadow 对象上具有相同签名的方法.

假设应用程序定义了以下代码:

```java
...
this.imageView.setImageResource(R.drawable.pivotallabs_logo);
...
```

在测试下 Shadow 实例的 `ShadowImageView#setImageResource(int resId)` 方法将会被调用.

Shadow 方法必须使用 `@Implementation` 注解标记. Robolectric 包含了一个 lint 检测去确认是否正确. 

```java
@Implements(ImageView.class)
public class ShadowImageView extends ShadowView {
  ...	
  @Implementation
  public void setImageResource(int resId) {
    // implementation here.
  }
}
```

在相应的 Shadow 类中定义的原始方法应该被 Shadow 类实现. 否则, Robolectric 的查找机制将无法找到它们(即使它们已经在 Shadow 子类中被声明). 例如, `setEnabled()` 方法在 View 中被定义. 如果一个 `setEnabled()` 方法在 `ShadowViewGroup` 中定义而不是在 `ShadowView` 中, 那么即使 `setEnabled()` 是被 `ViewGroup` 实例调用在运行时也不会被找到. 

#### Shadow 构造函数

一旦 Shadow 对象被实例化, Robolectric 将会查找一个名为 `__constructor__ ` 的方法, 它与在真实对象上调用的构造函数具有相同的参数. 

例如, 如果应用程序代码调用接收 Context 的 TextView 构造函数: 

```java
new TextView(context);
```

Robolectric 将会调用这个接下来的这个接受 Context 的 `__constructor__` 方法:

```java
@Implements(TextView.class)
public class ShadowTextView {
  ...
  public void __constructor__(Context context) {
    this.context = context;
  }
  ...
```

#### 访问真实的实例

有时候 Shadow 也会想应用一个正在 Shadowing 的对象, 例如去操控域. 一个 Shadow 类可以通过使用 `@RealObject` 注解来申明一个域来完成:

```java
@Implements(Point.class)
public class ShadowPoint {
  @RealObject private Point realPoint;
  ...
  public void __constructor__(int x, int y) {
    realPoint.x = x;
    realPoint.y = y;
  }
}
```

在调用其它方法之前, Robolectric 将会设置 realPoint 到真实的 `Point` 实例.

值得注意的是, 在一个真实对象的方法调用仍然可以被 Robolectric 拦截和重定向. 这在测试代码中并不常见, 但它对 Shadow 类的实现者有重要的影响. 由于 Shadow 类的继承层次机构并不总是和它的镜像 Android 类保持一致, 有时候需要通过这些真实对象调用, 以至于 Robolectric 运行时有机会基于对象的实际类去路由它们到正确的 Shadow 类. 否则, 在基类的 Shadow 上的方法将无法访问其子类的 Shadow 上的方法. 
 