# MultiStatePage
[![](https://jitpack.io/v/Zhao-Yan-Yan/MultiStatePage.svg)](https://jitpack.io/#Zhao-Yan-Yan/MultiStatePage)
[![License](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](https://github.com/Zhao-Yan-Yan/MultiStatePage/blob/master/LICENSE) 
## 下载Demo

点击或者扫描二维码下载

[![QR code](imgs/QRCode.png)](https://www.pgyer.com/1uvC)

| [Activity](app/src/main/java/com/zy/multistatepage/MultiStateActivity.kt) | [Fragment](app/src/main/java/com/zy/multistatepage/MultiFragmentActivity.kt) | [View](app/src/main/java/com/zy/multistatepage/MultiViewActivity.kt) | [ViewPager2](app/src/main/java/com/zy/multistatepage/ViewPager2Activity.kt) |
| :-----: | :----: | :----: | :----: |
| ![](imgs/activity.gif) | ![](imgs/fragment.gif) | ![](imgs/view.gif) | ![](imgs/viewpager2.gif) |

| [Lottie拓展（自定义State）](app/src/main/java/com/zy/multistatepage/LottieExtActivity.kt) | [State刷新](app/src/main/java/com/zy/multistatepage/RefreshStateActivity.kt) | [网络请求](app/src/main/java/com/zy/multistatepage/MockNetActivity.kt) | [mock](app/src/main/java/com/zy/multistatepage/ApiActivity.kt) |
| :-----: | :----: | :----: | :-----: |
| ![](imgs/lottie.gif) | ![](imgs/state_call.gif) | ![](imgs/net.gif) | ![](imgs/api.gif) |

## MultiStatePage的功能及特点
- 无需在布局添加视图代码
- 可显示自定义状态视图,任意拓展
- 可用于 Activity、Fragment、或指定的 View
- 自定义重新请求监听
- 可动态更新视图样式
- 可结合第三方控件使用
- 支持状态回调监听
- kotlin开发更易用的API

## 开始

### 添加依赖
Step1. Add the JitPack repository to your build file
```
allprojects {
    repositories {
        maven { url 'https://jitpack.io' }
    }
}
```

Step2. Add the dependency
[![](https://jitpack.io/v/Zhao-Yan-Yan/MultiStatePage.svg)](https://jitpack.io/#Zhao-Yan-Yan/MultiStatePage)
```
dependencies {
    implementation 'com.github.Zhao-Yan-Yan:MultiStatePage:1.0.7'
}
```
### 1.生成MultiStateContainer

#### 在View上使用
##### kotlin
基础用法
```kotlin
val multiStateContainer = MultiStatePage.bindMultiState(view)

val multiStateContainer = MultiStatePage.bindMultiState(view) { multiStateContainer: MultiStateContainer ->
    // 重试事件处理
}
```
拓展方法
```kotlin
val multiStateContainer = view.bindMultiState()

val multiStateContainer = view.bindMultiState() { multiStateContainer: MultiStateContainer ->
    // 重试事件处理
}
```

##### java
```java
MultiStateContainer multiStateContainer = MultiStatePage.bindMultiState(view)

MultiStateContainer multiStateContainer = MultiStatePage.bindMultiState(view, new OnRetryEventListener() {
    @Override
    public void onRetryEvent(MultiStateContainer multiStateContainer) {
        // 重试事件处理
    }
});
```
#### 在Activity 根View中使用
##### kotlin
基础用法
```kotlin
val multiStateContainer = MultiStatePage.bindMultiState(this)

val multiStateContainer = MultiStatePage.bindMultiState(this) { multiStateContainer: MultiStateContainer ->
    // 重试事件处理
}
```
拓展方法
```kotlin
val multiStateContainer = bindMultiState()

val multiStateContainer = bindMultiState() { multiStateContainer: MultiStateContainer ->
    // 重试事件处理
}
```
##### java
```java
MultiStateContainer multiStateContainer = MultiStatePage.bindMultiState(this);

MultiStateContainer multiStateContainer = MultiStatePage.bindMultiState(this, new OnRetryEventListener() {
    @Override
    public void onRetryEvent(MultiStateContainer multiStateContainer) {
        // 重试事件处理   
    }
});
```
#### 在Fragment根View中使用
##### kotlin
```kotlin
class MultiStateFragment : Fragment {
    
    private lateinit var multiStateContainer: MultiStateContainer
    
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        val root = inflater.inflate(R.layout.fragment, container, false)
        multiStateContainer = MultiStatePage.bindMultiState(root) { multiStateContainer: MultiStateContainer ->
            // 重试事件处理
        }
        //或者
        multiStateContainer = root.bindMultiState() { multiStateContainer: MultiStateContainer ->
            // 重试事件处理
        }
        return multiStateContainer
    }
}
```
##### java
```java
class JavaFragment extends Fragment {
    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View root = inflater.inflate(R.layout.activity_main, container, false);
        MultiStateContainer multiStateContainer = MultiStatePage.bindMultiState(root);
        return multiStateContainer;
    }
}
```

### 2.切换状态
调用  `MultiStateContainer.show<T>()` 方法

**默认内置3种状态**
```kotlin
val multiStateContainer = MultiStatePage.bindMultiState(view)
//成功页 
multiStateContainer.show<SuccessState>()
//错误页
multiStateContainer.show<ErrorState>()
//空页面
multiStateContainer.show<EmptyState>()
//加载状态页
multiStateContainer.show<LoadingState>()
```

#### 动态设置state

```kotlin
multiStateContainer.show<ErrorState>{errorState ->
    errorState.setErrorMsg("xxx出错了")
}
```

#### 设置重试回调

```kotlin
val multiStateContainer = MultiStatePage.bindMultiState(view){
    Toast.makeText(context, "retry", Toast.LENGTH_SHORT).show()
}
```

### 自定义State
#### 1.继承`MultiState`
```kotlin
class LottieWaitingState : MultiState() {
    override fun onCreateMultiStateView(
        context: Context,
        inflater: LayoutInflater,
        container: MultiStateContainer
    ): View {
        return inflater.inflate(R.layout.multi_lottie_waiting, container, false)
    }

    override fun onMultiStateViewCreate(view: View) {
        //逻辑处理
    }
    
    //是否允许重试
    override fun enableReload(): Boolean = false
    
    //指定重试 view
    override fun bindRetryView(): View? {
        return retryView
    }
}
```
`enableReload()` 是否允许`retry`回调 `false`不允许

`bindRetryView` 绑定重试点击事件的`view` 默认为根`view`

结合`ViewBidng` 参考 `demo` [MultiStateBinding](app/src/main/java/com/zy/multistatepage/base/MultiStateBinding.kt) 和 [WithBindingState](app/src/main/java/com/zy/multistatepage/state/WithBindingState.kt)

#### 2.show (1.0.3后无需register)

```kotlin
class LottieExtActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val multiStateContainer = bindMultiState()
        multiStateContainer.show<LottieWaitingState>()
    }
}
```

### 使用内置状态配置

```kotlin
class App : Application() {
    override fun onCreate() {
        super.onCreate()
        val config = MultiStateConfig.Builder()
            .alphaDuration(300)
            .errorIcon(R.mipmap.state_error)
            .emptyIcon(R.mipmap.state_empty)
            .emptyMsg("emptyMsg")
            .loadingMsg("loadingMsg")
            .errorMsg("errorMsg")
            .build()
        MultiStatePage.config(config)
    }
}
```
| method | 作用 |
| :-----: | :----: |
| `alphaDuration` | alpha动画时长 |
| `errorIcon` | 错误状态默认图标 |
| `emptyIcon` | 空数据状态默认图标 |
| `emptyMsg` | 空数据状态默认提示信息 |
| `errorMsg` | 错误状态默认提示信息 |
| `loadingMsg` | loading状态默认提示信息 |

### 小技巧
可以借助kotlin的拓展函数封装常用的状态
```kotlin
fun MultiStateContainer.showSuccess(callBack: (SuccessState) -> Unit = {}) {
    show<SuccessState> {
        callBack.invoke(it)
    }
}

fun MultiStateContainer.showError(callBack: (ErrorState) -> Unit = {}) {
    show<ErrorState> {
        callBack.invoke(it)
    }
}

fun MultiStateContainer.showEmpty(callBack: (EmptyState) -> Unit = {}) {
    show<EmptyState> {
        callBack.invoke(it)
    }
}

fun MultiStateContainer.showLoading(callBack: (LoadingState) -> Unit = {}) {
    show<LoadingState> {
        callBack.invoke(it)
    }
}
```

调用
```kotlin
val multiStateContainer = bindMultiState()
multiStateContainer.showLoading()
multiStateContainer.showSuccess()
```
## 更新日志
- 1.0.7(2020/11/26) 小优化 移除部分log打印
- 1.0.6(2020/11/06) 优化state切换策略 保留原view
- 1.0.5(2020/11/04) `kotlin`函数类型参数更换为`java interface`对`java`的调用更友好
- 1.0.4(2020/11/04) api重命名 `Activity`和`View`统一为`bindMultiState()`
- 1.0.3(2020/10/26) 修复state内存泄漏, 移除register函数
- 1.0.2(2020/10/23) 支持指定重试view, 支持ViewBinding 
- 1.0.1(2020/09/22) 支持内置状态页信息配置, 支持alpha动画时长配置

## Thanks
- [DylanCaiCoding/LoadingHelper](https://github.com/DylanCaiCoding/LoadingHelper/) 
- [KingJA/LoadSir](https://github.com/KingJA/LoadSir)
- [airbnb/lottie-android](https://github.com/airbnb/lottie-android)
- [lottie动画资源社区](https://lottiefiles.com/featured)
- [玩Android](https://www.wanandroid.com/)

**如果对你有帮助的话可以点个star支持一下子 谢谢亲**

**本项目会长期维护 欢迎issue指正**
## License
```
Copyright (C) 2020. ZhaoYan

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
