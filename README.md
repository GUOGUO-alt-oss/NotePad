# NotePad 记事本应用

## 项目概述

这是一个基于 Android 平台开发的现代化记事本应用，采用经典的 Java 语言和 SQLite 数据库实现，结合 ContentProvider 架构提供安全、统一的数据访问接口。该应用旨在提供简洁、高效的笔记管理体验，支持多种实用功能，适合日常工作和生活使用。

## 功能特性

### 📋 基本功能

| 功能 | 描述 |
|------|------|
| ✅ 创建新笔记 | 快速创建新笔记，支持自动生成标题 |
| ✅ 查看笔记列表 | 直观显示所有笔记，包含标题、分类和修改时间 |
| ✅ 编辑笔记 | 提供带行线的编辑界面，支持自动保存 |
| ✅ 删除笔记 | 通过上下文菜单便捷删除笔记 |
| ✅ 时间排序 | 默认按修改时间降序排列，方便查看最新笔记 |
| ✅ 搜索功能 | 支持按标题和内容实时搜索，快速定位笔记 |
| ✅ 主题切换 | 支持明亮/暗黑主题切换，适应不同使用场景 |

### 🚀 扩展功能

| 功能 | 描述 |
|------|------|
| ✅ 笔记分类管理 | 支持 4 种预设分类：未分类、工作、个人、购物 |
| ✅ 分类记忆功能 | 再次编辑笔记时自动保留之前选择的分类 |
| ✅ 分类可视化 | 在列表中清晰显示每条笔记的分类 |
| ✅ Live Folder 支持 | 兼容传统 Android Live Folder 功能 |
| ✅ 内容共享与导出 | 支持将笔记导出为文本流，方便分享 |

## 技术栈

| 技术 | 说明 |
|------|------|
| **编程语言** | Java |
| **开发框架** | Android SDK |
| **数据存储** | SQLite 数据库 |
| **数据访问** | ContentProvider 架构 |
| **UI 组件** | ListView, SimpleCursorAdapter, Spinner, SearchView |
| **设计模式** | 内容提供者模式、MVC 架构 |
| **构建工具** | Gradle |

## 项目结构

```
app/
├── src/main/
│   ├── AndroidManifest.xml       # 应用清单文件，定义组件和权限
│   ├── java/com/example/android/notepad/  # 源代码目录
│   │   ├── NotePad.java          # 常量定义和数据库契约类
│   │   ├── NotePadProvider.java  # 内容提供者实现，处理所有数据操作
│   │   ├── NotesList.java        # 笔记列表主界面，包含搜索和主题切换
│   │   ├── NoteEditor.java       # 笔记编辑界面，包含分类选择功能
│   │   ├── TitleEditor.java      # 标题编辑对话框
│   │   └── NotesLiveFolder.java  # Live Folder 支持类
│   └── res/                      # 资源文件目录
│       ├── drawable/             # 图片资源（图标等）
│       ├── layout/               # 布局文件
│       │   ├── activity_main.xml       # 主界面布局
│       │   ├── note_editor.xml         # 笔记编辑布局
│       │   ├── noteslist_item.xml      # 笔记列表项布局（含分类显示）
│       │   └── title_editor.xml        # 标题编辑布局
│       ├── menu/                 # 菜单资源
│       │   ├── editor_options_menu.xml  # 编辑界面菜单
│       │   ├── list_context_menu.xml     # 列表上下文菜单
│       │   └── list_options_menu.xml      # 列表选项菜单
│       └── values/               # 字符串、颜色、样式配置
│           ├── colors.xml      # 颜色定义
│           ├── strings.xml     # 字符串资源（含分类列表）
│           └── styles.xml      # 样式定义（含主题）
└── build.gradle                  # 模块构建配置
```

## 核心功能实现

### 1. 笔记分类系统

#### 设计思路
采用外键关联方式，将分类信息存储在独立的 `categories` 表中，笔记表通过 `category_id` 字段关联分类。

#### 实现细节

