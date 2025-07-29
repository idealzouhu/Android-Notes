### 什么是 Package Manager Service

在 Android 系统中，**Package Manager Service（PMS，包管理服务）** 是负责管理应用程序安装、卸载、更新以及权限控制的**核心系统服务**。它与 AMS（Activity Manager Service）协同工作，共同维护应用的生命周期和系统安全性。





## **PMS 的核心功能**

1. **应用安装与卸载**

- **解析 APK 文件**：读取 `AndroidManifest.xml` 中的包名、版本号、权限、组件（Activity/Service 等）信息。
- **安装流程**：
  - 复制 APK 到 `/data/app` 目录。
  - 为应用分配唯一的 Linux UID（用户 ID）和 GID（组 ID）。
  - 生成优化后的 `odex` 或 `oat` 文件（加快应用启动）。
- **卸载应用**：删除 APK 文件、数据目录（`/data/data/<package-name>`）和缓存。

2. **应用信息查询**

- 提供接口供其他服务（如 AMS、Launcher）查询：
  - 已安装应用列表。
  - 应用的 `AndroidManifest.xml` 配置（如声明的 Activity、Service）。
  - 应用版本号、签名信息。

3. **权限管理**

- 维护所有应用的权限声明（如 `Manifest.permission.CAMERA`）。
- 在应用安装时检查权限的合法性（如系统权限需匹配签名）。
- 运行时权限（Android 6.0+）的动态授权记录。

4. **组件可见性控制**

- 决定哪些组件（如 Activity、Service）可以被其他应用调用（通过 `exported` 属性）。
- 处理隐式 Intent 的匹配（例如启动一个 `ACTION_VIEW` 时，PMS 会找到所有能处理该 Intent 的应用）。

5. **多用户支持**

- 管理不同用户（Profile）下的应用安装状态（如工作资料和个人资料隔离）。



### 开发者如何与 PMS 交互？

1. **通过 `PackageManager` 类**

   ```java
   PackageManager pm = getPackageManager();
   
   // 获取应用信息
   ApplicationInfo appInfo = pm.getApplicationInfo("com.example.app", 0);
   
   // 查询所有已安装应用
   List<ApplicationInfo> apps = pm.getInstalledApplications(0);
   
   // 检查权限
   boolean hasPermission = pm.checkPermission("android.permission.CAMERA", "com.example.app");
   ```

2. **解析 Intent 的接收者**

```java
// 查询能处理 ACTION_VIEW 的所有 Activity
Intent intent = new Intent(Intent.ACTION_VIEW);
List<ResolveInfo> activities = pm.queryIntentActivities(intent, 0);
```

3. **AndroidManifest.xml 配置**

   声明权限：

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

- 控制组件导出：

  ```xml
<activity android:name=".MainActivity" android:exported="true" />
  ```







### 学习目标

- 第一节 前言 PKMS 是什么东西？
- 第二节 PKMS 概述信息
- 第三节 PKMS角色位置
- 第四节 PKMS 启动过程分析
- 第五节 APK的扫描
- 第七节 PMS之权限扫描
- 第八节 PackageManagerService大综合笔记