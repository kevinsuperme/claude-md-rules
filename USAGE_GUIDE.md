# Claude MD Rules 使用指南

**版本**: 2.0 (XML-Structured)
**最后更新**: 2025-10-19
**适用于**: 开发者、技术主管、架构师

---

## 📖 快速开始

### **5 分钟快速上手**

1. **克隆或下载项目**
   ```bash
   git clone https://github.com/kevinsuperme/claude-md-rules.git
   cd claude-md-rules
   ```

2. **选择您的语言规范**
   ```bash
   # Python 项目
   cp Python/CLAUDE.md /path/to/your/python/project/

   # Java 项目
   cp Java/CLAUDE.md /path/to/your/java/project/

   # 其他语言类似...
   ```

3. **让 Claude 读取规范**

   在 Claude Code 中打开您的项目，Claude 会自动读取 `CLAUDE.md` 文件并遵循其中的规范。

---

## 🎯 适用场景

### **场景 1：新项目启动**

**步骤**：
1. 复制对应语言的 `CLAUDE.md` 到项目根目录
2. 根据项目特点调整元数据：
   ```xml
   <metadata>
     <language>Python</language>
     <language_version>3.11</language_version>  <!-- 修改为实际版本 -->
     <framework_ecosystem>
       <primary>FastAPI 0.104.1</primary>  <!-- 修改为实际框架 -->
     </framework_ecosystem>
   </metadata>
   ```
3. 添加项目特定的原则（可选）
4. 开始编码！

### **场景 2：现有项目集成**

**步骤**：
1. 分析现有项目的编码规范
2. 选择最接近的语言规范模板
3. 自定义规则以匹配现有规范：
   ```xml
   <guiding_principles language="Python">
     <!-- 保留标准原则 -->
     <principle name="pythonic_style" priority="critical">...</principle>

     <!-- 添加项目特定原则 -->
     <principle name="company_specific_logging" priority="mandatory">
       <title>日志记录规范</title>
       <description>所有 API 调用必须记录请求和响应</description>
       <implementation>
         <code><![CDATA[
import logging

logger = logging.getLogger(__name__)

@app.post("/api/users")
async def create_user(user: UserCreate):
    logger.info(f"Creating user: {user.email}")
    result = await user_service.create(user)
    logger.info(f"User created: {result.id}")
    return result
         ]]></code>
       </implementation>
     </principle>
   </guiding_principles>
   ```

### **场景 3：团队协作**

**步骤**：
1. 将 `CLAUDE.md` 纳入版本控制
2. 在 `README.md` 中说明规范位置
3. 团队成员使用相同的规范文件
4. 通过 PR 审查确保遵循规范

**示例 README.md 内容**：
```markdown
## 开发规范

本项目使用 Claude MD Rules 规范，详见 [CLAUDE.md](./CLAUDE.md)。

使用 Claude Code 开发时，Claude 会自动遵循这些规范。

手动开发时，请参考规范中的：
- 核心编程原则
- 代码示例
- 强制要求
```

---

## 🔧 高级定制

### **添加自定义 Few-Shot 示例**

```xml
<examples category="your_project_patterns">
  <example id="custom-01" category="authentication">
    <title>JWT 认证实现</title>
    <scenario>实现符合公司安全标准的 JWT 认证</scenario>
    <code><![CDATA[
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from jose import JWTError, jwt

security = HTTPBearer()

async def get_current_user(
    credentials: HTTPAuthorizationCredentials = Depends(security)
) -> User:
    """从 JWT token 获取当前用户"""
    try:
        payload = jwt.decode(
            credentials.credentials,
            settings.SECRET_KEY,
            algorithms=[settings.ALGORITHM]
        )
        user_id: str = payload.get("sub")
        if user_id is None:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Invalid authentication credentials"
            )
    except JWTError:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials"
        )

    user = await user_service.get_by_id(user_id)
    if user is None:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="User not found"
        )

    return user
    ]]></code>
    <key_points>
      <point>使用 HTTPBearer 进行 token 验证</point>
      <point>JWT 解码失败时返回 401</point>
      <point>用户不存在时返回 401</point>
      <point>异步函数提升性能</point>
    </key_points>
  </example>
</examples>
```

### **覆盖共享模块规则**

