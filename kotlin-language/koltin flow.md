[使用 Room 持久保留数据  | Android Basics Compose - Use Room for data persistence  | Android Developers](https://developer.android.google.cn/courses/pathways/android-basics-compose-unit-6-pathway-2?hl=zh-cn)： 了解功能强大且富有表现力的 Flow API，以及如何在 Android 应用中充分利用 Flow，提供了相应的视频讲解。



[在 Android 上测试 Kotlin 数据流  | Android Developers](https://developer.android.google.cn/kotlin/flow/test?hl=zh-cn&continue=https%3A%2F%2Fdeveloper.android.google.cn%2Fcourses%2Fpathways%2Fandroid-basics-compose-unit-6-pathway-2%3Fhl%3Dzh-cn%23article-https%3A%2F%2Fdeveloper.android.com%2Fkotlin%2Fflow%2Ftest)







```
val blurUiState: StateFlow<BlurUiState> = bluromaticRepository.outputWorkInfo
        .map { info ->
            when {
                info.state.isFinished -> {
                    BlurUiState.Complete(outputUri = "")
                }
                info.state == WorkInfo.State.CANCELLED -> {
                    BlurUiState.Default
                }
                else -> BlurUiState.Loading
            }
        }.stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5_000),
            initialValue = BlurUiState.Default
        )
```

