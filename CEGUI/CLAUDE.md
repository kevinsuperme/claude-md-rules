# CEGUI Layout开发规范

## 核心编程原则

### 1. 可读性优先
- 布局文件应该清晰易读，具有自解释性
- 使用有意义的窗口和控件名称
- 合理组织XML结构，保持层次清晰
- 添加必要的注释说明复杂布局逻辑

### 2. DRY原则 (Don't Repeat Yourself)
- 通过继承和模板减少重复布局代码
- 使用LookNFeel统一控件外观
- 合理使用相对定位减少硬编码坐标

### 3. 高内聚低耦合
- 将相关功能的UI元素组织在一起
- 避免布局文件间的过度依赖
- 使用事件系统实现组件间通信

### 4. DDD+TDD融合方法论
- 基于领域模型设计UI结构
- 先编写测试用例，再实现布局
- 持续重构优化布局结构

## 具体执行指令

### 沟通与语言规范
- 所有交流使用中文
- 术语使用CEGUI官方文档中的标准术语
- 布局文件使用UTF-8编码
- 注释使用中文，便于团队协作

### 批判性反馈与破框思维
- 定期审查布局文件，提出改进建议
- 不拘泥于传统布局方式，探索创新UI设计
- 鼓励质疑现有布局结构的合理性
- 提供多种布局方案供选择

### 开发与调试策略
- 使用CEGUI Layout Editor进行可视化设计
- 通过日志系统调试布局问题
- 利用CEGUI内置的调试工具分析布局
- 逐步集成测试，确保布局正确性

### 测试驱动开发规范
- 为每个复杂布局编写单元测试
- 测试不同分辨率下的布局适应性
- 验证事件处理和交互逻辑
- 进行性能测试，确保布局加载效率

## 常用命令

### 布局文件验证
```bash
# 验证XML语法
xmllint --noout layout.layout

# 使用CEGUI工具验证布局
ceguilayoutvalidator --input layout.layout
```

### 布局文件转换
```bash
# 转换旧版本布局到新版本
ceguilayoutconverter --from old_version --to new_version --input old.layout --output new.layout
```

### 资源管理
```bash
# 编译资源文件
ceguiresourcecompiler --resource-dir ./resources --output ./resources.dat

# 打包布局文件
ceguilayoutpackager --source ./layouts --output ./layouts.pkg
```

## 推荐项目结构

```
project/
├── resources/
│   ├── layouts/
│   │   ├── main_menu.layout
│   │   ├── game_hud.layout
│   │   └── settings.layout
│   ├── looknfeel/
│   │   ├── TaharezLook.looknfeel
│   │   └── CustomLook.looknfeel
│   ├── schemes/
│   │   ├── TaharezLook.scheme
│   │   └── Custom.scheme
│   └── imagesets/
│       ├── TaharezLook.imageset
│       └── Custom.imageset
├── src/
│   ├── ui/
│   │   ├── LayoutManager.cpp
│   │   ├── UIEventHandler.cpp
│   │   └── UILoader.cpp
│   └── tests/
│       ├── layout_tests.cpp
│       └── ui_interaction_tests.cpp
└── docs/
    ├── ui_design_guidelines.md
    └── layout_reference.md
```

## CEGUI Layout文件结构

### 基本XML结构
```xml
<?xml version="1.0" encoding="UTF-8"?>
<GUILayout version="4">
    <Window type="DefaultWindow" name="Root">
        <!-- 布局内容 -->
    </Window>
</GUILayout>
```

### 窗口属性(Property)设置
```xml
<Window type="TaharezLook/FrameWindow" name="MainWindow">
    <Property name="Position" value="{{0.1,0},{0.1,0}}" />
    <Property name="Size" value="{{0.8,0},{0.8,0}}" />
    <Property name="Text" value="主窗口" />
    <Property name="Alpha" value="0.8" />
</Window>
```

### 常用窗口类型
- `DefaultWindow`: 基础容器窗口
- `TaharezLook/FrameWindow`: 标准框架窗口
- `TaharezLook/Button`: 按钮控件
- `TaharezLook/Editbox`: 文本输入框
- `TaharezLook/Listbox`: 列表控件
- `TaharezLook/Combobox`: 下拉框
- `TaharezLook/Checkbox`: 复选框
- `TaharezLook/RadioButton`: 单选按钮
- `TaharezLook/ProgressBar`: 进度条
- `TaharezLook/Scrollbar`: 滚动条