**数据模型**
```java
// 笔记表契约
public static final class Notes implements BaseColumns {
    public static final String TABLE_NAME = "notes";
    // ... 其他字段
    public static final String COLUMN_NAME_CATEGORY_ID = "category_id"; // 分类外键
}

// 分类表契约
public static final class Categories implements BaseColumns {
    public static final String TABLE_NAME = "categories";
    public static final String COLUMN_NAME_NAME = "name"; // 分类名称
}
```

**UI 实现**
- 在 `NoteEditor.java` 中添加 `Spinner` 组件实现分类选择
- 在 `NotesList.java` 中使用 `ViewBinder` 将分类 ID 转换为分类名称
- 列表项布局中添加分类显示 `TextView`

**数据操作**
- 在 `NotePadProvider.java` 中实现分类相关的数据库操作
- 支持分类的增删改查
- 确保数据一致性

### 2. 智能搜索功能

#### 设计思路
采用 SQLite 的 `LIKE` 语句实现模糊搜索，结合 `SimpleCursorAdapter` 实现实时数据更新。

#### 实现细节
- 使用 `SearchView` 组件实现搜索界面
- 监听搜索文本变化，实时执行查询
- 支持按标题和内容同时搜索
- 搜索结果即时显示在列表中

### 3. 主题切换机制

#### 设计思路
使用 `SharedPreferences` 保存主题偏好，在 Activity 启动时动态设置主题。

#### 实现细节
- 定义明亮和暗黑两种主题样式
- 使用 `SharedPreferences` 持久化主题设置
- 在 `onCreate()` 方法中动态加载主题
- 支持运行时切换主题

## 数据库设计

### 🔍 数据库结构

#### 笔记表 (notes)

| 列名 | 类型 | 描述 | 约束 |
|------|------|------|------|
| _id | INTEGER | 主键，自动递增 | PRIMARY KEY AUTOINCREMENT |
| title | TEXT | 笔记标题 | NOT NULL |
| note | TEXT | 笔记内容 | NOT NULL |
| created | INTEGER | 创建时间戳 | NOT NULL |
| modified | INTEGER | 修改时间戳 | NOT NULL |
| category_id | INTEGER | 分类 ID（外键） | REFERENCES categories(_id) |

#### 分类表 (categories)

| 列名 | 类型 | 描述 | 约束 |
|------|------|------|------|
| _id | INTEGER | 主键，自动递增 | PRIMARY KEY AUTOINCREMENT |
| name | TEXT | 分类名称 | NOT NULL UNIQUE |

### 📊 数据关系

```
categories
└─── 1:N ─── notes
    │
    ├─ category_id = 0 → 未分类
    ├─ category_id = 1 → 工作
    ├─ category_id = 2 → 个人
    └─ category_id = 3 → 购物
```

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



## 使用指南

### 🚀 快速开始

1. **创建新笔记**
   - 点击菜单中的"新建"按钮
   - 或长按列表空白处
   - 输入内容并选择分类
   - 点击返回键自动保存

2. **编辑现有笔记**
   - 点击列表中的任意笔记
   - 修改内容
   - 可重新选择分类
   - 点击返回键自动保存

3. **删除笔记**
   - 长按要删除的笔记
   - 从上下文菜单中选择"删除"
   - 确认删除

4. **搜索笔记**
   - 点击顶部搜索框
   - 输入关键词
   - 查看搜索结果

5. **切换主题**
   - 点击菜单中的"明亮主题"或"暗黑主题"
   - 应用自动重启以应用新主题

### 💡 使用技巧

- **快速生成标题**：创建笔记时，系统会自动从内容中提取前 30 个字符作为标题
- **分类管理**：为每条笔记选择合适的分类，便于后续查找
- **搜索快捷键**：点击搜索框后直接输入关键词，无需额外操作
- **主题适应**：根据环境光线选择合适的主题，保护眼睛

## 技术实现亮点

### 🔧 核心技术

