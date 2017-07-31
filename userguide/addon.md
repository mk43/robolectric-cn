## 使用附加包

为了在应用测试时减少外部依赖, Robolectric 的 shadow 被分成各种附加包. 只有基本 Android SDK 中提供的类的 shadow 才能由主 Robolectric 模块提供. 另外像 appcompat 或者 support library 的 shadow 都是以附加模块提供. 这些可用的附加 shadow 包在下表列出:


 SDK Package | Robolectric Add-On Package |    
:------------|:--------------------------
com.android.support.support-v4 | org.robolectric:shadows-support-v4
com.android.support.multidex | org.robolectric:shadows-multidex
com.google.android.gms:play-services | org.robolectric:shadows-play-services
com.google.android.maps:maps | org.robolectric:shadows-maps
org.apache.httpcomponents:httpclient | org.robolectric:shadows-httpclient

请注意, 除了 Robolectric 依赖之外, 还需要在 `build.gradle` 或 `pom.xml` 中指定附加包. 