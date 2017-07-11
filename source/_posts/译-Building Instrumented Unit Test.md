---
title: 译-Testing UI for a Single App
date: 2017-06-19 13:15:51
tags: 单元测试 Android
directory:
---

 # 配置 Espresso

  添加依赖

```
  dependencies {
    // Other dependencies ...
    androidTestCompile 'com.android.support.test.espresso:espresso-core:2.2.2'
}
```

 关闭系统动画，否则可能会导致非预期的结果；在开发者选项中关闭：

 * 窗口动画缩放

 * 过渡动画缩放

 * 动画程序时长缩放

# 创建 Espresso 测试类

 按照如下步骤创建

 1. 使用Espresso.onView()在Activity中找到想要测试的UI控件，使用onData() 在AdapterView()中找；

 2. 通过调用 ViewInteraction.perform(action) 或者 DataInteraction.perform(aciton) 并将用户操作
 作为参数传递,执行模拟用户操作；可以对同意个空间同时执行多个操作，将操作用逗号分隔即可

 3. 重复上面的操作，在目标app的多个界面模拟用户操作流；

 4. 在执行用户操作后，使用 ViewAssertions 中的方法检查 UI 是否反应了预期的状态或行为。

 下面的代码展示了在一段测试代码中的通用模板：

 ```
 onView(ViewMatcher)
   .perform(ViewAction)
   .check(ViewAssertion);

 onView(withId(R.id.my_view))            // withId(R.id.my_view) is a ViewMatcher
        .perform(click())               // click() is a ViewAction
        .check(matches(isDisplayed())); // matches(isDisplayed()) is a ViewAssertion        
 ```

 # 使用 ActivityTestRule

  下面的代码段描述了如何创建一个基于Junit4风格的 UI 测试，并使用 `ActivityTestRule` 去减少
   模板代码的编写； 通过使用 `ActivityTestRule` ，测试框架将会在由 @Test 注解的任何测试方法和
   由 @Before 注解的任何方法前启动对应的 `Activity`,在执行完所有的 测试方法和有 @After 注解的
  方法后，将会关闭它；

```
package com.example.android.testing.espresso.BasicSample;

import org.junit.Before;
import org.junit.Rule;
import org.junit.Test;
import org.junit.runner.RunWith;

import android.support.test.rule.ActivityTestRule;
import android.support.test.runner.AndroidJUnit4;
...

@RunWith(AndroidJUnit4.class)
@LargeTest
public class ChangeTextBehaviorTest {

    private String mStringToBetyped;

    @Rule
    public ActivityTestRule<MainActivity> mActivityRule = new ActivityTestRule<>(
            MainActivity.class);

    @Before
    public void initValidString() {
        // Specify a valid string.
        mStringToBetyped = "Espresso";
    }

    @Test
    public void changeText_sameActivity() {
        // Type text and then press the button.
        onView(withId(R.id.editTextUserInput))
                .perform(typeText(mStringToBetyped), closeSoftKeyboard());
        onView(withId(R.id.changeTextBt)).perform(click());

        // Check that the text was changed.
        onView(withId(R.id.textToBeChanged))
                .check(matches(withText(mStringToBetyped)));
    }
}

```  

![](https://google.github.io/android-testing-support-library/assets/espresso-cheat-sheet-2.1.0.png)