1. **ContentProvider 架构**
   - 提供统一的数据访问接口
   - 支持数据共享和权限控制
   - 实现数据与 UI 分离

2. **SQLite 数据库优化**
   - 高效的本地数据存储
   - 支持事务处理
   - 优化的查询语句

3. **MVC 设计模式**
   - 清晰的代码结构
   - 便于维护和扩展
   - 支持单元测试

4. **响应式 UI 设计**
   - 适配不同屏幕尺寸
   - 流畅的用户体验
   - 直观的操作界面

### 📈 性能优化

- 采用延迟加载机制，提高列表加载速度
- 优化数据库查询，减少响应时间
- 合理使用内存，避免内存泄漏
- 支持后台数据操作

## 如何运行项目

### 📋 前置要求

- Android Studio 3.0+（推荐最新版本）
- JDK 8 或更高版本
- Android SDK 21+（Android 5.0 Lollipop 及以上）
- 可用的 Android 设备或模拟器

### 🚀 运行步骤

1. **克隆项目**
   ```bash
   git clone https://github.com/GUOGUO-alt-oss/NotePad.git
   ```

2. **打开项目**
   - 打开 Android Studio
   - 选择"Open an existing Android Studio project"
   - 导航到项目目录并选择

3. **同步项目**
   - 等待 Gradle 同步完成
   - 确保没有依赖错误

4. **运行应用**
   - 连接 Android 设备或启动模拟器
   - 点击 Run 按钮（绿色三角形）
   - 等待应用安装并启动

## 项目扩展建议

### 📌 功能扩展

1. **云同步功能**
   - 添加 Firebase 或其他云服务支持
   - 实现笔记的云端备份和同步

2. **富文本编辑**
   - 支持加粗、斜体、下划线等格式
   - 添加插入图片、链接等功能

3. **标签系统**
   - 支持为笔记添加多个标签
   - 实现标签云功能

4. **数据统计**
   - 添加笔记数量、分类分布统计
   - 提供可视化图表

### 🔧 技术优化

1. **迁移到 Kotlin**
   - 利用 Kotlin 的现代特性
   - 提高开发效率和代码质量

2. **使用 Jetpack 组件**
   - 采用 Room 替代传统 SQLite 操作
   - 使用 ViewModel 和 LiveData 实现数据绑定
   - 采用 RecyclerView 替代 ListView

3. **添加单元测试**
   - 为核心功能编写单元测试
   - 提高代码质量和稳定性

4. **持续集成**
   - 添加 CI/CD 流程
   - 实现自动构建和测试

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

## 贡献指南

欢迎大家参与项目贡献！您可以通过以下方式贡献：

1. **提交 Issue**：报告 bug 或提出新功能建议
2. **提交 Pull Request**：修复 bug 或实现新功能
3. **改进文档**：完善 README.md 或其他文档
4. **提供反馈**：分享使用体验和改进建议

### 贡献流程

1. Fork 本仓库
2. 创建特性分支：`git checkout -b feature/AmazingFeature`
3. 提交更改：`git commit -m 'Add some AmazingFeature'`
4. 推送到分支：`git push origin feature/AmazingFeature`
5. 提交 Pull Request

## 联系方式

- **项目地址**：[https://github.com/GUOGUO-alt-oss/NotePad](https://github.com/GUOGUO-alt-oss/NotePad)
- **Issue 提交**：[https://github.com/GUOGUO-alt-oss/NotePad/issues](https://github.com/GUOGUO-alt-oss/NotePad/issues)

## 更新日志

### v1.0.0 (2025-12-07)

- ✅ 实现完整的笔记管理功能
- ✅ 添加分类管理系统
- ✅ 支持主题切换
- ✅ 实现搜索功能
- ✅ 优化 UI 设计
- ✅ 完善文档

## 致谢

感谢 Android 开源社区提供的优秀框架和资源！

---

**NotePad 记事本应用** - 简洁、高效、实用的笔记管理工具

*Made with ❤️ for Android developers and users*
