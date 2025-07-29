### 什么是 AMS 服务

在 Android 操作系统（OS）中，**AMS** 是 **Activity Manager Service**（活动管理服务）的缩写，它是 Android 系统核心服务之一，负责管理应用程序的 **Activity（活动）** 生命周期、任务栈（Task Stack）、进程调度以及应用程序的启动和切换等关键功能。



### **AMS 的核心功能**

1. **Activity 生命周期管理**
   - 控制 Activity 的创建、启动、暂停、恢复、停止和销毁（如 `onCreate()`, `onResume()`, `onDestroy()` 等回调）。
   - 处理 Activity 之间的跳转（如 `startActivity()` 的调用）。
2. **任务栈（Task Stack）管理**
   - 维护 Activity 的返回栈（Back Stack），确保用户按“返回键”时能正确回退。
   - 支持多任务模式（如分屏、画中画）下的栈管理。
3. **进程和内存管理**
   - 根据系统资源（如内存不足）决定哪些进程需要被终止（通过 `Low Memory Killer` 机制）。
   - 标记进程的优先级（如前台进程、可见进程、服务进程等）。
4. **应用程序启动控制**
   - 解析 `Intent`（如显式/隐式 Intent），找到匹配的目标 Activity。
   - 处理应用冷启动、热启动等场景。
5. **权限和安全性检查**
   - 验证调用方是否有权限启动特定 Activity 或 Service（如跨应用启动时的权限控制）。
6. **多窗口模式支持**
   - 管理分屏、自由窗口等模式的 Activity 协调。



### **AMS 在 Android 架构中的位置**

- **系统服务**：AMS 运行在 `system_server` 进程中，是 Android 系统核心服务之一，由 `SystemServer` 启动。
- **Binder 通信**：应用进程通过 Binder IPC 机制与 AMS 交互（如 `IActivityManager` 接口）。
- **与其他组件协作**：
  - **WMS（Window Manager Service）**：管理窗口的显示和层叠。
  - **PMS（Package Manager Service）**：处理应用包信息查询。
  - **四大组件**：管理 Activity、Service、BroadcastReceiver 的生命周期。



### **开发者如何与 AMS 交互？**

- **通过 `ActivityManager` 类**：
  开发者可以通过 `Context.getSystemService(Context.ACTIVITY_SERVICE)` 获取 `ActivityManager` 对象，查询运行中的任务、内存信息等。

  ```java
  ActivityManager am = (ActivityManager) getSystemService(Context.ACTIVITY_SERVICE);
  List<ActivityManager.RunningTaskInfo> tasks = am.getRunningTasks(10);
  ```

- **隐式 Intent 的解析**：
  当调用 `startActivity()` 时，AMS 会解析 Intent 并匹配目标 Activity。

- **`AndroidManifest.xml` 配置**：
  通过 `launchMode`（如 `standard`, `singleTop`）或 `taskAffinity` 影响 AMS 的任务栈行为。