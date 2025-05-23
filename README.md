# 支持多构建系统的混合技术栈（Swift、OC、Rust）仿飞书 APP 开发

## 仓库介绍

该仓库的初始化是基于「Feishu-clone」仓库的代码之上进行重构的；核心的变动是项目中 Framework 的模块划分，以及更加合理的和可扩展的项目架构。

如果想要查看项目重构前的 commit，可以[点击跳转 ➡️](https://github.com/notURandomDev/Feishu-clone) 。

该仓库包含了两个主要的工程目录：

- LarkClone：使用 Swift 语言开发的 iOS App 工程
- RustSDK：使用 Rust 语言开发的 SDK 工程

## 开发环境配置

### 快速配置（推荐）

> 注：在最新的代码版本中，无需进行该配置；在首次构建项目时，Rust 工具链会自动下载。下载需要 1min - 2min（视网络情况），请耐心等待

成功 clone 项目到本地之后，请在项目根目录中打开终端，运行 `./setup.sh` 脚本安装所需的 Rust 工具链、目标平台与头文件生成工具：

```bash
bash ./scripts/setup_rust.sh
```

该脚本会自动完成以下操作：

- 安装 Rust 工具链（通过 rustup）
- 安装 cargo 与 cbindgen
- 安装 iOS 目标平台（aarch64-apple-ios-sim）

### 手动配置

你也可以手动执行以下命令：

```bash
# 安装 Rust 工具链
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 添加 Rust bin 到 PATH
export PATH="$HOME/.cargo/bin:$PATH"

# 安装 C 头文件生成工具
cargo install cbindgen

# 添加 iOS 模拟器目标平台
rustup target add aarch64-apple-ios-sim

```

### RustSDK 构建

目前，项目暂不支持使用 Run Script 在构建阶段自动构建 RustSDK；需要你在项目根目录打开终端，运行以下指令：

```bash
# 进入 RustSDK 目录
cd RustSDK

# 清空构建
cargo clean

# 构建 RustSDK
cargo build --release --target aarch64-apple-ios-sim
```

此方法只是目前的 workaround ，在 Run Script 中构建 RustSDK 会在以后的版本中支持。

### 使用 xcodebuild 进行构建

如果你想将此项目进行 CI/CD 集成；或者有脱离 Xcode，使用其它 IDE 或者代码编辑器进行开发的需求，可以通过命令行进行项目的构建：

> 注：请在项目根目录中运行指令

**运行构建 LarkClone 的脚本：**

```bash
bash ./scripts/xcodebuild/build.sh
```

**运行清理 LarkClone 构建的脚本：**

```bash
bash ./scripts/xcodebuild/clean.sh
```

### 使用 simctl 控制模拟器

在项目构建完成之后，可以通过以下命令来控制模拟器以及 _Simulator_ 的图形化界面：

> 注：请在项目根目录中运行指令

**启动模拟器，并安装和运行应用：**

```bash
bash ./scripts/simctl/run.sh
```

**关闭正在运行的模拟器：**

```bash
bash ./scripts/simctl/shutdown.sh
```

### 集成 VS Code 开发环境

1. 在 VS Code 中打开项目
2. 按下 Cmd + Shift + P 选择 Run Task
3. 选择 Task 并运行，将会自动运行脚本
   1. `[xcodebuild] 清理 LarkClone 构建` : 运行 `./scripts/xcodebuild/build.sh`
   2. `[xcodebuild] 构建 LarkClone` : 运行 `./scripts/xcodebuild/clean.sh`
   3. `[simctl] 启动模拟器，并安装和运行应用` : 运行 `./scripts/simctl/run.sh`
   4. `[simctl] 关闭正在运行的模拟器` : 运行 `./scripts/simctl/shutdown.sh`

## 如何贡献

### Pull Request

通过提交 Pull Request，为项目贡献新功能的代码实现或 Bug 的修复。

注意，请尽量使 PR 原子化；即，每个 PR 应只解决一个问题或实现一个功能。例如，文档更新、代码格式化、功能实现应通过不同的 PR 进行提交；这样有利于仓库管理者在合并 PR 的时候尽可能地减小冲突。

### Commit

PR 中每次的 Commit 信息应尽可能地反映更改内容。可以参照以下格式编写 Commit 信息：

```
<类型>: <简短描述>  // 标题

<详细说明>         // 正文（可选）
```

Commit 标题的类型，可以参照下表：

| 类型       | 用途                       | 示例                         |
| ---------- | -------------------------- | ---------------------------- |
| `feat`     | 新增功能                   | `feat: 添加邮箱页面`         |
| `fix`      | 修复 Bug                   | `fix: 解决plist读取问题`     |
| `docs`     | 文档更新                   | `docs: 更新README文档`       |
| `style`    | 代码格式调整（不影响逻辑） | `style: 格式化缩进`          |
| `refactor` | 代码重构（非功能修改）     | `refactor: 优化通信逻辑`     |
| `test`     | 测试相关                   | `test: 添加文件读取单元测试` |
| `chore`    | 构建/依赖管理等杂项        | `chore: 升级xcodebuild`      |

## 项目概要介绍

该项目是字节跳动-飞书客户端（iOS）技术训练营的课题。

### 课题名称

开发一个简易的仿飞书 App，包含「消息」和「邮箱」两个 Tab。

### 课题要求

- UI 部分使用 Swift、OC 开发
- 底层数据服务使用 Rust 开发
- Swift、OC 与 Rust 使用 protobuf 通信
- 支持 xcodebuild、bazel 两套构建系统

### 课题目的

飞书技术训练营希望能够通过这个课题，使参与开发的同学了解：

1. 基础 iOS UI 开发，使用 Swift、OC，开发一个多 Tab 应用，包含下面一些 UI 开发技术
   1. UIImage、UIImageView、UIButton、UILabel、UIViewController 等基础控件
   2. 使用 autolayout 进行布局
   3. 简单的 UI 转场动画
2. 基础移动端 SDK 开发，使用 Rust 语言，开发一套数据解析 SDK
   1. SDK 与 UI 部分，使用 protobuf 进行通信
3. 了解构建系统，了解 Xcodebuild、Bazel 的作用，各自的优势
   1. 通过命令行，调用 xcodebuild 命令，进行 App 开发
   2. 将基于 Xcodebuild 构建系统的 App 转成 Bazel 构建系统

### 竞品参考

飞书

## LarkClone

该目录下包含了所有 iOS App 开发所涉及到的代码文件。

```text
main.swift
---------
MessengerTab.framework  MailTab.framework （Swift、OC）
---------
LarkColor.framework   LarkLoadMore.framework  LarkAvatar.framework  LarkNavigation.framework
---------
LarkSDKPB.framework
---------
LarkSDK.framework
```

### 项目结构

AppDelegate.swift 和 SceneDelegate.swift 暂时放在了 LarkClone 的根目录下。

`Framework/` 目录下分成了三个子目录，分别为：

- Core：核心基础框架，提供网络、数据模型、工具类等基础能力
  - LarkFoundation、LarkSDK、LarkSDKPB
- Tabs：独立页面模块
  - MessengerTab、MailTab
- UI：通用 UI 组件
  - LarkColor、LarkLoadMore、LarkAvatar、LarkNavigation

### Tab 框架结构

Tab 框架（framework）的目录下主要有三个子目录：

- Resources：资源文件
- Sources：主要代码文件
- Tests：测试相关文件

其中，`Source/` 目录结构遵循 MVC（Model-View-Controller）原则进行解耦：

- Models：框架中使用到的结构体
- Views：与视图相关的文件（如 Cell、ViewController）
- Controllers：用于操作数据（如 DataManager）

除此之外，还有一些工具类型的文件存于以下目录：

- Scripts：脚本文件
- Extensions：协议的拓展

## RustSDK

### 工程概述

该目录下的工程使用 Rust 语言开发，用于模拟飞书应用中处理网络请求的 SDK ；工程代码的主要功能是：

- 解析 plist 的 mock 数据，生成相应的 Rust 数据体
- 将 Rust 数据体编码成 Protobuf Buffers（Protobuf），写入内存
- 通过 C 语言的 ABI（Application Binary Interface） 能力，暴露 FFI（Foreign Function Interface）接口，供 Swift 侧调用

其中，该工程需要编译成静态库，放置到 Swift 侧的工程中，以进行 SDK 的调用。

### 项目结构

`lark_sdk/` 目录下的代码实现了将 plist 数据转换成 Rust 数据体，以及异步函数的实现。

- async_handler.rs：定义了异步函数相关逻辑，供 Swift 侧调用
- data.rs：定义了结构体
- plist.rs：将 plist 数据转换成 Rust 数据体

`lark_sdk_pb/` 目录下的代码主要定义了 .proto 以及 Rust 数据体序列化的实现。

- contact.proto：联系人实体的 .proto 文件
- contact.rs：在 .proto 文件之上，由 Rust 自动生成的具有序列化和反序列化能力的 Rust 结构体
- mod.rs：将 Rust 数据体编码成 Protobuf 数据的实现逻辑

最后，`src/` 根目录下的 lib.rs 文件将 Rust 代码封装成高层次的接口进行暴露，供 Swift 侧通过静态库调用。

## Bazel构建系统

### 工程描述
构建系统迁移，从Xcodebuild构建系统切换到Bazel构建系统。Bazel 是一个高效、可扩展的构建工具，支持多语言和跨平台开发，通过增量构建、并行化及精准依赖管理，显著提升大型项目的构建速度与可靠性。

### 项目结构
```text
\
├── LarkClone //LarkClone模块、App、Tab模块
│   └── Frameworks
│       ├── Core  
│       │   ├── LarkBridgeModels  //LarkBridgeModels模块
│       │   ├── LarkSDK //LarkSDK模块
│       │   └── LarkSDKPB //LarkSDKPB模块
│       └── UI  
│           ├── Components //Components模块
│           ├── LarkAvatar //LarkAvatar模块
│           ├── LarkChatBubble //LarkChatBubble模块
│           ├── LarkColor //LarkColor模块
│           ├── LarkLaunchScreen //LarkLaunchScreen模块
│           └── LarkSearchBar //LarkSearchBar模块
├── RustSDK //构建Rust静态库
├── platforms //定义Bazel构建系统构建目标
├── BUILD.bazel      //构建xcodeproj
├── MODULE.bazel     //定义项目的依赖关系、版本和工具链
└── build.sh         //构建脚本
```

`build.sh`包含功能
- `--clean`清除构建目录
- `--build`构建app
- `--debug`安装调试app
- `--setupenv`设置Rust环境变量
- `--generate`生成mock数据
- `--help`显示帮助

自定义构建目标
```
xcode项目:
//:xcodeproj

App模块:
//LarkClone:LarkClone
//LarkClone:LarkCloneResources
//LarkClone:LarkCloneApp

Core模块:
//LarkClone/Frameworks/Core/LarkBridgeModels:LarkBridgeModels
//LarkClone/Frameworks/Core/LarkBridgeModels:LarkBridgeModelsHeaders
//LarkClone/Frameworks/Core/LarkSDK:LarkSDK
//LarkClone/Frameworks/Core/LarkSDKPB:LarkSDKPB

UI模块:
//LarkClone/Frameworks/UI/Components:Components
//LarkClone/Frameworks/UI/LarkAvatar:LarkAvatar
//LarkClone/Frameworks/UI/LarkChatBubble:LarkChatBubble
//LarkClone/Frameworks/UI/LarkColor:LarkColor
//LarkClone/Frameworks/UI/LarkLaunchScreen:LarkLaunchScreen
//LarkClone/Frameworks/UI/LarkSearchBar:LarkSearchBar

Tab模块:
//LarkClone:MailTab
//LarkClone:MailTabResources
//LarkClone:MessengerTab
//LarkClone:MessengerTabResources

RustSDK模块:
//RustSDK:RustSDKHeaders
//RustSDK:proto_swift
//RustSDK:protos
//RustSDK:rust_sdk
//RustSDK:rust_sdk_lib

目标平台模块:
//platforms:aarch64_apple_ios
```

### 参考资料
- `Bazel官方文档`
- `bazel仓库`：`https://registry.bazel.build/` 



## 相关链接

### notURandomDev

来自 **notURandomDev** 开发过程中的产出，仅供参考：

- [开发日志](https://www.notion.so/1df2ad4d78728035ac70e561191aee9d?pvs=21)：记录开发过程中遇到的问题，以及对技术的理解
- [Git 学习笔记](https://www.notion.so/Git-1e32ad4d78728044bc68ceee16c00132?pvs=21)：在这个项目中，我大量使用了 Git 和 GitHub；指令很多，需要总结
- [Rust 学习笔记](https://www.notion.so/Rust-1e42ad4d787280838c14c801f8aab3e3?pvs=21)：0 基础学习；语法现代的底层语言，学习曲线陡峭
- [Swift 学习笔记](https://www.notion.so/Swift-1e02ad4d7872803ea110d71e4b3e2932?pvs=21)：0 基础学习；除了语言的学习之外，还积累了 iOS 开发中的工程经验
