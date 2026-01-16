# NotePad 记事本应用

## 〇、项目总体介绍

这是一个基于Android平台的记事本应用，是官方早期的数据库操作基本教程项目。该应用演示了如何使用Android的ContentProvider和SQLite数据库来管理和存储用户笔记，并在此基础上进行了功能扩展，添加了时间戳显示、搜索功能、主题切换和笔记分类等功能。

## 项目概述

这是一个基于 Android 平台开发的现代化记事本应用，采用经典的 Java 语言和 SQLite 数据库实现，结合 ContentProvider 架构提供安全、统一的数据访问接口。该应用旨在提供简洁、高效的笔记管理体验，支持多种实用功能，适合日常工作和生活使用。

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

### 🔧 技术栈

| 技术 | 说明 |
|------|------|
| **编程语言** | Java |
| **开发框架** | Android SDK |
| **数据存储** | SQLite 数据库 |
| **数据访问** | ContentProvider 架构 |
| **UI 组件** | ListView, SimpleCursorAdapter, Spinner, SearchView |
| **设计模式** | 内容提供者模式、MVC 架构 |
| **构建工具** | Gradle |

## 一、初始应用的功能

初始应用实现了记事本的基本功能，包括：

- 新建笔记和编辑笔记
- 编辑标题
- 笔记列表展示

## 二、拓展基本功能

<<<<<<< HEAD
### （一）、笔记条目增加时间戳显示

#### 1. 功能要求

在主界面的笔记列表中，每个笔记条目除了显示标题外，还需要清晰地展示其最后修改时间。当笔记被创建或修改后，此时间戳应自动更新，并以用户友好的格式呈现。

#### 2. 实现思路

时间戳显示功能的实现遵循了完整的数据处理流程：
1. **数据存储**：在数据库中保存笔记的修改时间戳
2. **数据更新**：当笔记被创建或修改时，自动更新时间戳
3. **数据查询**：从数据库中查询笔记的时间戳数据
4. **UI绑定**：将时间戳数据绑定到UI组件，并格式化为用户友好的显示格式

#### 3. 实现步骤

##### （1）数据库设计与数据持久化

1. **数据库表结构设计**：
   - 在 `NotePad.java` 中定义了 `COLUMN_NAME_MODIFICATION_DATE` 常量，用于标识笔记的修改时间字段
   - 该字段类型为 `INTEGER`，用于存储自1970年以来的毫秒数，确保时间精度和存储效率

2. **数据持久化实现**：
   - **创建笔记时**：在 `NotePadProvider.java` 的 `insert()` 方法中，为新创建的笔记自动添加当前时间戳
   - **修改笔记时**：在 `NoteEditor.java` 的 `updateNote()` 方法中，每当笔记内容或标题被更新时，自动更新时间戳

   ```java
   // 在 NoteEditor.java 的 updateNote() 方法中
   ContentValues values = new ContentValues();
   // 核心：每次更新笔记时，将修改时间设为当前系统时间
   values.put(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, System.currentTimeMillis());
   // ... 其他字段的 put 操作 ...
   getContentResolver().update(mUri, values, null, null);
   ```

##### （2）布局文件修改

为了在笔记列表中显示时间戳，我们修改了 `noteslist_item.xml` 布局文件：

```xml
<!-- 在 noteslist_item.xml 中 -->
<TextView
    android:id="@+id/note_time"              <!-- 时间戳显示组件ID -->
    android:layout_width="wrap_content"      <!-- 宽度自适应内容 -->
    android:layout_height="wrap_content"     <!-- 高度自适应内容 -->
    android:layout_below="@android:id/text1" <!-- 位于标题下方 -->
    android:layout_alignParentStart="true"   <!-- 左对齐 -->
    android:layout_marginTop="4dp"           <!-- 与标题间距4dp -->
    android:textAppearance="?android:attr/textAppearanceSmall" <!-- 小字体显示 -->
    android:textColor="?android:attr/textColorSecondary"        <!-- 次要文本颜色 -->
    tools:text="2024-01-01 12:00"            <!-- 设计时预览文本 -->
/>
```

##### （3）数据查询实现

为了从数据库中获取时间戳数据，我们需要修改 `NotesList.java` 中的查询投影：

```java
// 在 NotesList.java 中
private static final String[] PROJECTION = new String[] {
        NotePad.Notes._ID,                          // 0: 笔记ID
        NotePad.Notes.COLUMN_NAME_TITLE,            // 1: 笔记标题
        NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 2: 添加的时间戳字段
        NotePad.Notes.COLUMN_NAME_CATEGORY_ID       // 3: 分类ID
};
```

##### （4）UI数据绑定与格式化

在 `NotesList.java` 中，我们使用 `SimpleCursorAdapter` 将数据绑定到 `ListView`，并通过 `ViewBinder` 来格式化时间戳的显示：

```java
// 在 NotesList.java 中
adapter.setViewBinder(new SimpleCursorAdapter.ViewBinder() {
    @Override
    public boolean setViewValue(View view, Cursor cursor, int columnIndex) {
        // 检查当前处理的是时间戳字段
        if (view.getId() == R.id.note_time) {
            // 1. 从Cursor中获取原始时间戳（毫秒数）
            long timeInMillis = cursor.getLong(columnIndex);
            
            // 2. 调用格式化方法，将毫秒数转换为用户友好的格式
            String formattedTime = formatTime(timeInMillis);
            
            // 3. 将格式化后的时间设置到TextView
            ((TextView) view).setText(formattedTime);
            
            // 4. 返回true表示已处理该字段的绑定
            return true;
        }
        // 返回false表示未处理，使用默认绑定方式
        return false;
    }
});

// 时间格式化方法
private String formatTime(long timeInMillis) {
    // 创建SimpleDateFormat对象，指定时间格式为"月-日 时:分"
    SimpleDateFormat sdf = new SimpleDateFormat("MM-dd HH:mm", Locale.getDefault());
    // 将毫秒数转换为Date对象，再格式化为字符串
    return sdf.format(new Date(timeInMillis));
}
```

