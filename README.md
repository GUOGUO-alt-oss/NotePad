# NotePad 记事本应用

这是一个基于 Android 平台开发的记事本应用，使用 Java 语言和 SQLite 数据库实现，采用 ContentProvider 架构提供数据访问接口。

## 功能特性

✅ **创建新笔记** - 支持快速创建新笔记
✅ **查看笔记列表** - 显示所有笔记，包含标题、分类和修改时间
✅ **编辑现有笔记** - 提供带行线的编辑界面
✅ **删除笔记** - 支持通过上下文菜单删除笔记
✅ **按修改时间排序** - 默认按修改时间降序显示
✅ **搜索笔记** - 支持按标题和内容搜索
✅ **主题切换** - 支持明亮/暗黑主题切换
✅ **笔记分类管理** - 支持为笔记添加分类，包括：
  - 未分类
  - 工作
  - 个人
  - 购物
✅ **分类记忆** - 再次编辑笔记时保留之前选择的分类
✅ **支持 Live Folder** - 传统 Android 功能支持
✅ **内容共享与导出** - 支持将笔记导出为文本流

## 技术栈

- **编程语言**: Java
- **开发框架**: Android SDK
- **数据存储**: SQLite 数据库
- **数据访问**: ContentProvider
- **UI 组件**: ListView, SimpleCursorAdapter, Spinner
- **设计模式**: 内容提供者模式、MVC 架构

## 项目结构

```
app/
├── src/main/
│   ├── AndroidManifest.xml       # 应用清单文件，定义组件和权限
│   ├── java/com/example/android/notepad/  # 源代码目录
│   │   ├── NotePad.java          # 定义常量和数据库契约类
│   │   ├── NotePadProvider.java  # 内容提供者实现，处理数据操作
│   │   ├── NotesList.java        # 笔记列表主界面
│   │   ├── NoteEditor.java       # 笔记编辑界面，包含分类选择
│   │   ├── TitleEditor.java      # 标题编辑对话框
│   │   └── NotesLiveFolder.java  # Live Folder 支持
│   └── res/                      # 资源文件目录
│       ├── drawable/             # 图片资源
│       ├── layout/               # 布局文件
│       │   ├── activity_main.xml       # 主界面布局
│       │   ├── note_editor.xml         # 笔记编辑布局
│       │   ├── noteslist_item.xml      # 笔记列表项布局
│       │   └── title_editor.xml        # 标题编辑布局
│       ├── menu/                 # 菜单资源
│       │   ├── editor_options_menu.xml  # 编辑界面菜单
│       │   ├── list_context_menu.xml     # 列表上下文菜单
│       │   └── list_options_menu.xml      # 列表选项菜单
│       └── values/               # 字符串、颜色等配置
│           ├── colors.xml      # 颜色定义
│           ├── strings.xml     # 字符串资源
│           └── styles.xml      # 样式定义
└── build.gradle                  # 模块构建配置
```

## 核心功能实现

### 1. 笔记分类功能

**NoteEditor.java** - 笔记编辑界面
- 添加了 `Spinner` 组件用于分类选择
- 在 `onResume()` 中加载并设置当前笔记的分类
- 在 `onPause()` 和 `updateNote()` 中保存分类信息
- 使用 `ContentValues` 存储分类 ID

**NotesList.java** - 笔记列表界面
- 更新了 `PROJECTION`，添加 `category_id` 字段
- 使用 `ViewBinder` 将分类 ID 转换为分类名称
- 在布局中添加了分类显示 TextView
- 支持搜索结果中显示分类信息

**NotePadProvider.java** - 内容提供者
- 在 `onCreate()` 中为 `notes` 表添加 `category_id` 列
- 在 `sNotesProjectionMap` 中添加 `category_id` 映射
- 更新 `onUpgrade()` 方法，同时处理 `notes` 和 `categories` 表

### 2. 主题切换功能

- 支持明亮和暗黑主题切换
- 使用 `SharedPreferences` 保存主题设置
- 动态重启 Activity 应用新主题

### 3. 搜索功能

- 使用 `SearchView` 组件实现搜索
- 支持按标题和内容搜索
- 实时更新搜索结果

## 数据库结构

### 笔记表 (notes)

| 列名 | 类型 | 描述 |
|------|------|------|
| _id | INTEGER | 主键，自动递增 |
| title | TEXT | 笔记标题 |
| note | TEXT | 笔记内容 |
| created | INTEGER | 创建时间戳 |
| modified | INTEGER | 修改时间戳 |
| category_id | INTEGER | 分类 ID（外键） |

### 分类表 (categories)

