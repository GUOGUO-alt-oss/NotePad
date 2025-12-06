# NotePad 记事本应用

这是一个基于Android平台的记事本应用，是官方早期的数据库操作基本教程项目。该应用演示了如何使用Android的ContentProvider和SQLite数据库来管理和存储用户笔记。

## 功能特性

- ✅ 创建新笔记
- ✅ 查看笔记列表
- ✅ 编辑现有笔记
- ✅ 删除笔记
- ✅ 按修改时间排序显示笔记
- ✅ 搜索笔记功能
- ✅ 主题切换支持（明亮/暗黑模式）
- ✅ 笔记分类管理
- ✅ 支持Live Folder（传统功能）
- ✅ 内容共享与导出

## 技术栈

- **编程语言**: Java
- **开发框架**: Android SDK
- **数据存储**: SQLite 数据库
- **数据访问**: ContentProvider
- **UI组件**: ListView, SimpleCursorAdapter
- **设计模式**: 内容提供者模式、MVC架构

## 项目结构

```
app/
├── src/main/
│   ├── AndroidManifest.xml       # 应用清单文件，定义组件和权限
│   ├── java/com/example/android/notepad/  # 源代码目录
│   │   ├── NotePad.java          # 定义常量和数据库契约类
│   │   ├── NotePadProvider.java  # 内容提供者实现
│   │   ├── NotesList.java        # 笔记列表主界面
│   │   ├── NoteEditor.java       # 笔记编辑界面
│   │   ├── TitleEditor.java      # 标题编辑对话框
│   │   └── NotesLiveFolder.java  # Live Folder支持
│   └── res/                      # 资源文件目录
│       ├── drawable/             # 图片资源
│       ├── layout/               # 布局文件
│       ├── menu/                 # 菜单资源
│       └── values/               # 字符串、颜色等配置
└── build.gradle                  # 模块构建配置
```

## 核心代码说明

### 1. 数据模型与契约 (NotePad.java)

定义了数据库表结构、列名、URI和MIME类型等常量，作为应用的核心数据契约。

```java
public static final class Notes implements BaseColumns {
    public static final String TABLE_NAME = "notes";
    public static final String COLUMN_NAME_TITLE = "title";
    public static final String COLUMN_NAME_NOTE = "note";
    public static final String COLUMN_NAME_CREATE_DATE = "created";
    public static final String COLUMN_NAME_MODIFICATION_DATE = "modified";
    public static final String COLUMN_NAME_CATEGORY_ID = "category_id";
    // 其他常量定义...
}
```

### 2. 内容提供者 (NotePadProvider.java)

实现了ContentProvider接口，处理笔记数据的增删改查操作，是应用的数据层核心。

主要功能：
- 数据库创建与升级
- 提供URI匹配机制
- 实现query、insert、update、delete等数据操作方法
- 支持数据共享和流导出

### 3. 笔记列表界面 (NotesList.java)

应用的主界面，显示所有笔记的列表，支持上下文菜单操作。

关键特性：
- 使用SimpleCursorAdapter绑定数据到ListView
- 实现搜索功能
- 支持主题切换
- 提供添加、编辑、删除笔记的入口

### 4. 笔记编辑器 (NoteEditor.java)

提供笔记内容的创建和编辑功能，支持保存和撤销操作。

## 如何运行项目

### 前置要求

- Android Studio 3.0+（推荐最新版本）
- JDK 8或更高版本
- Android SDK 21+（Android 5.0 Lollipop及以上）

### 运行步骤

1. 克隆或下载本项目到本地
2. 打开Android Studio，选择"Open an existing Android Studio project"
3. 导航到项目目录并选择
4. 等待Gradle同步完成
5. 连接Android设备或启动模拟器
6. 点击Run按钮运行应用

## 数据库结构

### 笔记表 (notes)

| 列名 | 类型 | 描述 |
|------|------|------|
| _id | INTEGER | 主键，自动递增 |
| title | TEXT | 笔记标题 |
| note | TEXT | 笔记内容 |
| created | INTEGER | 创建时间戳 |
| modified | INTEGER | 修改时间戳 |
| category_id | INTEGER | 分类ID（外键） |

### 分类表 (categories)

| 列名 | 类型 | 描述 |
|------|------|------|
| _id | INTEGER | 主键，自动递增 |
| name | TEXT | 分类名称 |

## 使用说明

1. **创建新笔记**：点击菜单中的"新建"按钮或长按列表项
2. **编辑笔记**：点击列表中的任意笔记项
3. **删除笔记**：长按笔记项，从上下文菜单中选择删除
4. **搜索笔记**：使用顶部的搜索框输入关键词
5. **切换主题**：在设置中选择明亮或暗黑主题

## 注意事项

- 本项目是一个教学示例，实际应用中应考虑：
  - 使用AsyncTask或其他异步机制处理数据库操作
  - 添加数据备份和恢复功能
  - 实现更丰富的文本编辑功能
  - 添加云同步支持

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

## 更多资源

- 官方解析参考：[Android Sample--NotePad解析](https://blog.csdn.net/llfjfz/article/details/67638499)
- Android ContentProvider官方文档：[Content providers | Android Developers](https://developer.android.com/guide/topics/providers/content-providers)
- SQLite数据库官方教程：[SQLite | Android Developers](https://developer.android.com/training/data-storage/sqlite)