#### 4. 实现关键点说明

1. **时间精度**：使用 `System.currentTimeMillis()` 获取毫秒级时间戳，确保时间精度
2. **自动更新**：在笔记创建和修改时自动更新时间戳，确保时间准确性
3. **用户友好显示**：将原始毫秒数格式化为"MM-dd HH:mm"格式，提高可读性
4. **高效查询**：通过投影映射只查询需要的字段，提高查询效率
5. **灵活绑定**：使用 `ViewBinder` 自定义字段绑定逻辑，提高UI显示的灵活性

#### 5. 实现流程图

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   创建/修改笔记         │     │   数据库更新时间戳       │     │   笔记列表查询数据       │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│ 调用updateNote()方法    │────▶│ 保存System.currentTimeMillis() │────▶│  查询包含时间戳的数据  │
└─────────────────────────┘     └─────────────────────────┘     └─────────┬───────────────┘
                                                                         │
                                                                         ▼
                                                             ┌─────────────────────────┐
                                                             │  使用ViewBinder格式化时间 │
                                                             └─────────┬───────────────┘
                                                                         │
                                                                         ▼
                                                             ┌─────────────────────────┐
                                                             │  在ListView中显示格式化时间 │
                                                             └─────────────────────────┘
```

### （二）、笔记查询功能

#### 1. 功能要求

为用户提供一个便捷的笔记搜索功能。用户可以通过点击Toolbar上的搜索图标进入搜索模式，在搜索框中输入关键词，实时搜索匹配的笔记，支持按标题和内容搜索。

#### 2. 实现思路

笔记查询功能的实现遵循了完整的搜索流程：
1. **UI组件集成**：在Toolbar中添加搜索图标和搜索框
2. **用户输入监听**：监听用户在搜索框中的输入变化
3. **动态数据查询**：根据用户输入的关键词，动态构建查询条件
4. **实时结果更新**：将查询结果实时更新到ListView中

#### 3. 实现步骤

##### （1）UI组件集成

1. **菜单资源定义**：在 `list_options_menu.xml` 中添加搜索菜单项

   ```xml
   <!-- 在 list_options_menu.xml 中 -->
   <item
       android:id="@+id/menu_search"                  <!-- 搜索菜单项ID -->
       android:title="搜索"                           <!-- 菜单项标题 -->
       android:icon="@android:drawable/ic_menu_search" <!-- 搜索图标 -->
       app:showAsAction="ifRoom|collapseActionView"  <!-- 在Toolbar上显示，支持折叠 -->
       app:actionViewClass="android.widget.SearchView" <!-- 使用SearchView作为动作视图 -->
   />
   ```

2. **菜单初始化与配置**：在 `NotesList.java` 的 `onCreateOptionsMenu()` 方法中初始化搜索菜单

##### （2）用户输入监听与搜索实现

1. **搜索视图配置**：
   - 获取搜索菜单项的 `SearchView` 对象
   - 设置 `OnQueryTextListener` 监听用户输入变化

2. **搜索逻辑实现**：
   - **实时搜索**：监听 `onQueryTextChange` 事件，当用户输入变化时触发搜索
   - **搜索方法**：实现 `searchNotes()` 方法，根据搜索关键词动态查询数据库

   ```java
   // 在 NotesList.java 的 onCreateOptionsMenu() 方法中
   MenuItem searchItem = menu.findItem(R.id.menu_search);
   if (searchItem != null) {
       // 获取SearchView对象
       SearchView searchView = (SearchView) searchItem.getActionView();
       if (searchView != null) {
           // 设置查询文本监听器
           searchView.setOnQueryTextListener(new SearchView.OnQueryTextListener() {
               @Override
               public boolean onQueryTextSubmit(String query) {
                   // 搜索提交时的处理，此处返回false表示不消耗事件
                   return false;
               }

               @Override
               public boolean onQueryTextChange(String newText) {
                   // 搜索文本变化时的处理，触发实时搜索
                   searchNotes(newText);
                   // 返回true表示已处理该事件
                   return true;
               }
           });
       }
   }
   ```

3. **数据库查询实现**：
   - **查询条件构建**：根据搜索关键词构建LIKE查询条件
   - **动态适配器更新**：使用查询结果更新ListView的适配器

   ```java
   // 搜索笔记的核心方法
   private void searchNotes(String query) {
       Cursor cursor;
       
       // 如果搜索关键词为空，查询所有笔记
       if (query.isEmpty()) {
           cursor = managedQuery(
                   getIntent().getData(),      // 查询URI
                   PROJECTION,                 // 查询字段投影
                   null,                       // 无查询条件
                   null,                       // 无查询参数
                   NotePad.Notes.DEFAULT_SORT_ORDER // 默认排序
           );
       } else {
           // 如果搜索关键词不为空，构建模糊查询条件
           cursor = managedQuery(
                   getIntent().getData(),      // 查询URI
                   PROJECTION,                 // 查询字段投影
                   // 查询条件：标题或内容包含关键词
                   NotePad.Notes.COLUMN_NAME_TITLE + " LIKE ? OR " + NotePad.Notes.COLUMN_NAME_NOTE + " LIKE ?",
                   // 查询参数：关键词前后加%,支持模糊匹配
                   new String[]{"%" + query + "%", "%" + query + "%"},
                   NotePad.Notes.DEFAULT_SORT_ORDER // 默认排序
           );
       }

       // 创建新的适配器，将查询结果绑定到ListView
       SimpleCursorAdapter adapter = new SimpleCursorAdapter(
               this,
               R.layout.noteslist_item,      // 列表项布局
               cursor,                       // 查询结果Cursor
               new String[]{                  // 要绑定的字段
                   NotePad.Notes.COLUMN_NAME_TITLE, 
                   NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,
                   NotePad.Notes.COLUMN_NAME_CATEGORY_ID
               },
               new int[]{                     // 对应的UI组件ID
                   android.R.id.text1, 
                   R.id.note_time,
                   R.id.note_category
               }
       );
       
       // 设置ViewBinder处理时间和分类显示
       adapter.setViewBinder(new SimpleCursorAdapter.ViewBinder() {
           @Override
           public boolean setViewValue(View view, Cursor cursor, int columnIndex) {
               // 时间戳处理
               if (view.getId() == R.id.note_time) {
                   long timeInMillis = cursor.getLong(columnIndex);
                   String formattedTime = formatTime(timeInMillis);
                   ((TextView) view).setText(formattedTime);
                   return true;
               }
               // 分类处理
               else if (view.getId() == R.id.note_category) {
                   long categoryId = cursor.getLong(columnIndex);
                   final String[] categories = getResources().getStringArray(R.array.category_array);
                   String categoryName = categories[(int) categoryId];
                   ((TextView) view).setText(categoryName);
                   return true;
               }
               return false;
           }
       });
       
       // 更新ListView的适配器，显示搜索结果
       setListAdapter(adapter);
   }
   ```

#### 4. 实现关键点说明

1. **实时搜索机制**：通过监听 `onQueryTextChange` 事件实现实时搜索，提高用户体验
2. **模糊查询实现**：使用 `LIKE` 关键字和 `%` 通配符实现模糊匹配
3. **多字段搜索**：支持同时按标题和内容搜索，提高搜索覆盖率
4. **空查询处理**：当搜索关键词为空时，显示所有笔记，提高搜索功能的鲁棒性
5. **高效查询**：通过投影映射只查询需要的字段，提高查询效率
6. **动态适配器更新**：根据查询结果动态创建和更新适配器，确保UI显示与数据一致

#### 5. 实现流程图

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   用户点击搜索图标       │     │   显示搜索框             │     │   用户输入搜索关键词     │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   进入搜索模式         │────▶│   监听搜索输入变化       │────▶│   构建模糊查询条件       │
└─────────────────────────┘     └─────────────────────────┘     └─────────┬───────────────┘
                                                                         │
                                                                         ▼
                                                             ┌─────────────────────────┐
                                                             │   执行数据库查询         │
                                                             └─────────┬───────────────┘
                                                                         │
                                                                         ▼
                                                             ┌─────────────────────────┐
                                                             │   更新ListView适配器     │
                                                             └─────────┬───────────────┘
                                                                         │
                                                                         ▼
                                                             ┌─────────────────────────┐
                                                             │   显示搜索结果           │
                                                             └─────────────────────────┘
```

