---
title: 译-Building Local Unit Test
date: 2017-06-19 13:15:51
tags: 单元测试 Android
categories: 翻译
directory:
---

# 创建本地单元测试

## 配置单元测试环境
 在对应的 build.gradle　文件中添加依赖
```
dependencies {
   // Required -- JUnit 4 framework
   testCompile 'junit:junit:4.12'
   // Optional -- Mockito framework
   testCompile 'org.mockito:mockito-core:1.10.19'
}
```
## 创建一个单元测试类
  Android 中的单元测试是 Junit4 框架的，我们不需要让我们的类继承子 TestCase类，也不需要让方法名一 test 开头，也不会使用到 junit.framework 中的任何类；

  创建一个基于 junit4 的类只需要创建一个包含若干测试方法的 java 类就可以，测试方法使用 @Test 标注就可以，

  下面的例子展示了如何实现一个本地单元测试类，测试方法验证了程序中的 isValidEmail() 方法是否符合返回的结果；
```
import org.junit.Test;
import java.util.regex.Pattern;
import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;

public class EmailValidatorTest {

    @Test
    public void emailValidator_CorrectEmailSimple_ReturnsTrue() {
        assertThat(EmailValidator.isValidEmail("name@email.com"), is(true));
    }
    ...
}
```
 为了使测试代码更具可读性，我们可以使用 Hamcrest Matchers(匹配结果的类，is(),equalTo()方法)匹配返回结果与预期的结果

 # 模拟 Android 依赖

  默认情况下，gradle 插件会根据修改版本的 andorid.jar 执行本地单元测试，但是这个jar包中的方法不包含任何具体实现，在单元测试中调用android 的代码会抛出异常；这是为了保证在测试代码的时候不会被特定的android 平台干扰；

  我们可以使用一个 mock 框架来去掉特定的依赖项，来保证我们的单元测试和依赖项按找特定方式交互。通过 mock 对象来代替 Android 的依赖项； 在 mockito 的支持下，我们可以模拟对象让他在调用的时候返回任意值；

  给项目中添加Mock对象用于单元测试需要按照以下步骤来：

  1. 在 build.gradle 中添加依赖，像配置环境一节描述的一样

  2. 在定义的单元测试类之前添加 @RunWith(MockitoJunitRunner.class) 注解，这个注解会让 mockito test runner 验证 mockito 的用法是否正确以及简化模拟对象的初始化；

  3. 为了创建一个模拟对象，需要在变量声明之前添加 @mock 注解；

  4. 为了模拟依赖项的行为，我们可以使用 when(condition).thenReturn(value) 方法调用链去指定一个条件并在条件满足的时候
    返回想要的值；

  下面的例子展示了使用一个 mock 的 Context 对象：


```
import static org.hamcrest.MatcherAssert.assertThat;
import static org.hamcrest.CoreMatchers.*;
import static org.mockito.Mockito.*;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.Mock;
import org.mockito.runners.MockitoJUnitRunner;
import android.content.SharedPreferences;

@RunWith(MockitoJUnitRunner.class)
public class UnitTestSample {

    private static final String FAKE_STRING = "HELLO WORLD";

    @Mock
    Context mMockContext;

    @Test
    public void readStringFromContext_LocalizedString() {
        // Given a mocked Context injected into the object under test...
        when(mMockContext.getString(R.string.hello_word))
                .thenReturn(FAKE_STRING);
        ClassUnderTest myObjectUnderTest = new ClassUnderTest(mMockContext);

        // ...when the string is returned from the object under test...
        String result = myObjectUnderTest.getHelloWorldString();

        // ...then the result should be the expected one.
        assertThat(result, is(FAKE_STRING));
    }
}
```

## Error:"Method...not mocked"

  如果你运行的测试代码中调用 android sdk 中的 api,但是你英没有 mock 时,运行会报告异常说这个方法没有被mock,这是因为用于单元测试的 andoird.jar 方法都是空实现（这些API都是由具体的设备上Android 系统镜像提供的）；

  同样的，所有的方法都会抛出异常，这是为了保证我们的单元测试不会受到特定Android平台的干扰；

   如果抛出的异常干扰了测试，我们可以通过在工程级别的build.gradle 中添加如下片段，让所有的方法都返回默认值 null 或 0;

```
android {
  ...
  testOptions {
    unitTests.returnDefaultValues = true
  }
}
```

## 运行单元测试

  按照如下步骤来运行单元测试

  1. 确保已经使用gradle Sync Project 同步过工程;

  2. 按以下任意一种运行测试

      * 运行单个测试，打开工程试图，右击一个单元测试，然后点击 run 按钮

      * 运行一个类的所有单元测试，右击该单元测试类点击 run 按钮

      * 运行一个目录中的所有单元测试，右击该目录，然后点及 run ;


  Android gradle 的插件将会编译位于默认目录（src/test/java）下的本地单元测试代码，构建一个 测试 app,使用默认的测试运行类来执行，android stuidio 将会在 Run 窗口中展示结果；