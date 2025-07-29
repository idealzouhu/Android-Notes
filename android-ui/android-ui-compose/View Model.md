### ViewModel

`ViewModel` 组件用于存储和公开界面所使用的状态。界面状态是经过 `ViewModel` 转换的应用数据。`ViewModel` 可让您的应用遵循通过模型驱动界面的架构原则。

**`ViewModel` 会存储应用相关的数据**，这些数据不会在 Android 框架销毁并重新创建 activity 时销毁。与 activity 实例不同，`ViewModel` 对象不会被销毁。应用会在配置更改期间自动保留 `ViewModel` 对象，以便它们存储的数据在重组后立即可用。



### ViewModel 和 rememberSaveable 的区别

发生配置更改时，您可以通过不同的方式保存应用数据，例如使用 `rememberSaveable` 或保存实例状态。不过，这些方式可能会造成问题。大多数情况下，您可以使用 `rememberSaveable`，但这可能意味着将逻辑保留在可组合函数中或附近。

随着应用体量不断变大，您应将数据和逻辑从可组合函数中移出。 [`ViewModel`](https://developer.android.google.cn/topic/libraries/architecture/viewmodel?hl=zh-cn) 是 Android Jetpack 库中的架构组件之一，可用于存储应用数据。当框架在配置更改或其他事件期间销毁并重新创建 activity 时，存储的数据不会丢失。不过，如果 activity 因进程终止而被销毁，数据将会丢失。`ViewModel` 只能通过快速重新创建 activity 缓存数据。



[Compose 中的 ViewModel 和状态](https://developer.android.google.cn/codelabs/basic-android-kotlin-compose-viewmodel-and-state?hl=zh-cn&continue=https%3A%2F%2Fdeveloper.android.google.cn%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-4-pathway-1%3Fhl%3Dzh-cn%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-viewmodel-and-state#8)







## StateFlow

`StateFlow` 和 `MutableStateFlow` 是用于管理**可观察状态**的两种流类型

- [`StateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/) 是一个数据容器式可观察数据流，可发出当前状态更新和新状态更新。其 [`value`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/value.html) 属性反映了当前状态值。

- 如需更新状态并将其发送到数据流，请为 [`MutableStateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-mutable-state-flow/index.html) 类的 value 属性分配一个新值。

| **特性**     | `StateFlow`                    | `MutableStateFlow`                      |
| :----------- | :----------------------------- | :-------------------------------------- |
| **可变性**   | 只读（不可直接修改值）         | 可变（可直接修改 `value` 属性）         |
| **用途**     | 对外暴露状态（供外部观察）     | 内部管理状态（供内部修改）              |
| **线程安全** | 是（基于 `ConcurrentHashMap`） | 是                                      |
| **是否热流** | 是（无论是否有订阅者都会更新） | 是                                      |
| **转换方法** | 无                             | 可通过 `asStateFlow()` 转为 `StateFlow` |



`collectAsState()` 函数会从此 [`StateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html) 收集值，并通过 [`State`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/State?hl=zh-cn) 表示其最新值。[`StateFlow.value`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/value.html) 用作初始值。每次向 [`StateFlow`](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-state-flow/index.html) 发布一个新值时，返回的 [`State`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/State?hl=zh-cn) 都会更新，这会导致所有 [`State.value`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/State?hl=zh-cn#value()) 用法重组。

> 在使用 state 和 属性委托 的时候，记得导入 import androidx.compose.runtime.getValue









```
viewModelFactory
```



```
    val homeUiState: StateFlow<HomeUiState> =
        itemsRepository.getAllItemsStream()
            // 将获取到的数据流映射为HomeUiState对象，以适应UI展示需求
            .map { HomeUiState(it) }
            // 使用stateIn扩展函数将映射后的流转换为StateFlow
            .stateIn(
                // 指定作用域为viewModelScope，即与ViewModel的生命周期一致
                scope = viewModelScope,
                // 配置流的共享策略为WhileSubscribed，当没有订阅者时暂停流的活动，以优化资源使用
                // 并指定超时时间为TIMEOUT_MILLIS，超过这个时间没有订阅者将自动停止流的活动
                started = SharingStarted.WhileSubscribed(TIMEOUT_MILLIS),
                // 设置初始值为一个空的HomeUiState对象，确保在数据流开始之前UI有默认状态展示
                initialValue = HomeUiState()
            )
```







必须手动添加  `androidx.compose.runtime.getValue`

```
import androidx.compose.runtime.collectAsState
import androidx.compose.runtime.getValue

val homeUiState by viewModel.homeUiState.collectAsState()
```





rememberCoroutineScope 和 viewModelScope 有什么区别？





角色
ReplyViewModel
ReplyUiState
类型
ViewModel
数据类
可变性
✅ 可变（通过 MutableStateFlow）
❌ 不可变
职责
管理逻辑和状态更新
保存 UI 展示所需的状态快照
更新方式
使用 update { it.copy(...) }
通过 copy() 创建新实例