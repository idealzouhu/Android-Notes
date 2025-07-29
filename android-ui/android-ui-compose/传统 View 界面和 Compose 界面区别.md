##  一、Android 传统界面设计

在 **Compose 之前**，Android 的界面设计主要基于 [**传统的 View 系统** 和 **XML 布局**](https://developer.android.google.cn/develop/ui/views/layout/declaring-layout?hl=zh-cn)。

### **基于 View 和 ViewGroup 的层级结构**

**（1）View 系统的基本组成**

- **`View`**：所有 UI 组件的基类（如 `Button`、`TextView`）。
- **`ViewGroup`**：继承自 `View`，用于容纳和管理子 `View`（如 `LinearLayout`、`RelativeLayout`）。
- **布局方式**：通过 **XML 文件** 定义 UI 结构，运行时由系统解析成 `View` 对象树。

**（2）布局示例（XML + Java/Kotlin）**

```xml
<!-- res/layout/activity_main.xml -->
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello Android!" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Click Me" />
</LinearLayout>
```

在 Activity 中绑定：

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main) // 加载 XML 布局

        val textView = findViewById<TextView>(R.id.textView)
        val button = findViewById<Button>(R.id.button)
        button.setOnClickListener {
            textView.text = "Button Clicked!"
        }
    }
}
```



### **界面渲染流程（传统 View 系统）**

（**1）测量（Measure）**

- 系统递归遍历 `View` 树，调用每个 `View` 的 `onMeasure()`，确定其大小。
- 父 `ViewGroup` 根据子 `View` 的测量结果调整自身尺寸。

**（2）布局（Layout）**

- 调用 `onLayout()`，确定每个 `View` 在屏幕上的位置（基于 `left`, `top`, `right`, `bottom`）。

**（3）绘制（Draw）**

- 调用 `onDraw()`，将 `View` 的内容渲染到屏幕上（如文本、背景、图片）。

**（4）关键问题**

- **性能瓶颈**：嵌套 `ViewGroup` 会导致多次测量和布局（如 `RelativeLayout` 需要 2 次测量）。
- **状态管理困难**：UI 更新需手动调用 `invalidate()` 或 `requestLayout()`。



###  **数据绑定与 UI 更新**

**（1）手动更新**

```kotlin
// 传统方式：手动修改 View 状态
button.setOnClickListener {
    textView.text = "New Text"  // 直接操作 View
}
```

**（2）数据绑定（Data Binding）**

```xml
<!-- 启用 Data Binding -->
<layout>
    <data>
        <variable name="viewModel" type="com.example.MyViewModel" />
    </data>
    <TextView
        android:text="@{viewModel.text}" />
</layout>
```

```kotlin
// 通过 LiveData 自动更新
viewModel.text.observe(this) { newText ->
    binding.textView.text = newText
}
```





### 传统方式的局限性

| **问题**           | **原因**                                                 |
| :----------------- | :------------------------------------------------------- |
| **XML 与代码分离** | UI 逻辑分散在 XML 和代码中，维护成本高。                 |
| **性能问题**       | 深层嵌套布局导致渲染效率低（如 ListView 未优化时卡顿）。 |
| **状态管理复杂**   | 需要手动同步数据和 UI（如 `notifyDataSetChanged()`）。   |
| **动态 UI 支持差** | XML 布局难以实现复杂动画或条件化 UI。                    |





## 二、**Jetpack Compose** 界面设计

### 2.1 **为什么 Google 推出 Compose**

Compose 具备以下优点：

- **提高开发效率**：用 Kotlin 代码替代 XML，减少模板代码。
- **更好的性能**：智能重组（Recomposition）避免不必要的渲染。
- **现代化 UI**：原生支持 Material Design 3、动画、深色模式。
- **统一开发范式**：与 Flutter/SwiftUI 对齐，降低跨平台学习成本。
- **兼容性**：Compose 可与传统 `View` 混合使用（通过 `AndroidView`）。

同时，相对于传统设计，Jetpack compose 做出了如下改进：

| **传统 View 系统**                   | **Jetpack Compose**                      |
| :----------------------------------- | :--------------------------------------- |
| 基于 **命令式编程**（手动更新 View） | 基于 **声明式编程**（自动响应状态变化）  |
| **XML + View 对象树**                | **纯 Kotlin 代码**（`@Composable` 函数） |
| **测量/布局/绘制分离**               | **自动重组**（仅更新变化的部分）         |
| **状态管理困难**                     | **状态提升**（单向数据流）               |



### 2.2 代码对比案例







## 参考资料

[View 中的布局  | Views  | Android Developers](https://developer.android.google.cn/develop/ui/views/layout/declaring-layout?hl=zh-cn)