## 三、附加功能

### （一）、主题切换支持

#### 1. 功能要求

为用户提供主题切换功能，支持亮色主题和暗色主题的切换，并保存用户的主题偏好，以便下次启动应用时自动应用上次选择的主题。

#### 2. 实现思路

主题切换功能的实现遵循了完整的主题管理流程：
1. **主题资源定义**：在 `styles.xml` 中定义不同主题的样式资源
2. **主题偏好存储**：使用 `SharedPreferences` 存储用户选择的主题偏好
3. **主题应用**：在 `Activity` 启动时根据偏好设置应用主题
4. **主题切换**：提供菜单选项允许用户切换主题，并实时更新应用界面

#### 3. 实现步骤

##### （1）主题资源定义

1. **主题样式定义**：
   - 在 `styles.xml` 中定义了基础主题和亮色/暗色主题变体
   - 基础主题继承自 `Theme.AppCompat.Light.DarkActionBar`
   - 亮色主题继承自基础主题，使用浅色背景和深色文字
   - 暗色主题继承自 `Theme.AppCompat`，使用深色背景和浅色文字

   ```xml
   <!-- 在 styles.xml 中 -->
   <!-- 基础主题，继承自AppCompat Light主题 -->
   <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
       <!-- 主题公共属性定义 -->
       <item name="colorPrimary">@color/colorPrimary</item>
       <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
       <item name="colorAccent">@color/colorAccent</item>
   </style>

   <!-- 亮色主题，继承自基础主题 -->
   <style name="AppTheme.Light" parent="AppTheme">
       <!-- 亮色主题特有属性 -->
       <item name="android:windowBackground">@android:color/white</item>
       <item name="android:textColor">@android:color/black</item>
       <item name="android:actionBarStyle">@style/Theme.AppCompat.Light.DarkActionBar</item>
   </style>

   <!-- 暗色主题，继承自AppCompat主题 -->
   <style name="AppTheme.Dark" parent="Theme.AppCompat">
       <!-- 暗色主题特有属性 -->
       <item name="android:windowBackground">@android:color/black</item>
       <item name="android:textColor">@android:color/white</item>
       <item name="android:actionBarStyle">@style/Theme.AppCompat.Dark.ActionBar</item>
       <item name="android:windowActionBarOverlay">false</item>
   </style>
   ```