| 列名 | 类型 | 描述 |
|------|------|------|
| _id | INTEGER | 主键，自动递增 |
| name | TEXT | 分类名称 |

## 使用说明

1. **创建新笔记**
   - 点击菜单中的"新建"按钮
   - 或长按列表项

2. **编辑笔记**
   - 点击列表中的任意笔记项
   - 在编辑页面修改内容
   - 在底部选择分类
   - 自动保存修改

3. **删除笔记**
   - 长按笔记项
   - 从上下文菜单中选择"删除"

4. **搜索笔记**
   - 使用顶部的搜索框输入关键词
   - 实时查看搜索结果

5. **切换主题**
   - 在选项菜单中选择"明亮主题"或"暗黑主题"
   - 应用自动重启以应用新主题

6. **分类管理**
   - 在编辑页面底部的下拉菜单中选择分类
   - 分类会自动保存
   - 列表中显示每条笔记的分类

## 如何运行项目

### 前置要求

- Android Studio 3.0+（推荐最新版本）
- JDK 8 或更高版本
- Android SDK 21+（Android 5.0 Lollipop 及以上）

### 运行步骤

1. 克隆或下载本项目到本地
   ```bash
   git clone https://github.com/GUOGUO-alt-oss/NotePad.git
   ```

2. 打开 Android Studio，选择"Open an existing Android Studio project"

3. 导航到项目目录并选择

4. 等待 Gradle 同步完成

5. 连接 Android 设备或启动模拟器

6. 点击 Run 按钮运行应用

## 注意事项

1. 本项目是教学示例，实际应用中应考虑：
   - 使用 AsyncTask 或其他异步机制处理数据库操作
   - 添加数据备份和恢复功能
   - 实现更丰富的文本编辑功能
   - 添加云同步支持

2. 数据库操作在 UI 线程中执行，实际应用中应改为异步处理

3. 分类功能使用预设分类，实际应用中可考虑支持自定义分类

## 许可证

```
Copyright (C) 2007 The Android Open Source Project

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

## 版本更新

### 最新版本 (v1.0)
- ✅ 实现笔记分类功能
- ✅ 优化 UI 布局，添加分类显示
- ✅ 支持分类记忆
- ✅ 修复数据库升级问题
- ✅ 更新 README.md 文档

## 实际成果展示

### 应用截图

这里展示应用的实际运行效果：

1. **笔记列表界面**
   - 显示所有笔记，包含标题、分类和修改时间
   - 支持主题切换
   - 顶部搜索功能
    <img width="497" height="882" alt="image" src="https://github.com/user-attachments/assets/78b3d4d2-bb20-4227-a55c-77036915e996" />


2. **笔记编辑界面**
   - 带行线的编辑区域
   - 底部分类选择器
   - 自动保存功能
    <img width="503" height="887" alt="image" src="https://github.com/user-attachments/assets/ebd8584f-9670-4b46-9890-d506e8f70f5f" />


3. **分类选择效果**
   - 支持多种预设分类
   - 再次编辑时保留分类选择
    <img width="499" height="885" alt="image" src="https://github.com/user-attachments/assets/64c5915d-8cb2-4c61-9d58-257eb9775fbd" />
    <img width="505" height="891" alt="image" src="https://github.com/user-attachments/assets/d8a0339b-42fd-481d-80a1-70fee9d469bc" />


4. **搜索功能**
   - 实时搜索结果
   - 支持按标题和内容搜索
    <img width="493" height="882" alt="image" src="https://github.com/user-attachments/assets/fd8f8e9f-6986-4bc5-93e3-3f9e62ddfd22" />
    <img width="499" height="888" alt="image" src="https://github.com/user-attachments/assets/8c3e8f0d-d007-4baa-8b33-06c7c75d8677" />


5. **主题切换**
   - 明亮主题
   - 暗黑主题
   <img width="504" height="876" alt="image" src="https://github.com/user-attachments/assets/316c65d3-db53-47ef-96eb-97442feb386a" />
   <img width="500" height="877" alt="image" src="https://github.com/user-attachments/assets/c08215ad-28ac-4015-b3e1-04d8e2420648" />



### 使用示例

```
1. 打开应用，进入笔记列表
2. 点击"新建"按钮创建笔记
3. 在编辑页面输入内容
4. 在底部选择分类
5. 点击返回键自动保存
6. 在列表中查看带有分类的笔记
7. 使用搜索框查找特定笔记
```

## 贡献

欢迎提交 Issue 和 Pull Request！

## 联系方式

如有问题或建议，请通过 GitHub Issues 提出。
