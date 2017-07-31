## 使用资源限定符

在 [Android 开发文档](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)的描述, 资源限定符允许你根据设备上的语言，屏幕大小以和是白天还是黑夜来更改资源的加载方式. 对于严格测试来说这些改变通常是乏味的(每个字符串对于支持的所有语言都有对应的翻译), 你可能希望在不同的资源限定符的上下文中运行测试.

### 在测试中指定 Resources

指定资源限定符是相当简单的: 只要在你测试用例或者测试类的 @Config 注解中添加你期望的限定符, 具体取决于你是否要更改整个文件的资源限定符，或者只是一个简单的测试.

给出以下资源, 

values/strings.xml

```xml
<string name="not_overridden">Not Overridden</string>
<string name="overridden">Unqualified value</string>
<string name="overridden_twice">Unqualified value</string>
```

values-en/strings.xml

```xml
<string name="overridden">English qualified value</string>
<string name="overridden_twice">English qualified value</string>
```

values-en-port/strings.xml

```xml
<string name="overridden_twice">English portrait qualified value</string>
```

Robolectric 测试会通过使用 Android 资源限定符来解析规则.

```java
@Test
@Config(qualifiers="en-port")
public void shouldUseEnglishAndPortraitResources() {
  final Context context = RuntimeEnvironment.application;
  assertThat(context.getString(R.id.not_overridden)).isEqualTo("Not Overridden");
  assertThat(context.getString(R.id.overridden)).isEqualTo("English qualified value");
  assertThat(context.getString(R.id.overridden_twice)).isEqualTo("English portrait qualified value");
}
```

多个限定词应该用破折号分开, 并按照[此列表](https://developer.android.com/guide/topics/resources/providing-resources.html#table2)中提供的顺序编写.