2. **菜单资源定义**：
   - 在 `list_options_menu.xml` 中添加主题切换菜单项
   - 包括亮色主题和暗色主题两个选项

   ```xml
   <!-- 在 list_options_menu.xml 中 -->
   <item
       android:id="@+id/menu_light"
       android:title="亮色主题"
       android:icon="@android:drawable/ic_menu_daynight"
       app:showAsAction="never" />
   <item
       android:id="@+id/menu_dark"
       android:title="暗色主题"
       android:icon="@android:drawable/ic_menu_night_mode"
       app:showAsAction="never" />
   ```

##### （2）主题偏好存储与应用

1. **偏好存储常量定义**：
   - 定义了 `SharedPreferences` 的文件名、主题偏好键名和主题标识常量
   - 确保主题相关的常量集中管理，便于维护

   ```java
   // 在 NotesList.java 中
   private static final String PREFS_NAME = "app_settings";        // SharedPreferences文件名
   private static final String KEY_THEME = "theme";               // 主题偏好键名
   private static final String THEME_LIGHT = "light";             // 亮色主题标识
   private static final String THEME_DARK = "dark";               // 暗色主题标识
   ```

2. **主题应用实现**：
   - 在 `Activity` 的 `onCreate()` 方法中，在调用 `super.onCreate()` 之前设置主题
   - 从 `SharedPreferences` 中获取用户保存的主题偏好
   - 根据偏好设置对应的主题样式
   - 确保主题在 `Activity` 初始化的早期阶段被设置，以确保主题样式正确应用到所有UI组件

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {
       // 1. 获取SharedPreferences实例
       SharedPreferences prefs = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
       // 2. 从SharedPreferences中获取主题偏好，默认使用亮色主题
       String theme = prefs.getString(KEY_THEME, THEME_LIGHT);
       
       // 3. 根据主题偏好设置应用主题
       if (THEME_DARK.equals(theme)) {
           setTheme(R.style.AppTheme_Dark);    // 设置暗色主题
       } else {
           setTheme(R.style.AppTheme_Light);   // 设置亮色主题
       }
       
       // 4. 调用super.onCreate()和setContentView()，此时主题已生效
       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_main);
       // ...
   }
   ```

##### （3）主题切换实现

1. **菜单选项处理**：
   - 在 `onOptionsItemSelected()` 方法中处理主题切换菜单项的点击事件
   - 更新 `SharedPreferences` 中的主题偏好
   - 调用 `recreate()` 方法重新创建 `Activity`，以应用新主题
   - 确保主题切换操作的原子性，避免数据不一致

   ```java
   // 主题切换菜单处理
   @Override
   public boolean onOptionsItemSelected(MenuItem item) {
       // 处理亮色主题选项
       if (item.getItemId() == R.id.menu_light) {
           // 1. 获取SharedPreferences编辑器
           SharedPreferences preferences = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
           SharedPreferences.Editor editor = preferences.edit();
           
           // 2. 保存亮色主题偏好
           editor.putString(KEY_THEME, THEME_LIGHT);
           // 3. 提交偏好更改
           editor.apply();
           
           // 4. 重新创建Activity以应用新主题
           recreate();
           return true;
       } 
       // 处理暗色主题选项
       else if (item.getItemId() == R.id.menu_dark) {
           // 1. 获取SharedPreferences编辑器
           SharedPreferences preferences = getSharedPreferences(PREFS_NAME, MODE_PRIVATE);
           SharedPreferences.Editor editor = preferences.edit();
           
           // 2. 保存暗色主题偏好
           editor.putString(KEY_THEME, THEME_DARK);
           // 3. 提交偏好更改
           editor.apply();
           
           // 4. 重新创建Activity以应用新主题
           recreate();
           return true;
       }
       return super.onOptionsItemSelected(item);
   }
   ```

##### （4）主题一致性保证

1. **所有Activity共享主题**：
   - 在所有 `Activity` 的 `onCreate()` 方法中应用相同的主题设置逻辑
   - 确保应用内所有界面的主题一致
   - 可以考虑创建一个 `BaseActivity` 类，在其中实现主题设置逻辑，然后让所有 `Activity` 继承自该类

2. **主题偏好的全局访问**：
   - 考虑将主题偏好的访问封装为工具类或单例类
   - 便于在应用的任何地方访问和修改主题偏好

#### 4. 实现关键点说明

1. **主题设置时机**：
   - 必须在 `super.onCreate()` 之前设置主题，否则主题不会生效
   - 这是因为 `super.onCreate()` 会初始化 `Window` 对象并应用主题样式

2. **SharedPreferences使用**：
   - 使用 `SharedPreferences` 持久化存储用户主题偏好
   - 确保下次启动应用时自动应用上次选择的主题
   - 使用 `apply()` 方法异步提交偏好更改，避免阻塞UI线程

3. **Activity重建**：
   - 调用 `recreate()` 方法重新创建 `Activity`，以实时应用新主题
   - 该方法会销毁当前 `Activity` 实例并创建一个新的实例，同时保存和恢复 `Activity` 的状态

4. **主题样式继承**：
   - 通过样式继承减少重复代码，提高主题管理的可维护性
   - 基础主题定义公共属性，变体主题定义特有属性

5. **默认主题处理**：
   - 设置默认主题，确保首次启动应用时的正常显示
   - 本项目默认使用亮色主题

6. **主题一致性**：
   - 所有 `Activity` 都应遵循相同的主题设置逻辑，确保应用内主题一致
   - 考虑创建 `BaseActivity` 类统一处理主题设置

#### 5. 实现流程图

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   应用启动             │     │   读取SharedPreferences │     │   获取主题偏好         │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   根据主题偏好设置主题   │────▶│   调用super.onCreate()   │────▶│   加载布局资源         │
└─────────────────────────┘     └─────────┬───────────────┘     └─────────────────────────┘
                                         │
                                         ▼
                                 ┌─────────────────────────┐
                                 │   显示应用界面         │
                                 └─────────┬───────────────┘
                                           │
                                           ▼
                                 ┌─────────────────────────┐     ┌─────────────────────────┐
                                 │   用户选择主题菜单     │     │   更新SharedPreferences │
                                 └─────────┬───────────────┘     └─────────┬───────────────┘
                                           │                               │
                                           ▼                               ▼
                                 ┌─────────────────────────┐     ┌─────────────────────────┐
                                 │   调用recreate()方法    │────▶│   重新创建Activity     │
                                 └─────────────────────────┘     └─────────────────────────┘
                                           │                               │
                                           ▼                               ▼
                                 ┌─────────────────────────┐     ┌─────────────────────────┐
                                 │   应用新主题           │────▶│   显示更新后的界面       │
                                 └─────────────────────────┘     └─────────────────────────┘
```

