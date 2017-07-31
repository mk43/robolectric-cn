## 编写你的第一个测试

假设你有一个代表欢迎界面的活动布局.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/login"
        android:text="Login"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</LinearLayout>
```

我们想写一个测试, 断言当一个用户点击按钮, 这个应用将会跳转到 LoginActivity.

```xml
public class WelcomeActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.welcome_activity);

        final View button = findViewById(R.id.login);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                startActivity(new Intent(WelcomeActivity.this, LoginActivity.class));
            }
        });
    }
}
```

为了测试这个, 我们可以检查当用户点击 "Login" 按钮, 我们开启正确的 intent. 因为 Robolectric 是一个单元测试框架, 这个 LoginActivity 不会实际的开启, 但是我们可以检测这个 WelcomeActivity 是否发出正确的 intent.

```java
@RunWith(RobolectricTestRunner.class)
public class WelcomeActivityTest {

    @Test
    public void clickingLogin_shouldStartLoginActivity() {
        WelcomeActivity activity = Robolectric.setupActivity(WelcomeActivity.class);
        activity.findViewById(R.id.login).performClick();

        Intent expectedIntent = new Intent(activity, LoginActivity.class);
        assertThat(shadowOf(activity).getNextStartedActivity()).isEqualTo(expectedIntent);
    }
}
```
