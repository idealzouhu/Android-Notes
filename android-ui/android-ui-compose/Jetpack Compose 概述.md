### 什么是 Jetpack Compose 

Jetpack Compose 是用于构建 Android 界面的新款工具包。Compose 使用更少的代码、强大的工具和直观的 Kotlin 功能，可以帮助您简化并加快 Android 界面开发。借助 Compose，您可以通过定义一组函数来构建界面，这些函数称为可组合函数，它们会接受数据并描述界面元素。



- Compose 是一个声明性界面框架，这意味着您可以在代码中声明界面的外观。



### 可组合函数

在 Compose 中，可组合函数是界面的基本构建块。可组合函数：

- 描述界面中的某一部分。
- 不会返回任何内容。
- 接受一些输入并生成屏幕上显示的内容。

可组合函数带有 [`@Composable`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/Composable?hl=zh-cn) 注解。所有可组合函数都必须带有此注解。此注解可告知 Compose 编译器：此函数用于将数据转换为界面。



### 可组合函数示例

以下代码段是一个简单的可组合函数示例，该函数接受传递的数据（`name` 函数参数）并用其在屏幕上渲染文本元素。

```
@Composable
fun Greeting(name: String) {
    Text(text = "Hello $name!")
}
```

关于可组合函数的几点说明：

- Jetpack Compose 是围绕可组合函数构建的。这些函数可让您以程序化方式定义应用的界面，只需描述应用界面的外观，而不必关注界面的构建过程。如需创建可组合函数，只需将 `@Composable` 注解添加到函数名称中即可。
- 可组合函数可以接受一些实参，用来让应用逻辑描述或修改界面。在本例中，界面元素接受一个 `String`，以便在问候用户时称呼姓名。



### 可组合函数名称

不返回任何内容且带有 `@Composable` 注解的 Compose 函数必须使用 Pascal 命名法命名。Pascal 命名法是指一种命名惯例，采用这种命名法时，复合词中每个单词的首字母大写。Pascal 命名法与驼峰命名法之间的区别在于：在 Pascal 命名法中，所有单词的首字母都大写；而在驼峰命名法中，首字母可以是大写或小写。

Compose 函数：

- **必须是名词**：`DoneButton()`*MUST*
- 不能是动词或动词短语：`DrawTextField()`
- 不能是名词性介词：`TextFieldWithLink()`
- 不能是形容词：`Bright()`
- 不能是副词：`Outside()`
- 名词可以添加描述性形容词作为前缀：`RoundIcon()`