### （二）、笔记分类功能

#### 1. 功能要求

为了解决笔记数量增多后难以管理的问题，项目引入了笔记分类功能。用户可以将笔记归类到不同的分类中，实现笔记的结构化管理，具体包括：
- 为笔记添加分类属性
- 在笔记编辑界面选择笔记分类
- 在笔记列表界面显示笔记所属分类
- 支持多种预设分类
- 分类数据的持久化存储

#### 2. 实现思路

笔记分类功能的实现遵循了完整的数据管理和UI交互流程：
1. **数据模型设计**：设计分类数据模型和笔记-分类关联关系
2. **数据库扩展**：扩展数据库表结构，添加分类表和笔记分类关联字段
3. **ContentProvider升级**：更新ContentProvider以支持分类数据的CRUD操作
4. **UI组件设计**：在笔记编辑界面添加分类选择组件，在笔记列表界面添加分类显示组件
5. **数据绑定与显示**：实现分类数据的绑定和显示逻辑

#### 3. 实现步骤

##### （1）数据模型设计

1. **分类数据模型**：
   - 在 `NotePad.java` 中定义了 `Categories` 类，用于表示分类数据模型
   - 实现了 `BaseColumns` 接口，继承了 `_ID` 字段，确保每个分类都有唯一标识
   - 定义了分类表名 `TABLE_NAME` 和分类名称字段 `COLUMN_NAME_NAME`

   ```java
   // 在 NotePad.java 中
   public static final class Categories implements BaseColumns {
       public static final String TABLE_NAME = "categories";  // 分类表名
       public static final String COLUMN_NAME_NAME = "name"; // 分类名称字段
   }
   ```

2. **笔记-分类关联**：
   - 在 `NotePad.Notes` 类中添加了 `COLUMN_NAME_CATEGORY_ID` 常量，用于表示笔记所属分类的ID
   - 该字段作为外键关联到 `Categories` 表的 `_ID` 字段，建立笔记和分类之间的一对多关系
   - 默认值为0，表示未分类

   ```java
   // 在 NotePad.Notes 类中
   public static final String COLUMN_NAME_CATEGORY_ID = "category_id"; // 笔记分类ID字段
   ```

##### （2）数据库表结构设计与实现

1. **分类表创建**：
   - 在 `NotePadProvider.java` 的 `DatabaseHelper.onCreate()` 方法中创建分类表
   - 表包含 `_ID` 和 `name` 两个字段，其中 `_ID` 是主键，自动递增
   - `name` 字段用于存储分类名称

2. **笔记表扩展**：
   - 在笔记表中添加 `category_id` 字段，用于关联分类
   - 字段类型为 `INTEGER`，允许为空，默认值为0

   ```java
   // 在 NotePadProvider.java 的 onCreate() 方法中
   // 创建分类表
   db.execSQL("CREATE TABLE " + NotePad.Categories.TABLE_NAME + " (" +
           NotePad.Categories._ID + " INTEGER PRIMARY KEY," +  // 分类ID，主键，自动递增
           NotePad.Categories.COLUMN_NAME_NAME + " TEXT" +     // 分类名称
           ");");

   // 创建笔记表，添加分类ID字段
   db.execSQL("CREATE TABLE " + NotePad.Notes.TABLE_NAME + " (" +
           NotePad.Notes._ID + " INTEGER PRIMARY KEY," +                  // 笔记ID
           NotePad.Notes.COLUMN_NAME_TITLE + " TEXT," +                  // 笔记标题
           NotePad.Notes.COLUMN_NAME_NOTE + " TEXT," +                   // 笔记内容
           NotePad.Notes.COLUMN_NAME_CREATE_DATE + " INTEGER," +          // 创建时间
           NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE + " INTEGER," +    // 修改时间
           NotePad.Notes.COLUMN_NAME_CATEGORY_ID + " INTEGER DEFAULT 0" + // 分类ID，外键，默认值0
           ");");
   ```

##### （3）ContentProvider升级

1. **投影映射更新**：
   - 在 `NotePadProvider.java` 中更新了 `sNotesProjectionMap`，添加了 `category_id` 列的映射
   - 确保查询结果中包含分类ID字段，以便UI层使用

   ```java
   // 在 NotePadProvider.java 中
   // Maps "category_id" to "category_id"
   sNotesProjectionMap.put(NotePad.Notes.COLUMN_NAME_CATEGORY_ID, NotePad.Notes.COLUMN_NAME_CATEGORY_ID);
   ```

