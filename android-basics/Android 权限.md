## 一、Android 权限分类

### 1.1 按保护级别（Protection Level）分类对比

| **类别**     | **特点**                                       | **授权方式**                   | **示例权限**                                                 |
| :----------- | :--------------------------------------------- | :----------------------------- | :----------------------------------------------------------- |
| **普通权限** | 低风险，不影响用户隐私或设备安全               | 安装时自动授予（无需用户确认） | `INTERNET`、`ACCESS_NETWORK_STATE`、`BLUETOOTH`、`VIBRATE`   |
| **危险权限** | 涉及用户隐私或设备安全（如相机、位置、存储等） | 运行时动态请求（用户手动授权） | `CAMERA`、`READ_CONTACTS`、`ACCESS_FINE_LOCATION`、`RECORD_AUDIO` |
| **签名权限** | 仅允许相同签名的应用访问                       | 系统自动授予（同签名应用）     | `BIND_ACCESSIBILITY_SERVICE`、`INSTALL_PACKAGES`             |
| **特殊权限** | 需用户通过系统设置手动授权                     | 跳转到系统设置页面授权         | `WRITE_SETTINGS`、`SYSTEM_ALERT_WINDOW`、`REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` |





### 谨慎使用权限组

原则上，**用户一旦同意了某个权限申请之后，同组的其他权限也会被系统自动授权**。但是请谨记，不要基于此规则来实现任何功能逻辑，因为Android系统随时有可能调整权限的分 组





### 1.2 **按使用场景分类对比**

| **类别**       | **特点**                                 | **适用版本** | **示例权限**                                      |
| :------------- | :--------------------------------------- | :----------- | :------------------------------------------------ |
| **安装时权限** | 普通权限在安装时自动授予                 | Android 11+  | `INTERNET`、`ACCESS_WIFI_STATE`                   |
| **运行时权限** | 危险权限需在运行时动态请求               | Android 6.0+ | `READ_EXTERNAL_STORAGE`、`ACCESS_COARSE_LOCATION` |
| **后台权限**   | 部分权限（如位置）在后台使用时需额外声明 | Android 10+  | `ACCESS_BACKGROUND_LOCATION`                      |



#### 为什么引入运行时权限

这样做的主要目的是保护用户隐私和安全，减少恶意应用滥用权限。

- **问题**：早期 Android 系统在安装时要求用户一次性授予所有权限，用户无法细粒度控制敏感权限（如相机、位置、通讯录等）。
- **解决方案**：运行时权限将危险权限（Dangerous Permissions）的授权延迟到应用实际需要使用时，用户可明确知晓权限用途并动态管理。
  - **示例**：用户只有在使用拍照功能时才会被请求相机权限，而非安装时就强制同意。

运行时权限的核心就是在程序运行过程中由用户授权我们去执行某些危险操作，程序是不可以擅自做主去执行这些危险操作的。关键实现步骤是：

1. **检查权限状态**: 使用 `ContextCompat.checkSelfPermission()` 方法进行检查。

   ```java
   if (ContextCompat.checkSelfPermission(context, Manifest.permission.CAMERA) 
       != PackageManager.PERMISSION_GRANTED) {
       // 未授权，需要请求
   }
   ```

2. **请求权限**：如果没有授权的话，则需要调用 `ActivityCompat.requestPermissions()` 方法向用户申请授权。

   ```
   ActivityCompat.requestPermissions(activity, 
       new String[]{Manifest.permission.CAMERA}, 
       REQUEST_CODE);
   ```

3. **处理用户选择**

   ```
   @Override
   public void onRequestPermissionsResult(int code, String[] permissions, int[] results) {
       if (code == REQUEST_CODE && results[0] == PERMISSION_GRANTED) {
           // 用户同意，执行操作
       } else {
           // 用户拒绝，提示或降级处理
       }
   }
   ```

   



### 1.3 **按权限类型分类对比**

| **类别**         | **功能范围**                         | **示例权限**                                              |
| :--------------- | :----------------------------------- | :-------------------------------------------------------- |
| **硬件相关权限** | 访问设备硬件功能（相机、传感器等）   | `CAMERA`、`BLUETOOTH_SCAN`、`BODY_SENSORS`                |
| **数据相关权限** | 访问用户数据（通讯录、存储、短信等） | `READ_CONTACTS`、`READ_SMS`、`WRITE_EXTERNAL_STORAGE`     |
| **系统级权限**   | 控制系统行为或跨应用交互             | `BIND_NOTIFICATION_LISTENER_SERVICE`、`CHANGE_WIFI_STATE` |





## 参考资料

[官方文档](https://developer.android.com/guide/topics/permissions/overview)