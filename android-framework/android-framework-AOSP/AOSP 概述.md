## 一、AOSP 概述

### 1.1 什么是 AOSP

**AOSP（Android Open Source Project，安卓开源项目）** 是由 Google 主导的开放源代码项目，旨在提供 Android 操作系统的完整基础框架和核心组件。它是 Android 生态系统的基石，手机厂商、开发者和其他组织可以基于 AOSP 定制自己的 Android 系统。

AOSP 和 厂商定制 Android 系统的区别：

| **对比项**      | **AOSP（原生安卓）**     | **厂商定制系统（如 MIUI、OneUI）**     |
| :-------------- | :----------------------- | :------------------------------------- |
| **代码来源**    | Google 官方开源          | 基于 AOSP 深度修改                     |
| **Google 服务** | 不包含（需单独授权）     | 部分厂商预装 GMS（如华为 EMUI 国际版） |
| **UI 和功能**   | 简洁，接近 Pixel 体验    | 增加独家功能（如小窗模式、游戏加速）   |
| **更新速度**    | 由 Google 主导，更新较快 | 依赖厂商适配，通常滞后                 |



### 1.2 AOSP 的核心组成

AOSP 包含 Android 系统的所有底层代码和核心功能，主要分为以下几部分：

- **Linux 内核**：提供硬件驱动、进程管理、内存管理等底层支持。
- **HAL（硬件抽象层）**：让 Android 兼容不同厂商的硬件（如摄像头、传感器）。
- **Android Runtime（ART）**：负责执行 Android 应用的虚拟机（替代早期的 Dalvik）。
- **Framework 层**：提供四大组件（Activity、Service、Broadcast、ContentProvider）和系统 API。
- **系统应用**：如 Launcher（桌面）、Settings（设置）、Dialer（电话）等基础应用。
- **工具链**：编译工具（如 `make`、`soong`）、调试工具（ADB、Logcat）等。





### 1.3 **AOSP 的特点**

✅ **开源免费**：代码公开（Apache 2.0/GPL 等协议），可自由下载、修改和分发。
✅ **高度可定制**：厂商可修改 AOSP 代码，打造自己的 Android 系统（如 MIUI、ColorOS）。
✅ **不包含 Google 服务**：AOSP 本身没有 Google Mobile Services（GMS），如 Play Store、Gmail 等，需额外授权。



### 1.4 **AOSP 的应用场景**

- **手机厂商**：基于 AOSP 开发自己的 ROM（如小米 MIUI、OPPO ColorOS）。
- **开发者**：学习 Android 底层机制，或为特定设备（如物联网设备）定制系统。
- **第三方 ROM**：如 LineageOS、Pixel Experience 等社区项目。
- **Google 官方设备**：Pixel 手机的系统 = AOSP + 独家优化 + GMS。



## 二、AOSP 编译具体流程

### 开发环境搭建

下载虚拟机，并创建 Ubuntu 系统

- repo 工具

  ```
  mkdir ~/bin
  curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo -o ~/bin/repo
  chmod +x ~/bin/repo
  ```

- 



### 获取和编译 AOSP

1. **下载源码**：

   ```
   repo init -u https://android.googlesource.com/platform/manifest
   repo sync
   ```

2. **选择版本**：如 `android-13.0.0_r1`（对应 Android 13）。

3. **编译系统**：

   ```
   source build/envsetup.sh
   lunch aosp_arm-eng  # 选择目标设备
   make -j8           # 开始编译
   ```

4. **刷机测试**：通过 `fastboot` 刷入支持 AOSP 的设备（如 Pixel）。







## 参考资料

[[视频教程\]写给应用开发的 Android Framework 教程——玩转 AOSP 篇之极速上手Android Fr - 掘金](https://juejin.cn/post/7202634945171537977)