如果项目需要不同的工作流程：

```xml
<claude_rules version="2.0" language="Python">
  <metadata>...</metadata>

  <!-- 不引用标准工作流 -->
  <!-- <import source="../_core/workflow-three-phases.md"/> -->

  <!-- 定义项目特定工作流 -->
  <development_workflow>
    <phase id="1" name="design">
      <title>设计阶段</title>
      <description>先设计 API 接口和数据模型</description>
    </phase>
    <phase id="2" name="implement">
      <title>实现阶段</title>
      <description>实现功能并编写测试</description>
    </phase>
    <phase id="3" name="review">
      <title>审查阶段</title>
      <description>团队代码审查</description>
    </phase>
  </development_workflow>
</claude_rules>
```

### **添加项目特定工具**

```xml
<tools_and_commands language="Python">
  <!-- 标准命令 -->
  <command_category name="environment_management">
    <title>环境管理</title>
    <commands>...</commands>
  </command_category>

  <!-- 项目特定命令 -->
  <command_category name="project_specific">
    <title>项目特定命令</title>
    <commands>
      <command>
        <description>启动本地开发环境</description>
        <code>docker-compose up -d && python manage.py runserver</code>
      </command>
      <command>
        <description>运行集成测试</description>
        <code>pytest tests/integration --cov=src</code>
      </command>
      <command>
        <description>生成 API 文档</description>
        <code>python -m mkdocs serve</code>
      </command>
    </commands>
  </command_category>
</tools_and_commands>
```

---

## 📊 性能优化技巧

### **技巧 1：按需引用共享模块**

如果项目不需要某些共享模块：

```xml
<imports>
  <!-- 只引用需要的模块 -->
  <import source="../_core/workflow-three-phases.md">
    三阶段工作流程
  </import>
  <!-- 不引用 DDD+TDD 如果项目不使用 -->
  <!-- <import source="../_core/ddd-tdd-methodology.md"/> -->
</imports>
```

### **技巧 2：使用 lazy_load 标记**

对于大型示例：

```xml
<examples>
  <example id="advanced-01" complexity="advanced" lazy_load="true">
    <!-- 只在需要时加载 -->
    <title>高级设计模式</title>
    <summary>包含策略模式、工厂模式等</summary>
    <full_content_link>_examples/advanced-patterns.md</full_content_link>
  </example>
</examples>
```

### **技巧 3：控制示例数量**

**推荐配置**：
- 基础项目：3-4 个示例
- 中型项目：5-7 个示例
- 大型项目：8-10 个示例

**超过 10 个示例时**，考虑：
1. 创建外部示例库
2. 使用分类和优先级
3. 实现按需加载

---

## 🎓 最佳实践

### **实践 1：保持 CLAUDE.md 简洁**

**Do**（推荐）：
```xml
<guiding_principles>
  <principle name="solid" priority="critical">
    <title>SOLID 原则</title>
    <description>遵循 SOLID 设计原则</description>
    <reference>详见共享模块</reference>
  </principle>
</guiding_principles>
```

**Don't**（不推荐）：
```xml
<guiding_principles>
  <principle name="solid">
    <title>SOLID 原则</title>
    <sub_principle>S - 单一职责原则
      <definition>...</definition>
      <examples>...</examples>
      <anti_patterns>...</anti_patterns>
    </sub_principle>
    <!-- 重复大量内容 -->
  </principle>
</guiding_principles>
```

### **实践 2：使用语义化的 ID**

**Do**（推荐）：
```xml
<example id="py-ddd-entity-user" category="ddd_entity">
```

**Don't**（不推荐）：
```xml
<example id="example1" category="code">
```

### **实践 3：为每个示例添加 key_points**

```xml
<example id="...">
  <code>...</code>
  <key_points>
    <point>为什么这样做</point>
    <point>关键的设计决策</point>
    <point>常见陷阱及避免方法</point>
  </key_points>
</example>
```

### **实践 4：定期更新框架版本**

```xml
<metadata>
  <language_version>3.11</language_version>  <!-- 定期检查更新 -->
  <framework_ecosystem>
    <primary>FastAPI 0.104.1</primary>  <!-- 定期检查更新 -->
    <last_updated>2025-10-19</last_updated>  <!-- 记录更新日期 -->
  </framework_ecosystem>
</metadata>
```

