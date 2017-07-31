## 创建自定义shadows

自定义 Shadow 是 Robolectric 的一个特性, 它允许你对 Android 功能测试去做一些有目的性的改变. 这可能是简单的捕获一种方法, 去插入与测试对象交互的代码, 甚至完全不做任何事情.

自定义 Shadow 允许你只在部分测试代码中包含 shadow 功能, 而不是直接添加或修改 Shadow 源码. 它还允许你引用特定域的上下文, 像测试类的域对象.

### 写一个自定义 Shadow

自定义 Shadow 结构和普通的 shadow 类基本一样. 在类定义中必须包含 `@Implements(AndroidClassName.class)` 注解. 你可以使用通常的 Shadow 实现选项. 例如使用 `@Implementation` 实现实例方法, 或者使用 `public void __constructor__(...)` 实现构造函数.

```java
@Implements(Bitmap.class)
public class MyShadowBitmap {
  @RealObject private Bitmap realBitmap;
  private int bitmapQuality = -1;
    
  @Implementation
  public boolean compress(Bitmap.CompressFormat format, int quality, OutputStream stream) {
    bitmapQuality = quality;
    return realBitmap.compress(format, quality, stream);
  }

  public int getQuality() {
    return bitmapQuality;
  }
}
```

### 使用自定义 Shadow

自定义 Shadow 通过在测试类或者测试方法中使用 @Config 注解来链接到 Robolectric, 并使用 `shadows` 数组属性. 用前面提及的 MyShadowBitmap 类作为例子, 在测试中用 `@Config(shadows={MyShadowBitmap.class})` 注解, 用 `@Config(shadows={MyShadowBitmap.class, MyOtherCustomShadow.class})` 注解包含多个 Shadow. 这将会使 Robolectric 在执行代码时识别并使用你的自定义 Shadow. 

然而, `Shadows.shadowOf()` 在自定义 Shadow 中是无效的, 因为它必须在每个 Robolectric Shadow 类中被实现. 你可以用 `Shadow.extract()` 代替并且强制转换返回值为你实现的自定义 Shadow.

另外, 如果你 shadow 了一个已经在 Robolectric 中 shadow 的 Android 类, 你将替换 Robolectric 中的 Shadow. 如果你仍然需要基本 Shadow 类的基本功能, 你可以尝试去继承它. 
