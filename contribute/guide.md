## 贡献指南

### 开始

Fork 和 Clone 仓库:

```cmd
git clone git@github.com:username/robolectric.git
```

创建一个分支:

```cmd
git co -b my-feature-name
```

安装所有的请求依赖:

```cmd
./scripts/install-dependencies.sh
```

执行所有 Shadow 的完整构建:

```cmd
./gradlew install
```

### 贡献要求

#### 写测试文件

Robolectric 是一个单元测试框架, 并且 Robolectric 本身测试也是重要的. 所有的类都应该有一个单元测试类. 所有的公开方法都应该进行单元测试. 这些类和方法都应该有良好的测试性. 没有测试的提交请求会被退回.

#### 代码风格

必须是 IntelliJ 默认的 Java 风格, 但是使用两个空格缩进.

1. 使用空格而不是 Tab 缩进.
2. 缩进使用两个空格.
3. 所有的都使用花括号: if, else 等.
4. 方法之间使用一个空行隔开.
5. 不要在实例或者静态变量中使用 `'m'` 或者 `'s'` 前缀.

#### 文档

Robolectric 使用 [Markdown Doclet](https://github.com/Abnaxos/markdown-doclet), 所以写 Java 文档时使用 [markdown](https://daringfireball.net/projects/markdown/), 并且使用 javadoc 标记扩展.

对于支持 [Robolectric Chrome](https://chrome.google.com/webstore/detail/robolectric/pjepcinimnfnaoopahdkpkefnefdkdgh) 扩展的 Shadow 类, javadoc有特殊的规则.

在 [developer.android.com](https://developer.android.com/reference/packages.html) 上, 在 Shadow 类的类和方法级别的 javadoc 应该被插入在相应的  Shadow 类中, 并且标识为 Robolectric 相关的测试提示. javadoc 要显示, 在 Shadow 类的 `非-@Implementation` 方法被显示在一个新页面的 'Testing APIs' 区.

所有的 `@Implementation` 方法行为与标准 Android 行为有差异的必须使用 javadoc 描述其不同. 使用 `@see` 或者 `{@link}` 去指示方法的行为是否可以通过调用测试 API 方法进行更改或检查.

所有的 `非-@Implementation` 方法应该有其对应的 javadoc 描述.