### 常用属性说明
- `Position`: 位置，格式为`{{x比例,x偏移},{y比例,y偏移}}`
- `Size`: 大小，格式同Position
- `Text`: 显示文本
- `Alpha`: 透明度(0-1)
- `Visible`: 是否可见
- `Disabled`: 是否禁用
- `AlwaysOnTop`: 是否置顶
- `ClippedByParent`: 是否被父窗口裁剪
- `ID": 控件ID，用于代码中查找
- `UserData": 用户自定义数据

## 开发指南

### 1. 布局设计最佳实践

#### 相对定位与绝对定位
```xml
<!-- 相对定位 - 推荐使用，适应不同分辨率 -->
<Property name="Position" value="{{0.5,-100},{0.5,-50}}" />
<Property name="Size" value="{{0,200},{0,100}}" />

<!-- 绝对定位 - 谨慎使用，可能在不同分辨率下显示异常 -->
<Property name="Position" value="{{0,100},{0,200}}" />
<Property name="Size" value="{{0,200},{0,100}}" />
```

#### 响应式布局设计
```xml
<!-- 使用UnifiedDim相对尺寸 -->
<Property name="Size" value="{{0.3,0},{0.2,0}}" />

<!-- 根据内容自动调整大小 -->
<Property name="AutoSizing" value="Enabled" />

<!-- 最小/最大尺寸限制 -->
<Property name="MinSize" value="{{0,100},{0,50}}" />
<Property name="MaxSize" value="{{0,500},{0,300}}" />
```

### 2. 事件处理

#### 在布局文件中定义事件
```xml
<Window type="TaharezLook/Button" name="OKButton">
    <Property name="Text" value="确定" />
    <Event name="Clicked" function="handleOKButtonClicked" />
</Window>
```

#### 在代码中处理事件
```cpp
// 注册事件处理器
void MyClass::initializeUI() {
    CEGUI::Window* root = CEGUI::System::getSingleton().getDefaultGUIContext().getRootWindow();
    CEGUI::Window* okButton = root->getChild("MainWindow/OKButton");
    
    if(okButton) {
        okButton->subscribeEvent(CEGUI::PushButton::EventClicked,
            CEGUI::Event::Subscriber(&MyClass::onOKButtonClicked, this));
    }
}

// 事件处理函数
bool MyClass::onOKButtonClicked(const CEGUI::EventArgs& e) {
    // 处理按钮点击事件
    return true;
}
```

### 3. 动画与过渡效果

#### 使用PropertyAnimator实现动画
```xml
<Window type="TaharezLook/Button" name="AnimatedButton">
    <Property name="Position" value="{{0.5,-50},{0.8,0}}" />
    <Property name="Size" value="{{0,100},{0,30}}" />
    <Property name="Alpha" value="1.0" />
    
    <!-- 定义动画 -->
    <Property name="FadeInAnimation" value="Alpha:0.0->1.0:0.5" />
    <Property name="HoverAnimation" value="Size:{{0,100},{0,30}}->{{0,110},{0,35}}:0.2" />
</Window>
```

#### 代码中控制动画
```cpp
// 淡入效果
void MyClass::fadeInWindow(const std::string& windowName) {
    CEGUI::Window* window = getRootWindow()->getChild(windowName);
    if(window) {
        window->setAlpha(0.0f);
        window->setProperty("AlphaAnimation", "0.0->1.0:0.5");
        window->setProperty("FadeInAnimation", "true");
    }
}
```

### 4. 布局继承与模板

#### 创建基础模板
```xml
<!-- base_button.layout -->
<?xml version="1.0" encoding="UTF-8"?>
<GUILayout version="4">
    <Window type="TaharezLook/Button" name="BaseButton">
        <Property name="Size" value="{{0,100},{0,30}}" />
        <Property name="Text" value="" />
        <Property name="HoverImage" value="TaharezLook/ButtonHover" />
        <Property name="PushedImage" value="TaharezLook/ButtonPushed" />
    </Window>
</GUILayout>
```

#### 继承并扩展模板
```xml
<!-- special_button.layout -->
<?xml version="1.0" encoding="UTF-8"?>
<GUILayout version="4">
    <Window type="TaharezLook/Button" name="SpecialButton">
        <!-- 继承基础属性 -->
        <Property name="Size" value="{{0,100},{0,30}}" />
        <Property name="Text" value="" />
        <Property name="HoverImage" value="TaharezLook/ButtonHover" />
        <Property name="PushedImage" value="TaharezLook/ButtonPushed" />
        
        <!-- 扩展属性 -->
        <Property name="NormalImage" value="Custom/SpecialButtonNormal" />
        <Property name="DisabledImage" value="Custom/SpecialButtonDisabled" />
    </Window>
</GUILayout>
```

### 5. 性能优化建议