##### （4）分类数据初始化

1. **预设分类定义**：
   - 在 `res/values/strings.xml` 中定义了预设分类数组
   - 包含"未分类"、"工作"、"学习"、"生活"等常见分类

   ```xml
   <!-- 在 strings.xml 中 -->
   <string-array name="category_array">
       <item>未分类</item>
       <item>工作</item>
       <item>学习</item>
       <item>生活</item>
       <item>其他</item>
   </string-array>
   ```

2. **分类数据初始化**：
   - 在应用首次启动时，向分类表中插入预设分类数据
   - 确保分类数据的完整性和一致性

##### （5）UI组件设计与实现

1. **分类选择组件**：
   - 在 `note_editor.xml` 中添加了 `Spinner` 组件，用于选择笔记分类
   - 组件ID为 `category_spinner`，放置在笔记内容编辑区域上方

   ```xml
   <!-- 在 note_editor.xml 中 -->
   <Spinner
       android:id="@+id/category_spinner"
       android:layout_width="match_parent"
       android:layout_height="wrap_content"
       android:layout_below="@+id/title"
       android:layout_marginTop="8dp"
       android:entries="@array/category_array"
       android:prompt="@string/category_prompt" />
   ```

2. **分类显示组件**：
   - 在 `noteslist_item.xml` 中添加了 `TextView` 组件，用于显示笔记所属分类
   - 组件ID为 `note_category`，放置在笔记标题下方，与时间戳并排显示

   ```xml
   <!-- 在 noteslist_item.xml 中 -->
   <TextView
       android:id="@+id/note_category"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:layout_below="@android:id/text1"
       android:layout_alignParentEnd="true"
       android:layout_marginTop="4dp"
       android:textAppearance="?android:attr/textAppearanceSmall"
       android:textColor="?android:attr/textColorSecondary"
       tools:text="未分类" />
   ```

##### （6）分类选择与保存实现

1. **分类选择初始化**：
   - 在 `NoteEditor.java` 中初始化 `Spinner` 组件
   - 使用 `ArrayAdapter` 加载 `category_array` 字符串数组作为分类选项

   ```java
   // 在 NoteEditor.java 中
   // 添加成员变量
   private Spinner mCategorySpinner;

   @Override
   protected void onCreate(Bundle savedInstanceState) {
       // ...
       // 初始化分类选择Spinner
       mCategorySpinner = (Spinner) findViewById(R.id.category_spinner);
       // 创建ArrayAdapter，从resources中加载分类数据
       ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
               this,
               R.array.category_array,  // 分类数据数组资源
               android.R.layout.simple_spinner_item  // Spinner项布局
       );
       // 设置下拉列表布局
       adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
       // 设置适配器
       mCategorySpinner.setAdapter(adapter);
       // ...
   }
   ```

2. **分类数据加载**：
   - 在 `onResume()` 方法中，从数据库中获取当前笔记的分类ID
   - 根据分类ID设置 `Spinner` 的选中项，如果分类ID为空则默认选择"未分类"

   ```java
   // 在 onResume() 方法中加载分类
   int colCategoryIndex = mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CATEGORY_ID);
   if (!mCursor.isNull(colCategoryIndex)) {
       // 获取当前笔记的分类ID
       long categoryId = mCursor.getLong(colCategoryIndex);
       // 设置Spinner选中项（分类ID对应数组索引）
       mCategorySpinner.setSelection((int) categoryId);
   } else {
       // 默认选择第一个分类（未分类）
       mCategorySpinner.setSelection(0);
   }
   ```

3. **分类数据保存**：
   - 在 `onPause()` 方法中，获取 `Spinner` 当前选中的分类ID
   - 将分类ID保存到数据库中，更新笔记的分类信息

   ```java
   // 在 onPause() 方法中保存分类
   // 获取当前选中的分类ID（Spinner选中项位置即为分类ID）
   long categoryId = mCategorySpinner.getSelectedItemPosition();
   // 创建ContentValues对象保存分类数据
   ContentValues values = new ContentValues();
   values.put(NotePad.Notes.COLUMN_NAME_CATEGORY_ID, categoryId);
   // 更新数据库中的笔记分类
   getContentResolver().update(mUri, values, null, null);
   ```

##### （7）分类显示实现

1. **分类数据查询**：
   - 在 `NotesList.java` 的 `PROJECTION` 数组中添加 `COLUMN_NAME_CATEGORY_ID` 字段
   - 确保查询结果中包含分类ID数据

   ```java
   // 在 NotesList.java 中
   // 在PROJECTION数组中添加分类ID字段
   private static final String[] PROJECTION = new String[] {
           NotePad.Notes._ID,                          // 0: 笔记ID
           NotePad.Notes.COLUMN_NAME_TITLE,            // 1: 笔记标题
           NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 2: 修改时间
           NotePad.Notes.COLUMN_NAME_CATEGORY_ID       // 3: 分类ID
   };
   ```

