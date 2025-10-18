# CLAUDE - Lua 开发规范

This file provides guidance to Claude Code (claude.ai/code) when working with Lua code in this repository.

### 第一部分：核心编程原则 (Guiding Principles)
这是我们合作的顶层思想，指导所有具体的行为。

#### 基础设计原则
- **可读性优先 (Readability First)：** 遵循 Lua 简洁优雅的设计哲学，使用有意义的命名和清晰的结构。
- **DRY (Don't Repeat Yourself)：** 通过模块、函数和闭包来消除重复，充分利用 Lua 的元编程能力。
- **高内聚，低耦合 (High Cohesion, Low Coupling)：** 利用 Lua 的模块系统实现清晰的代码组织，避免全局状态污染。

#### DDD + TDD 融合开发方法论
- **领域驱动设计 (Domain-Driven Design)：** 采用 Domain Model 组织代码结构，利用 Lua 的灵活数据结构表示领域概念。
- **测试驱动开发 (Test-Driven Development)：** 使用 busted 框架，每完成一个功能模块就立即编写相应的测试。
- **渐进式开发策略：** 利用 Lua 的交互式特性和 LuaJIT 的即时编译能力，快速验证实现。
- **领域边界清晰：** 通过 Lua 模块系统明确 domain 关系对应。
- **AI 辅助质量保障：** 结合 AI 工具和 Lua 的静态分析工具提升代码质量。

### 第二部分：具体执行指令 (Actionable Instructions)
这是 Claude 在 Lua 开发中需要严格遵守的具体操作指南。

#### 沟通与语言规范
- **默认语言：** 请默认使用简体中文进行所有交流、解释和思考过程的陈述。
- **代码与术语：** 所有代码实体（变量名、函数名、模块名等）及技术术语必须保持英文原文。
- **注释规范：** 代码注释应使用中文，关键函数和模块应有详细注释。

#### 批判性反馈与破框思维
- **审慎分析：** 必须以审视和批判的眼光分析输入，主动识别潜在的问题和 Lua 最佳实践违背。
- **坦率直言：** 指出非 Lua 惯用的代码模式，推荐更优雅的 Lua 解决方案。
- **严厉质询：** 对于违背 Lua 最佳实践的代码，必须明确指出并提供改进建议。

#### 开发与调试策略 (Development & Debugging Strategy)

##### 问题解决策略
- **坚韧不拔的解决问题：** 充分利用 Lua 的调试工具（如 LuaInspect、LuaPanda）进行问题定位。
- **逐个击破：** 使用 Lua 的交互式特性逐步验证每个组件的功能。
- **探索有效替代方案：** 优先考虑 Lua 标准库和成熟的第三方库解决方案。
- **禁止伪造实现：** 严禁使用空函数作为功能实现，所有代码必须具备真实逻辑。

##### 测试驱动开发 (TDD) 规范
- **busted 优先原则：** 使用 busted 作为主要测试框架，充分利用其断言和描述性测试功能。
- **Red-Green-Refactor 循环：** 
  1. **Red（红）：** 编写失败的测试用例，验证错误处理
  2. **Green（绿）：** 编写最少的代码使测试通过
  3. **Refactor（重构）：** 利用 Lua 的元编程特性优化代码
- **测试覆盖率：** 确保测试覆盖率达到 90% 以上
- **文档化示例：** 在注释中包含可执行的示例代码

##### AI 辅助开发指导原则
- **静态分析集成：** 结合 LuaCheck、LuaStan 等工具确保代码质量
- **IDE 增强：** 利用 AI 辅助的代码补全和重构建议
- **代码审查：** 对 AI 生成的代码进行 Lua 最佳实践检查

#### 项目与代码维护
- **Lua 风格遵循：** 严格遵守 Lua 社区推荐的代码风格
- **注释规范：** 为所有公共函数和模块编写详细的注释
- **依赖管理：** 使用 LuaRocks 管理项目依赖
- **及时清理：** 定期清理未使用的变量和函数

## 常用命令

### 环境管理
```bash
# 安装 LuaRocks
# Windows 可从 https://luarocks.org/ 下载安装程序
# Linux/macOS
curl -R -O http://luarocks.github.io/luarocks/releases/luarocks-3.8.0.tar.gz
tar zxpf luarocks-3.8.0.tar.gz
cd luarocks-3.8.0
./configure && make && make install

# 安装依赖
luarocks install <package_name>

# 生成依赖文件
luarocks list --porcelain > dependencies.txt
```

### 测试和质量检查
```bash
# 安装 busted 测试框架
luarocks install busted

# 运行测试
busted

# 安装 LuaCheck 进行代码检查
luarocks install luacheck

# 运行代码检查
luacheck .
```

## 项目架构概览

### 推荐项目结构
```
project/
├── src/
│   ├── domain/          # 领域模型
│   │   ├── entities/    # 实体类
│   │   ├── value_objects/ # 值对象
│   │   └── services/    # 领域服务
│   ├── application/     # 应用层
│   │   ├── use_cases/   # 用例
│   │   └── dtos/        # 数据传输对象
│   ├── infrastructure/  # 基础设施层
│   │   ├── repositories/ # 仓储实现
│   │   └── adapters/    # 适配器
│   └── interfaces/      # 接口层
│       └── api/         # API 接口
├── tests/
│   ├── unit/           # 单元测试
│   ├── integration/    # 集成测试
│   └── e2e/           # 端到端测试
├── docs/              # 文档
├── dependencies.txt   # 依赖管理
└── README.md         # 项目说明
```

### 技术栈推荐
- **Web 框架：** Lapis / Kepler / 自定义基于 nginx-lua 的解决方案
- **数据库：** LuaSQL / SQLite3 for Lua / PostgreSQL for Lua
- **测试框架：** busted + luassert
- **代码检查：** luacheck
- **文档生成：** LDoc
- **性能分析：** LuaProfiler / LuaJIT profiler

## 开发指南

### 领域驱动设计 (DDD) 实施指南

#### 领域模型组织
```lua
-- 实体示例
local User = {}
User.__index = User

function User:new(name, email)
    local self = setmetatable({}, User)
    self.id = self:_generate_uuid()
    self.name = name
    self.email = email
    return self
end

function User:change_email(new_email)
    """更改用户邮箱"""
    if not self:_is_valid_email(new_email) then
        error("Invalid email format")
    end
    self.email = new_email
end

function User:_is_valid_email(email)
    """验证邮箱格式"""
    return string.find(email, "@") ~= nil  -- 简化实现
end

function User:_generate_uuid()
    -- 简化的 UUID 生成实现
    return "uuid-" .. tostring(os.time())
end

return User
```

#### 值对象设计
```lua
-- 值对象示例
local Money = {}
Money.__index = Money

function Money:new(amount, currency)
    local self = setmetatable({}, Money)
    self.amount = amount
    self.currency = currency or "USD"
    
    if self.amount < 0 then
        error("Amount cannot be negative")
    end
    
    return self
end

function Money:add(other)
    if self.currency ~= other.currency then
        error("Cannot add different currencies")
    end
    return Money:new(self.amount + other.amount, self.currency)
end

return Money
```

#### 仓储模式
```lua
-- 仓储接口
local UserRepository = {
    -- 抽象方法，由具体实现类提供
    save = function(self, user) error("Not implemented") end,
    find_by_id = function(self, user_id) error("Not implemented") end,
    find_by_email = function(self, email) error("Not implemented") end
}

-- 具体仓储实现示例
local SQLUserRepository = {}
SQLUserRepository.__index = SQLUserRepository

function SQLUserRepository:new(db_connection)
    local self = setmetatable({}, SQLUserRepository)
    self.db = db_connection
    return self
end

function SQLUserRepository:save(user)
    -- 实现数据库保存逻辑
    -- 示例:
    -- self.db:execute("INSERT INTO users (id, name, email) VALUES (?, ?, ?)", 
    --                 user.id, user.name, user.email)
    return true
end

function SQLUserRepository:find_by_id(user_id)
    -- 实现根据ID查询的逻辑
    return nil  -- 简化实现
end

function SQLUserRepository:find_by_email(email)
    -- 实现根据邮箱查询的逻辑
    return nil  -- 简化实现
end

return { UserRepository = UserRepository, SQLUserRepository = SQLUserRepository }
```

### 测试驱动开发最佳实践

#### 单元测试示例
```lua
describe("User", function()
    local User = require("src.domain.entities.user")
    
    describe("creation", function()
        it("should create a user with valid properties", function()
            local user = User:new("张三", "zhangsan@example.com")
            assert.is_not_nil(user.id)
            assert.are.equal(user.name, "张三")
            assert.are.equal(user.email, "zhangsan@example.com")
        end)
    end)
    
    describe("change_email", function()
        it("should successfully change email with valid format", function()
            local user = User:new("张三", "zhangsan@example.com")
            user:change_email("new@example.com")
            assert.are.equal(user.email, "new@example.com")
        end)
        
        it("should throw error with invalid email format", function()
            local user = User:new("张三", "zhangsan@example.com")
            assert.has_error(function()
                user:change_email("invalid-email")
            end, "Invalid email format")
        end)
    end)
end)

describe("Money", function()
    local Money = require("src.domain.value_objects.money")
    
    describe("creation", function()
        it("should create money with valid properties", function()
            local money = Money:new(100.0, "USD")
            assert.are.equal(money.amount, 100.0)
            assert.are.equal(money.currency, "USD")
        end)
        
        it("should use USD as default currency", function()
            local money = Money:new(100.0)
            assert.are.equal(money.currency, "USD")
        end)
        
        it("should throw error with negative amount", function()
            assert.has_error(function()
                Money:new(-50.0, "USD")
            end, "Amount cannot be negative")
        end)
    end)
    
    describe("add", function()
        it("should successfully add money with same currency", function()
            local money1 = Money:new(100.0, "USD")
            local money2 = Money:new(50.0, "USD")
            local result = money1:add(money2)
            assert.are.equal(result.amount, 150.0)
            assert.are.equal(result.currency, "USD")
        end)
        
        it("should throw error when adding different currencies", function()
            local money1 = Money:new(100.0, "USD")
            local money2 = Money:new(50.0, "EUR")
            assert.has_error(function()
                money1:add(money2)
            end, "Cannot add different currencies")
        end)
    end)
end)
```

#### 集成测试示例
```lua
describe("UserRepository Integration", function()
    local SQLUserRepository = require("src.infrastructure.repositories.sql_user_repository").SQLUserRepository
    local User = require("src.domain.entities.user")
    
    local repository
    local test_db
    
    setup(function()
        -- 设置测试数据库连接
        -- 这里应该是实际的数据库连接设置
        test_db = {}
        repository = SQLUserRepository:new(test_db)
    end)
    
    it("should save and retrieve user", function()
        local user = User:new("测试用户", "test@example.com")
        repository:save(user)
        
        -- 这里应该验证数据库操作是否成功
        -- local retrieved_user = repository:find_by_id(user.id)
        -- assert.are_equal(retrieved_user.email, "test@example.com")
    end)
end)
```

### Lua 特有编程模式与最佳实践

#### 模块模式
```lua
-- 推荐的模块实现方式
local M = {}

-- 私有函数
local function _private_helper()
    return "private result"
end

-- 公共API
function M.public_function()
    return "public result: " .. _private_helper()
end

-- 返回模块表
return M
```

#### 闭包与对象
```lua
-- 使用闭包创建对象
function createCounter(initial_value)
    local count = initial_value or 0
    
    return {
        increment = function()
            count = count + 1
            return count
        end,
        decrement = function()
            count = count - 1
            return count
        end,
        get = function()
            return count
        end
    }
end
```

#### 元表与面向对象
```lua
-- 使用元表实现面向对象编程
local Rectangle = {}
Rectangle.__index = Rectangle

function Rectangle:new(width, height)
    local self = setmetatable({}, self)
    self.width = width
    self.height = height
    return self
end

function Rectangle:area()
    return self.width * self.height
end

function Rectangle:perimeter()
    return 2 * (self.width + self.height)
end
```

#### 错误处理
```lua
-- 推荐的错误处理模式
function calculate_safe(operation, ...)
    local success, result = pcall(operation, ...)
    if not success then
        -- 记录错误
        print("Error occurred:", result)
        return nil, result
    end
    return result
end

-- 使用示例
local success, result = calculate_safe(function(a, b)
    if b == 0 then
        error("Division by zero")
    end
    return a / b
end, 10, 0)
```