---

## 🔍 故障排查

### **问题 1：Claude 没有遵循 CLAUDE.md 中的规则**

**可能原因**：
1. 文件名不正确（必须是 `CLAUDE.md`）
2. 文件位置不对（应在项目根目录或 Claude 能访问的位置）
3. XML 格式错误

**解决方案**：
```bash
# 1. 检查文件名
ls -la | grep CLAUDE.md

# 2. 验证 XML 格式
xmllint --noout CLAUDE.md

# 3. 如果没有 xmllint，使用在线验证工具
# https://www.xmlvalidation.com/
```

### **问题 2：提示 XML 解析错误**

**常见错误**：

**错误 1**：未闭合标签
```xml
<!-- ❌ 错误 -->
<principle name="solid">
  <description>SOLID 原则
</principle>

<!-- ✅ 正确 -->
<principle name="solid">
  <description>SOLID 原则</description>
</principle>
```

**错误 2**：特殊字符未转义
```xml
<!-- ❌ 错误 -->
<code>
if (x < 5 && y > 3) {
  // ...
}
</code>

<!-- ✅ 正确 -->
<code><![CDATA[
if (x < 5 && y > 3) {
  // ...
}
]]></code>
```

### **问题 3：性能下降（Token 使用过多）**

**诊断步骤**：

1. **检查文件大小**
   ```bash
   wc -l CLAUDE.md
   # 推荐：< 1000 行
   # 警告：> 1500 行
   # 需优化：> 2000 行
   ```

2. **分析 Token 使用**

   使用 [OpenAI Tokenizer](https://platform.openai.com/tokenizer) 估算 Token 数量

3. **优化策略**
   - 移除不必要的示例
   - 将详细示例移到外部文件
   - 使用共享模块引用替代重复内容

---

## 📚 扩展资源

### **官方文档**

- [Claude Code Documentation](https://docs.claude.com/claude-code)
- [Prompt Engineering Guide](https://docs.anthropic.com/claude/docs/intro-to-prompting)
- [Effective Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)

### **相关工具**

- **XML 验证器**: https://www.xmlvalidation.com/
- **Token 计数器**: https://platform.openai.com/tokenizer
- **Markdown 转 XML**: https://pandoc.org/

---

## 🆘 获取帮助

### **常见问题解答 (FAQ)**

**Q: 可以同时使用多个语言的 CLAUDE.md 吗？**

A: 可以！在多语言项目中，为每个语言子目录创建对应的 CLAUDE.md：

```
project/
├── backend/
│   └── CLAUDE.md          # Java 规范
├── frontend/
│   └── CLAUDE.md          # Frontend 规范
└── scripts/
    └── CLAUDE.md          # Python 规范
```

**Q: 如何处理团队成员的不同偏好？**

A: 建议团队共同讨论并达成一致，将共识写入 CLAUDE.md。对于可选规则，使用 `priority="recommended"` 而非 `priority="mandatory"`。

**Q: 是否需要在每次提交时更新 CLAUDE.md？**

A: 不需要。CLAUDE.md 是长期稳定的规范文档。只在以下情况更新：
- 框架版本升级
- 团队规范变更
- 发现规范中的错误
- 添加新的最佳实践

**Q: 性能影响有多大？**

A: 根据我们的测试：
- 初次加载：~2-3 秒（解析 XML）
- 后续请求：几乎无影响（Claude 已理解规范）
- Token 成本：每个文件 ~3,000 tokens

### **联系支持**

- **GitHub**: [https://github.com/kevinsuperme/claude-md-rules](https://github.com/kevinsuperme/claude-md-rules)
- **Email**: iphone.com@live.cn
- **作者**: SuperKevin

---

## 🎯 下一步

1. **选择适合的语言规范** → [返回项目首页](../README.md)
2. **查看完整优化报告** → [阅读 OPTIMIZATION_REPORT.md](./OPTIMIZATION_REPORT.md)
3. **学习 XML 结构设计** → [深入理解优化原理](./OPTIMIZATION_REPORT.md#b-阶段深入讨论特定优化点)

---

**祝您使用愉快！** 🚀

如有任何问题，欢迎随时联系我们。