2. **分类数据绑定与显示**：
   - 使用 `SimpleCursorAdapter` 将Cursor中的数据绑定到ListView的Item视图中
   - 通过 `ViewBinder` 自定义分类字段的绑定逻辑，将分类ID转换为分类名称

   ```java
   // 在 NotesList.java 中
   // 创建SimpleCursorAdapter
   SimpleCursorAdapter adapter = new SimpleCursorAdapter(
           this,
           R.layout.noteslist_item,      // 列表项布局
           cursor,                       // 查询结果Cursor
           new String[]{                  // 要绑定的字段
               NotePad.Notes.COLUMN_NAME_TITLE, 
               NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE,
               NotePad.Notes.COLUMN_NAME_CATEGORY_ID
           },
           new int[]{                     // 对应的UI组件ID
               android.R.id.text1,        // 笔记标题
               R.id.note_time,            // 最后修改时间
               R.id.note_category         // 笔记分类
           }
   );
   
   // 设置ViewBinder处理分类显示
   adapter.setViewBinder(new SimpleCursorAdapter.ViewBinder() {
       @Override
       public boolean setViewValue(View view, Cursor cursor, int columnIndex) {
           // 检查当前处理的是分类字段
           if (view.getId() == R.id.note_category) {
               // 1. 获取分类ID
               long categoryId = cursor.getLong(columnIndex);
               
               // 2. 从resources中获取分类名称数组
               final String[] categories = getResources().getStringArray(R.array.category_array);
               
               // 3. 将分类ID转换为分类名称（分类ID对应数组索引）
               String categoryName = categories[(int) categoryId];
               
               // 4. 设置到TextView显示
               ((TextView) view).setText(categoryName);
               
               // 5. 返回true表示已处理该字段的绑定
               return true;
           }
           // 返回false表示未处理，使用默认绑定方式
           return false;
       }
   });
   
   // 设置适配器到ListView
   setListAdapter(adapter);
   ```

#### 4. 实现关键点说明

1. **数据模型设计**：清晰地设计了分类数据模型和笔记-分类关联关系，确保数据结构的合理性和扩展性
2. **数据库扩展**：通过扩展数据库表结构，实现了分类数据的存储和笔记-分类关联，建立了一对多的关系模型
3. **ContentProvider升级**：更新了ContentProvider的投影映射，确保分类数据能够被正确查询和访问
4. **UI组件设计**：合理设计了分类选择和显示组件，提高了用户体验和操作便捷性
5. **数据绑定与显示**：使用ViewBinder自定义分类字段的绑定逻辑，实现了分类ID到分类名称的转换和显示
6. **数据持久化**：确保分类数据能够被正确保存和加载，实现了分类数据的持久化存储
7. **预设分类**：提供了预设分类数据，方便用户直接使用，提高了应用的易用性
8. **默认值处理**：为分类ID设置了默认值0，表示未分类，确保了数据的完整性和一致性

#### 5. 实现流程图

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   用户打开笔记编辑界面   │     │   加载当前笔记数据       │     │   初始化分类选择Spinner   │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   根据笔记分类ID设置Spinner选中项 │     │   用户选择笔记分类       │     │   保存笔记数据         │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   更新笔记分类ID到数据库  │     │   用户返回笔记列表       │     │   查询笔记数据         │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   加载笔记分类数据       │     │   绑定笔记数据到ListView  │     │   使用ViewBinder处理分类显示 │
└─────────┬───────────────┘     └─────────┬───────────────┘     └─────────┬───────────────┘
          │                               │                               │
          ▼                               ▼                               ▼
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   将分类ID转换为分类名称  │     │   设置分类名称到TextView   │     │   显示完整的笔记列表       │
└─────────────────────────┘     └─────────────────────────┘     └─────────────────────────┘
```

## 四、数据库设计

### 1. 笔记表 (notes)

| 列名 | 类型 | 描述 |
|------|------|------|
| _id | INTEGER | 主键，自动递增 |
| title | TEXT | 笔记标题 |
| note | TEXT | 笔记内容 |
| created | INTEGER | 创建时间戳 |
| modified | INTEGER | 修改时间戳 |
| category_id | INTEGER | 分类ID（外键） |

### 2. 分类表 (categories)

| 列名 | 类型 | 描述 |
|------|------|------|
| _id | INTEGER | 主键，自动递增 |
| name | TEXT | 分类名称 |

## 五、技术实现细节
=======
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
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0

### 1. 笔记分类系统

<<<<<<< HEAD
NotePad.java 定义了数据库表结构、列名、URI和MIME类型等常量，作为应用的核心数据契约。
=======
#### 设计思路
采用外键关联方式，将分类信息存储在独立的 `categories` 表中，笔记表通过 `category_id` 字段关联分类。
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0

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

<<<<<<< HEAD
NotePadProvider 实现了 ContentProvider 接口，处理笔记数据的增删改查操作，是应用的数据层核心。
=======
**数据操作**
- 在 `NotePadProvider.java` 中实现分类相关的数据库操作
- 支持分类的增删改查
- 确保数据一致性
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0

### 2. 智能搜索功能

<<<<<<< HEAD
#### 配置信息：
```xml
<provider
    android:name=".NotePadProvider"
    android:authorities="com.google.provider.NotePad"
    android:exported="true">
    <grant-uri-permission android:pathPattern=".*" />
</provider>
```

### 3. AndroidManifest.xml 组件配置

应用的组件配置定义了应用的结构和功能入口，包括：

#### 3.1 应用基础配置
```xml
<application
    android:icon="@drawable/app_notes"
    android:label="@string/app_name"
    android:theme="@style/AppTheme.Light">
    <!-- 组件配置 -->