如需了解详情，请参阅[为可组合函数命名](https://github.com/androidx/androidx/blob/androidx-main/compose/docs/compose-api-guidelines.md#naming-unit-composable-functions-as-entities)。





### 组合和重组

“组合”是对 Compose 在执行可组合项时所构建界面的描述。Compose 应用调用可组合函数，以将数据转换为界面。如果发生状态更改，Compose 会使用新状态重新执行受影响的可组合函数，从而创建更新后的界面。这一过程称为“重组”。

如果您想让界面在应用运行时或用户与应用互动时发生变化，需要依赖名为“重组”的进程来更新应用的组合。

当 Compose 首次运行可组合函数时，在初始组合期间，它会跟踪您为了描述组合中的界面而调用的可组合函数。重组是指 Compose 重新执行可能因数据更改而更改的可组合项，然后更新组合以反映所有更改。

组合只能通过初始组合生成且只能通过重组进行更新。重组是修改组合的唯一方式。

在 Compose 中，您可以使用 [`State`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/State?hl=zh-cn) 和 [`MutableState`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/MutableState?hl=zh-cn) 类型让应用中的状态可被 Compose 观察或跟踪。`State` 类型不可变，因此您只能读取其中的值，而 [`MutableState`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/MutableState?hl=zh-cn) 类型是可变的。您可以使用 [`mutableStateOf()`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/package-summary?hl=zh-cn#mutableStateOf(kotlin.Any,androidx.compose.runtime.SnapshotMutationPolicy)) 函数来创建可观察的 `MutableState`。它接受初始值作为封装在 `State` 对象中的形参，这样便可使其 `value` 变为可观察。

`mutableStateOf()` 函数返回的值：

- 会保持状态，即账单金额。
- 可变，因此该值可以更改。
- 可观察，因此 Compose 会观察值的所有更改并触发重组以更新界面。

可组合方法可能会因重组而被系统多次调用。如果不保存，可组合项就会在重组期间重置状态。

可组合函数可以使用 [`remember`](https://developer.android.google.cn/reference/kotlin/androidx/compose/runtime/package-summary?hl=zh-cn#remember(kotlin.Function0)) 跨重组存储对象。初始组合期间，`remember` 函数计算的值会存储在组合中，而存储的值会在重组期间返回。`remember` 和 `mutableStateOf` 函数通常在可组合函数中一起使用，以使状态及其更新正确反映在界面中

```
@Composable
fun EditNumberField(
    modifier: Modifier = Modifier
) {
    // amountInput 是 Compose 跟踪的可变状态，系统会安排重组
    var amountInput by remember { mutableStateOf("") }

    // 计算小费
    val amount = amountInput.toDoubleOrNull() ?: 0.0   // 尝试将输入的字符串转换为 Double
    val tip = calculateTip(amount)

    // 输入框
    TextField(
        value = amountInput,    // 输入框内容
        onValueChange =  { amountInput = it },    // 输入框内容改变的回调
        singleLine = true,  // 将文本框从多行压缩成可水平滚动的单行
        label = { Text(stringResource(R.string.bill_amount)) }, // 输入框的标签
        keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),  // 配置键盘类型
        modifier = modifier
    )
}
```

实际案例参考  [简介：Compose 中的状态](https://developer.android.google.cn/codelabs/basic-android-kotlin-compose-using-state?hl=zh-cn&continue=https%3A%2F%2Fdeveloper.android.google.cn%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-2-pathway-3%3Fhl%3Dzh-cn%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-using-state#6)





- 指示 Compose 在重组期间保留并重复使用其值，您需要使用 `remember` API 进行声明
- 为了让 Compose 在配置更改期间保留状态，您必须使用  `rememberSaveable`。   [activity 生命周期的阶段](https://developer.android.google.cn/codelabs/basic-android-kotlin-compose-activity-lifecycle?hl=zh-cn&continue=https%3A%2F%2Fdeveloper.android.google.cn%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-4-pathway-1%3Fhl%3Dzh-cn%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-activity-lifecycle#4)





### 状态提升

状态提升是一种将状态移到其调用方以使组件变为无状态的模式。

当您需要执行以下操作时，应该提升状态：

- 与多个可组合函数共享状态。
- 创建可在应用中重复使用的无状态可组合项。

[Compose 中的状态提升案例](https://developer.android.google.cn/codelabs/basic-android-kotlin-compose-using-state?hl=zh-cn&continue=https%3A%2F%2Fdeveloper.android.google.cn%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-2-pathway-3%3Fhl%3Dzh-cn%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-using-state#9)



### 无状态

无状态可组合函数是指不存储自身状态的可组合函数，有状态可组合项是指具有可以随时间变化的状态的可组合项，它会显示作为输入实参提供给它的任何状态。相反，有状态可组合项是指具有可以随时间变化的状态的可组合项。

默认情况下，可组合函数是无状态的，这意味着它们不存储值，并且可随时被系统重组，从而导致值被重置。不过，Compose 提供了一种避免这种情况的便捷方式。

可组合函数可以使用 `remember` 可组合函数将对象存储在内存中。

`mutableStateOf()` 函数会返回一个可观察对象。

[创建交互式 Dice Roller 应用](https://developer.android.google.cn/codelabs/basic-android-kotlin-compose-build-a-dice-roller-app?hl=zh-cn&continue=https%3A%2F%2Fdeveloper.android.google.cn%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-2-pathway-2%3Fhl%3Dzh-cn%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fbasic-android-kotlin-compose-build-a-dice-roller-app#6)

```kotlin
/**
 * 创建一个包含骰子图片和滚动按钮的组件
 * 当按钮被点击时，会随机选择一个骰子面，并更新图片
 *
 * @param modifier 组件的修饰符，用于布局、绘制和动画
 */
@Composable
fun DiceWithButtonAndImage(modifier: Modifier = Modifier) {
    // 创建一个可变状态变量，用于保存骰子结果
    var result by remember { mutableStateOf(1) }
    val imageResource = when (result) {
        1 -> R.drawable.dice_1
        2 -> R.drawable.dice_2
        3 -> R.drawable.dice_3
        4 -> R.drawable.dice_4
        5 -> R.drawable.dice_5
        else -> R.drawable.dice_6
    }

    // 创建一个列布局，用于垂直排列元素
    Column (
        modifier = modifier,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Image(
            painter = painterResource(imageResource),
            contentDescription = result.toString()
        )
        Spacer(modifier = Modifier.height(16.dp)) // 在图片和按钮之间添加一个间距
        Button(onClick = { result = (1..6).random()  }) {
            Text(stringResource(R.string.roll))
        }
    }
}
```



### 生命周期





## 界面更新

在 Compose 中，更新界面的唯一方式是更改应用的状态。您可以控制的是界面状态。每当界面的状态发生变化时，Compose 都会重新创建界面树中已更改的部分。可组合函数可以接受状态并公开事件。例如，`TextField`/`OutlinedTextField` 接受值并公开请求回调处理程序更改值的回调 `onValueChange`。

```
//Example code no need to copy over

var name by remember { mutableStateOf("") }
OutlinedTextField(
    value = name,
    onValueChange = { name = it },
    label = { Text("Name") }
)
```

由于可组合函数接受状态并公开事件，因此单向数据流模式非常适合 Jetpack Compose。