#### 布局加载优化
```cpp
// 异步加载大型布局
void MyClass::loadLargeLayoutAsync(const std::string& layoutFile) {
    std::thread([this, layoutFile]() {
        try {
            CEGUI::Window* layout = CEGUI::WindowManager::getSingleton().loadLayoutFromFile(layoutFile);
            
            // 在主线程中添加到UI
            CEGUI::System::getSingleton().getDefaultGUIContext().getRootWindow()->addChild(layout);
        } catch (const std::exception& e) {
            // 处理加载错误
            logError("Failed to load layout: " + std::string(e.what()));
        }
    }).detach();
}
```

#### 布局缓存策略
```cpp
class LayoutCache {
private:
    std::map<std::string, CEGUI::Window*> cache;
    
public:
    CEGUI::Window* getOrCreateLayout(const std::string& layoutFile) {
        auto it = cache.find(layoutFile);
        if(it != cache.end()) {
            // 返回缓存的布局副本
            return CEGUI::WindowManager::getSingleton().duplicateWindow(it->second);
        }
        
        // 加载并缓存布局
        CEGUI::Window* layout = CEGUI::WindowManager::getSingleton().loadLayoutFromFile(layoutFile);
        cache[layoutFile] = CEGUI::WindowManager::getSingleton().duplicateWindow(layout);
        return layout;
    }
};
```

## 测试策略

### 1. 布局单元测试
```cpp
// 测试布局加载
TEST(LayoutTest, CanLoadMainWindow) {
    CEGUI::Window* layout = CEGUI::WindowManager::getSingleton().loadLayoutFromFile("main_window.layout");
    ASSERT_NE(nullptr, layout);
    EXPECT_EQ("MainWindow", layout->getName());
}

// 测试控件存在性
TEST(LayoutTest, ContainsRequiredButtons) {
    CEGUI::Window* layout = CEGUI::WindowManager::getSingleton().loadLayoutFromFile("main_window.layout");
    ASSERT_NE(nullptr, layout->getChild("MainWindow/OKButton"));
    ASSERT_NE(nullptr, layout->getChild("MainWindow/CancelButton"));
}

// 测试属性设置
TEST(LayoutTest, ButtonHasCorrectText) {
    CEGUI::Window* layout = CEGUI::WindowManager::getSingleton().loadLayoutFromFile("main_window.layout");
    CEGUI::Window* button = layout->getChild("MainWindow/OKButton");
    EXPECT_EQ("确定", button->getText());
}
```

### 2. 布局集成测试
```cpp
// 测试事件处理
TEST(LayoutIntegrationTest, ButtonClickTriggersCorrectAction) {
    TestUI ui;
    ui.initialize();
    
    // 模拟按钮点击
    CEGUI::Window* okButton = ui.getRootWindow()->getChild("MainWindow/OKButton");
    okButton->fireEvent(CEGUI::PushButton::EventClicked, CEGUI::EventArgs());
    
    // 验证结果
    EXPECT_TRUE(ui.isOKButtonClicked());
}
```

## 常见问题与解决方案

### 1. 布局在不同分辨率下显示异常
**问题**: 在高分辨率或不同宽高比的显示器上布局变形
**解决方案**:
- 优先使用相对定位而非绝对像素值
- 设置最小/最大尺寸限制
- 测试多种分辨率下的显示效果
- 使用CEGUI的自动缩放功能

### 2. 控件重叠或遮挡
**问题**: 控件显示顺序不正确，重要元素被遮挡
**解决方案**:
- 调整XML中控件的定义顺序
- 使用`AlwaysOnTop`属性强制置顶
- 合理设置父窗口和子窗口关系
- 使用`ZOrderingEnabled`属性控制Z轴顺序

### 3. 布局加载性能问题
**问题**: 复杂布局加载缓慢，影响用户体验
**解决方案**:
- 实施布局缓存策略
- 异步加载非关键UI
- 简化嵌套层次结构
- 使用布局模板减少重复代码

### 4. 事件处理失效
**问题**: 控件点击或其他事件无响应
**解决方案**:
- 检查控件名称是否正确
- 确认事件处理器已正确注册
- 验证控件未被禁用或遮挡
- 检查事件冒泡是否被阻止

## 参考资源

- [CEGUI官方文档](http://static.cegui.org.uk/docs/0.7.1/)
- [CEGUI布局编辑器指南](http://static.cegui.org.uk/docs/0.7.1/editor.html)
- [CEGUI API参考](http://static.cegui.org.uk/docs/0.7.1/api_reference.html)
- [CEGUI示例代码](http://static.cegui.org.uk/docs/0.7.1/samples.html)