</application>
```

#### 3.2 Activity 配置

| Activity 名称 | 功能描述 | 导出设置 | 主题设置 |
|---------------|----------|----------|----------|
| NotesList | 主列表界面，显示所有笔记 | exported=true | 默认主题 |
| NoteEditor | 笔记编辑界面 | exported=true | Theme.Holo.Light |
| TitleEditor | 标题编辑界面 | exported=true | Theme.Holo.Dialog |
| NotesLiveFolder | Live Folder支持 | exported=true | 默认主题 |

#### 3.3 Intent Filter 配置

每个Activity都配置了特定的Intent Filter，用于响应不同的操作请求：

- **NotesList**：响应VIEW、EDIT、PICK、GET_CONTENT等操作
- **NoteEditor**：响应VIEW、EDIT、INSERT、PASTE等操作
- **TitleEditor**：响应EDIT_TITLE操作
- **NotesLiveFolder**：响应CREATE_LIVE_FOLDER操作

### 4. 资源文件管理

应用使用了多种资源文件来支持其功能：

#### 4.1 布局文件
- `noteslist_item.xml`：笔记列表项布局
- `note_editor.xml`：笔记编辑界面布局
- `title_editor.xml`：标题编辑界面布局

#### 4.2 菜单文件
- `list_options_menu.xml`：列表界面选项菜单
- `editor_options_menu.xml`：编辑界面选项菜单
- `list_context_menu.xml`：列表界面上下文菜单

#### 4.3 样式和主题
- `styles.xml`：定义应用主题和样式
- `colors.xml`：定义应用颜色
- `strings.xml`：定义应用字符串资源

#### 4.4 图标资源
- 应用图标：`app_notes.png`
- 菜单项图标：`ic_menu_*.png`
- Live Folder图标：`live_folder_notes.png`

### 5. 数据流转流程

应用的数据流转遵循了ContentProvider模式，确保数据的安全性和一致性：

1. **UI层**：Activity通过ContentResolver发起数据操作请求
2. **ContentProvider层**：NotePadProvider处理数据请求，执行相应的数据库操作
3. **数据层**：SQLiteDatabase执行实际的数据库CRUD操作
4. **结果返回**：操作结果通过Cursor返回给UI层，更新界面显示

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│   UI层 (Activity)        │────▶│   ContentProvider层     │────▶│   数据层 (SQLiteDatabase) │
└─────────────────────────┘     └─────────────────────────┘     └─────────────────────────┘
        ▲                                 ▲                                 ▲
        │                                 │                                 │
        │                                 │                                 │
        └─────────────────────────────────┼─────────────────────────────────┘
                                          │
                                          │
                                          ▼
                                 ┌─────────────────────────┐
                                 │   数据操作结果         │
                                 └─────────────────────────┘
```

## 六、主要功能模块详细说明

### 6.1 笔记列表界面 (NotesList.java)

NotesList 是应用的主界面，显示所有笔记的列表，支持上下文菜单操作。
=======
#### 设计思路
采用 SQLite 的 `LIKE` 语句实现模糊搜索，结合 `SimpleCursorAdapter` 实现实时数据更新。

#### 实现细节
- 使用 `SearchView` 组件实现搜索界面
- 监听搜索文本变化，实时执行查询
- 支持按标题和内容同时搜索
- 搜索结果即时显示在列表中
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0

### 3. 主题切换机制

<<<<<<< HEAD
### 6.2 笔记编辑器 (NoteEditor.java)

NoteEditor 提供笔记内容的创建和编辑功能，支持保存和撤销操作。
=======
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
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0

关键特性：
- 支持分类选择
- 自动保存修改时间
- 支持粘贴功能
- 实时更新笔记内容

### 6.3 标题编辑器 (TitleEditor.java)

TitleEditor 用于编辑笔记的标题，提供了一个简洁的编辑界面。

关键特性：
- 专注于标题编辑
- 支持键盘快捷键
- 实时保存标题变更

### 6.4 Live Folder 支持 (NotesLiveFolder.java)

NotesLiveFolder 实现了 Android Live Folder 功能，允许用户在主屏幕上直接查看和访问笔记，无需打开应用。

关键特性：
- 实时显示最新笔记
- 支持直接从主屏幕打开笔记
- 提供快速访问入口

实现代码：
```java
public class NotesLiveFolder extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // 检查Intent是否为Live Folder请求
        if (getIntent().getAction().equals("android.intent.action.CREATE_LIVE_FOLDER")) {
            // 创建Live Folder的内容URI
            Uri liveFolderUri = NotePad.Notes.CONTENT_URI;
            
            // 设置Live Folder的基本信息
            Intent intent = new Intent();
            intent.setData(liveFolderUri);
            intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_NAME, getString(R.string.live_folder_name));
            intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_ICON, Intent.ShortcutIconResource.fromContext(this, R.drawable.ic_launcher_notepad));
            intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_DISPLAY_MODE, LiveFolders.DISPLAY_MODE_LIST);
            intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_URI, liveFolderUri);
            
            // 设置结果并返回
            setResult(RESULT_OK, intent);
        } else {
            setResult(RESULT_CANCELED);
        }
        
        // 结束Activity
        finish();
    }
}
```

## 七、如何运行项目

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

<<<<<<< HEAD
## 八、使用说明

1. **创建新笔记**：点击菜单中的"新建"按钮，进入笔记编辑界面
2. **编辑笔记**：点击列表中的任意笔记项，进入编辑界面
3. **删除笔记**：长按笔记项，从上下文菜单中选择删除
4. **搜索笔记**：点击搜索图标，输入关键词进行搜索
5. **切换主题**：从菜单中选择亮色或暗色主题
6. **分类管理**：在编辑笔记时，通过下拉菜单选择笔记分类
7. **复制笔记**：长按笔记项，从上下文菜单中选择复制
8. **编辑标题**：在笔记编辑界面点击标题，进入标题编辑界面

## 九、实际成果展示

### 应用截图
=======
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
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0

## 十、许可证

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
<<<<<<< HEAD
=======

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
>>>>>>> 06e0829f6a858444b32880d0ecf2d80b44713ae0
