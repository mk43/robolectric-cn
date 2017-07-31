## 最佳实践

DON’T mock or spy on Android classes that will be acted on by other Android code (e.g. Context, SharedPreferences, and many others). Stubbing is very brittle and can lead to breakages on Robolectric or Android Platform upgrades. The small exceptions to this rule are classes with very narrow responsibilities, such as event listeners.

DO test layout inflation in your Robolectric test and ensure that click listeners are set up correctly by testing the Activity and Layout interaction directly rather than mocking the LayoutInflater or providing an abstraction over the view.

在 Robolectric 测试中做布局引入测试时, 确保点击监听被正确的设置. 通过测试 `Acticity` 和 `Layout` 直接交互而不是模拟 `LayoutInflater` 或者提供一个抽象视图.

DO use public lifecycle APIs (e.g: via Robolectric.buildActivity()) rather than exposing @VisibleForTesting methods when testing Android components such as Activities and Services. Calling those methods directly makes it difficult to refactor the code under test later.

当测试像 `Activities` 和 `Services` 这样的 Android 组件时, 使用公开的生命周期 API (像: `Robolectric.buildActivity()`) 而不是暴露 `@VisibleForTesting` 方法. 直接调用这些代码 会使得测试之后的重构变得麻烦.

DO limit the number of the threads that are running during each test. Rogue threads often cause test pollution because they are not automatically cleaned up between tests. Oftentimes threads are inadvertently spawned when using third-party libraries (e.g for networking) or background processing components. One of the main sources of additional threads during tests are ExecutorServices that maintain thread pools. If possible, mock dependent components that spawn threads, or use a DirectExecutor. If it’s necessary to run multiple threads during a test, make sure to explicitly stop all threads and ExecutorServices to avoid test pollution.

在运行测试时限制线程的数量. 流氓线程通常会导致测试污染, 因为它们不会在测试之间自动清除. 当使用第三方库(像: 网络)或后台处理组件时, 通常会开启新线程. 测试期间的其它线程的主要来源之一是 `ExecutorService` 线程池维护. 如果可能, 模拟产生新线程的相关组件或使用 `DirectExecutor`. 如果在测试下要跑多线程, 确保明确地停止所有线程和 `ExecutorService` 以避免测试污染. 

注意: 由于水平有限, 这里翻译不是很好, 贴出原文以供参考.
