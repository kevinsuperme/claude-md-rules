<?xml version="1.0" encoding="UTF-8"?>
<!--
  CLAUDE.md - Python 开发规范
  Version: 2.0 (XML-Structured)
  Last Updated: 2025-10-19

  This file provides structured guidance to Claude Code (claude.ai/code)
  when working with Python code in this repository.

  Based on Claude Prompt Engineering Best Practices:
  - XML structure for clear boundaries
  - Modular design with shared components
  - Chain-of-thought reasoning support
  - Few-shot examples for accuracy
-->

<claude_rules version="2.0" language="Python">

  <!-- ==================== 元数据 ==================== -->
  <metadata>
    <language>Python</language>
    <language_version>3.9+</language_version>
    <framework_ecosystem>
      <primary>FastAPI / Flask / Django</primary>
      <testing>pytest + pytest-asyncio</testing>
      <type_checking>mypy</type_checking>
      <formatting>black + isort</formatting>
    </framework_ecosystem>
    <last_updated>2025-10-19</last_updated>
    <schema_version>2.0</schema_version>
  </metadata>

  <!-- ==================== 共享模块引用 ==================== -->
  <imports>
    <import source="../_core/workflow-three-phases.md">
      三阶段工作流程：分析问题 → 细化方案 → 执行方案
    </import>
    <import source="../_core/ddd-tdd-methodology.md">
      DDD + TDD 融合方法论：领域驱动设计 + 测试驱动开发
    </import>
    <import source="../_core/communication-standards.md">
      沟通与语言规范：中文交流 + 英文代码 + 批判性反馈
    </import>
  </imports>

  <!-- ==================== 核心编程原则（Python 特定） ==================== -->
  <guiding_principles language="Python">
    <overview>
      Python 开发遵循"The Zen of Python"哲学，强调代码的优雅、简洁和可读性。
    </overview>

    <principle name="pythonic_style" priority="critical">
      <title>Pythonic 编程风格</title>
      <description>
        遵循 Python 的惯用法和设计哲学，代码应该像诗一样优美，简洁胜过复杂。
      </description>
      <zen_of_python_highlights>
        <item>Beautiful is better than ugly (优美胜于丑陋)</item>
        <item>Explicit is better than implicit (明了胜于晦涩)</item>
        <item>Simple is better than complex (简洁胜于复杂)</item>
        <item>Readability counts (可读性很重要)</item>
        <item>There should be one-- and preferably only one --obvious way to do it</item>
      </zen_of_python_highlights>
      <examples>
        <example category="list_comprehension">
          <good>
            <code>squares = [x**2 for x in range(10)]</code>
            <explanation>使用列表推导式，简洁明了</explanation>
          </good>
          <bad>
            <code>
squares = []
for x in range(10):
    squares.append(x**2)
            </code>
            <explanation>传统循环，冗长</explanation>
          </bad>
        </example>
        <example category="context_manager">
          <good>
            <code>
with open('file.txt', 'r') as f:
    content = f.read()
            </code>
            <explanation>使用上下文管理器，自动处理资源</explanation>
          </good>
          <bad>
            <code>
f = open('file.txt', 'r')
content = f.read()
f.close()
            </code>
            <explanation>手动管理资源，易出错</explanation>
          </bad>
        </example>
      </examples>
    </principle>

    <principle name="type_annotations" priority="mandatory">
      <title>类型注解强制要求</title>
      <description>
        所有函数和方法必须添加类型注解，提高代码可读性和IDE支持。
      </description>
      <requirements>
        <requirement>所有函数参数必须有类型注解</requirement>
        <requirement>所有函数返回值必须有类型注解</requirement>
        <requirement>复杂数据结构使用 typing 模块定义</requirement>
        <requirement>使用 mypy 进行静态类型检查</requirement>
      </requirements>
      <example>
        <code><![CDATA[
from typing import List, Optional, Dict, Any

def fetch_users(
    user_ids: List[int],
    include_deleted: bool = False
) -> List[Dict[str, Any]]:
    """获取用户列表

    Args:
        user_ids: 用户ID列表
        include_deleted: 是否包含已删除用户

    Returns:
        用户信息字典列表
    """
    pass
        ]]></code>
      </example>
    </principle>

    <principle name="virtual_environment" priority="mandatory">
      <title>虚拟环境强制规范</title>
      <description>
        所有 Python 项目**必须**使用虚拟环境，严禁在全局环境中安装项目依赖。
      </description>
      <setup_commands>
        <command platform="all">
          <![CDATA[
# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
source venv/bin/activate  # Linux/macOS
.\venv\Scripts\activate   # Windows

# 安装依赖
pip install -r requirements.txt
          ]]>
        </command>
      </setup_commands>
      <dependency_management>
        <tool name="pip">
          <file>requirements.txt</file>
          <command>pip freeze > requirements.txt</command>
        </tool>
        <tool name="poetry">
          <file>pyproject.toml</file>
          <command>poetry init && poetry add package_name</command>
        </tool>
      </dependency_management>
    </principle>

    <principle name="pep_compliance" priority="mandatory">
      <title>PEP 规范遵循</title>
      <description>
        严格遵守 PEP 8 代码风格和相关 PEP 规范。
      </description>
      <key_peps>
        <pep number="8">
          <title>Style Guide for Python Code</title>
          <enforcement>使用 black 进行代码格式化</enforcement>
        </pep>
        <pep number="257">
          <title>Docstring Conventions</title>
          <enforcement>为所有公共函数和类编写详细的文档字符串</enforcement>
        </pep>
        <pep number="484">
          <title>Type Hints</title>
          <enforcement>使用类型注解，通过 mypy 检查</enforcement>
        </pep>
      </key_peps>
      <toolchain>
        <tool name="black">代码格式化</tool>
        <tool name="isort">导入语句排序</tool>
        <tool name="pylint">代码质量检查</tool>
        <tool name="mypy">类型检查</tool>
      </toolchain>
    </principle>
  </guiding_principles>

  <!-- ==================== Python 特定执行指令 ==================== -->
  <actionable_instructions language="Python">

    <instruction_set category="development_strategy">
      <title>开发与调试策略</title>

      <problem_solving>
        <approach name="iterative_validation">
          <title>迭代式验证</title>
          <description>
            利用 Python 的交互式特性，在 REPL 或 Jupyter 中逐步验证每个组件的功能
          </description>
          <tools>
            <tool>IPython REPL</tool>
            <tool>Jupyter Notebook</tool>
            <tool>Python Debugger (pdb/ipdb)</tool>
          </tools>
        </approach>

        <approach name="no_pass_statements">
          <title>禁止伪造实现</title>
          <rule>严禁使用 pass 语句作为功能实现，所有代码必须具备真实逻辑</rule>
          <exception>
            仅在定义抽象基类或接口时允许使用 pass 语句
          </exception>
        </approach>
      </problem_solving>

      <testing_standards>
        <framework>pytest + pytest-asyncio</framework>
        <coverage_requirements>
          <requirement level="core_business_logic">90% 以上</requirement>
          <requirement level="utility_functions">80% 以上</requirement>
          <requirement level="overall">75% 以上</requirement>
        </coverage_requirements>

        <test_structure>
          <example><![CDATA[
import pytest
from src.domain.entities.user import User
from src.domain.value_objects.money import Money

class TestUser:
    """用户实体测试套件"""

    def test_user_creation(self):
        """测试用户创建"""
        user = User(name="张三", email="zhangsan@example.com")
        assert user.name == "张三"
        assert user.email == "zhangsan@example.com"
        assert user.id is not None

    def test_change_email_success(self):
        """测试成功更改邮箱"""
        user = User(name="张三", email="zhangsan@example.com")
        user.change_email("new@example.com")
        assert user.email == "new@example.com"

    def test_change_email_invalid_format(self):
        """测试无效邮箱格式"""
        user = User(name="张三", email="zhangsan@example.com")
        with pytest.raises(ValueError, match="Invalid email format"):
            user.change_email("invalid-email")

@pytest.mark.asyncio
async def test_async_operation():
    """测试异步操作"""
    result = await some_async_function()
    assert result is not None
          ]]></example>
        </test_structure>
      </testing_standards>
    </instruction_set>

    <instruction_set category="code_quality">
      <title>代码质量保障</title>

      <static_analysis>
        <tool name="mypy">
          <purpose>类型检查</purpose>
          <command>mypy src/</command>
          <config_file>mypy.ini or pyproject.toml</config_file>
        </tool>
        <tool name="pylint">
          <purpose>代码检查</purpose>
          <command>pylint src/</command>
          <minimum_score>8.0</minimum_score>
        </tool>
        <tool name="black">
          <purpose>代码格式化</purpose>
          <command>black src/</command>
          <line_length>88</line_length>
        </tool>
        <tool name="isort">
          <purpose>导入排序</purpose>
          <command>isort src/</command>
          <profile>black</profile>
        </tool>
      </static_analysis>

      <docstring_standards>
        <format>Google Style or NumPy Style</format>
        <example><![CDATA[
def calculate_total(
    items: List[Item],
    tax_rate: float = 0.1,
    discount: Optional[float] = None
) -> Decimal:
    """计算订单总额

    Args:
        items: 订单项目列表
        tax_rate: 税率，默认 10%
        discount: 可选的折扣率

    Returns:
        计算后的总额（包含税费和折扣）

    Raises:
        ValueError: 当税率为负或折扣超过100%时

    Examples:
        >>> items = [Item(price=100), Item(price=200)]
        >>> calculate_total(items, tax_rate=0.1)
        Decimal('330.00')
    """
    pass
        ]]></example>
      </docstring_standards>
    </instruction_set>

    <instruction_set category="async_programming">
      <title>异步编程规范</title>

      <guidelines>
        <guideline>异步函数必须使用 async/await 语法，不使用回调</guideline>
        <guideline>IO 密集型操作优先使用异步实现</guideline>
        <guideline>合理使用 asyncio.gather 进行并发操作</guideline>
        <guideline>避免在异步函数中使用阻塞调用</guideline>
      </guidelines>

      <example><![CDATA[
import asyncio
from typing import List
import aiohttp

async def fetch_user_data(user_id: int) -> dict:
    """异步获取用户数据"""
    async with aiohttp.ClientSession() as session:
        async with session.get(f"/api/users/{user_id}") as response:
            return await response.json()

async def process_users(user_ids: List[int]) -> List[dict]:
    """并发处理多个用户"""
    tasks = [fetch_user_data(user_id) for user_id in user_ids]
    return await asyncio.gather(*tasks)

# 错误处理
async def safe_fetch(user_id: int) -> Optional[dict]:
    """带错误处理的安全获取"""
    try:
        return await fetch_user_data(user_id)
    except aiohttp.ClientError as e:
        logger.error(f"Failed to fetch user {user_id}: {e}")
        return None
      ]]></example>
    </instruction_set>
  </actionable_instructions>

  <!-- ==================== 项目架构 ==================== -->
  <project_architecture language="Python">
    <recommended_structure>
      <![CDATA[
project/
├── src/
│   ├── domain/              # 领域模型
│   │   ├── entities/        # 实体类
│   │   ├── value_objects/   # 值对象
│   │   └── services/        # 领域服务
│   ├── application/         # 应用层
│   │   ├── use_cases/       # 用例
│   │   └── dtos/            # 数据传输对象
│   ├── infrastructure/      # 基础设施层
│   │   ├── repositories/    # 仓储实现
│   │   └── adapters/        # 适配器
│   └── interfaces/          # 接口层
│       └── web/             # Web 接口
├── tests/
│   ├── unit/               # 单元测试
│   ├── integration/        # 集成测试
│   └── e2e/               # 端到端测试
├── docs/                  # 文档
├── requirements.txt       # 依赖管理
├── pyproject.toml        # 项目配置
└── README.md             # 项目说明
      ]]>
    </recommended_structure>

    <tech_stack>
      <web_frameworks>
        <framework name="FastAPI" use_case="现代异步 API">
          <advantages>
            <advantage>自动生成 OpenAPI 文档</advantage>
            <advantage>原生支持异步</advantage>
            <advantage>基于类型注解的数据验证</advantage>
          </advantages>
        </framework>
        <framework name="Flask" use_case="轻量级同步应用">
          <advantages>
            <advantage>简单灵活</advantage>
            <advantage>丰富的扩展生态</advantage>
          </advantages>
        </framework>
        <framework name="Django" use_case="全栈企业级应用">
          <advantages>
            <advantage>完整的 ORM 和后台管理</advantage>
            <advantage>成熟的安全机制</advantage>
          </advantages>
        </framework>
      </web_frameworks>

      <orm_options>
        <orm name="SQLAlchemy">通用ORM，支持多种数据库</orm>
        <orm name="Django ORM">Django 内置ORM</orm>
        <orm name="Tortoise ORM">异步ORM</orm>
      </orm_options>
    </tech_stack>
  </project_architecture>

  <!-- ==================== Few-Shot 示例 ==================== -->
  <examples category="python_best_practices">

    <example id="py-01" category="ddd_entity">
      <title>领域实体设计</title>
      <scenario>设计一个符合 DDD 原则的用户实体</scenario>
      <code><![CDATA[
from dataclasses import dataclass
from typing import Optional
from uuid import UUID, uuid4

@dataclass
class User:
    """用户实体

    实体特征：
    1. 有唯一标识符（id）
    2. 可变性：属性可以改变，但身份不变
    3. 封装业务逻辑
    """
    id: UUID
    name: str
    email: str
    is_active: bool = True

    def __post_init__(self):
        """实体初始化后处理"""
        if self.id is None:
            self.id = uuid4()
        self._validate()

    def _validate(self) -> None:
        """验证实体状态"""
        if not self.name or len(self.name) < 2:
            raise ValueError("Name must be at least 2 characters")
        if not self._is_valid_email(self.email):
            raise ValueError("Invalid email format")

    def change_email(self, new_email: str) -> None:
        """更改用户邮箱（业务方法）"""
        if not self._is_valid_email(new_email):
            raise ValueError("Invalid email format")
        self.email = new_email

    def deactivate(self) -> None:
        """停用用户账户"""
        self.is_active = False

    @staticmethod
    def _is_valid_email(email: str) -> bool:
        """验证邮箱格式"""
        import re
        pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
        return re.match(pattern, email) is not None
      ]]></code>
      <key_points>
        <point>使用 @dataclass 简化实体定义</point>
        <point>业务逻辑封装在实体方法中</point>
        <point>通过私有方法进行验证</point>
        <point>状态变更通过明确的业务方法</point>
      </key_points>
    </example>

    <example id="py-02" category="value_object">
      <title>值对象设计</title>
      <scenario>设计一个不可变的货币值对象</scenario>
      <code><![CDATA[
from dataclasses import dataclass
from decimal import Decimal
from typing import Protocol

@dataclass(frozen=True)
class Money:
    """货币值对象

    值对象特征：
    1. 不可变性：frozen=True
    2. 值相等性：通过属性值判断相等
    3. 无副作用：方法返回新对象而非修改自身
    """
    amount: Decimal
    currency: str = "USD"

    def __post_init__(self):
        """初始化验证"""
        if self.amount < 0:
            raise ValueError("Amount cannot be negative")
        if len(self.currency) != 3:
            raise ValueError("Currency must be 3-letter code")

    def add(self, other: 'Money') -> 'Money':
        """加法操作（返回新对象）"""
        if self.currency != other.currency:
            raise ValueError("Cannot add different currencies")
        return Money(self.amount + other.amount, self.currency)

    def multiply(self, factor: Decimal) -> 'Money':
        """乘法操作"""
        return Money(self.amount * factor, self.currency)

    def __str__(self) -> str:
        return f"{self.amount:.2f} {self.currency}"
      ]]></code>
      <key_points>
        <point>使用 frozen=True 确保不可变性</point>
        <point>操作返回新对象，不修改原对象</point>
        <point>值对象间比较基于属性值</point>
      </key_points>
    </example>

    <example id="py-03" category="repository_pattern">
      <title>仓储模式实现</title>
      <scenario>实现用户仓储接口和 SQL 实现</scenario>
      <code><![CDATA[
from abc import ABC, abstractmethod
from typing import List, Optional
from uuid import UUID

class UserRepository(ABC):
    """用户仓储接口（抽象基类）"""

    @abstractmethod
    async def save(self, user: User) -> None:
        """保存用户"""
        pass

    @abstractmethod
    async def find_by_id(self, user_id: UUID) -> Optional[User]:
        """通过 ID 查找用户"""
        pass

    @abstractmethod
    async def find_by_email(self, email: str) -> Optional[User]:
        """通过邮箱查找用户"""
        pass

    @abstractmethod
    async def delete(self, user_id: UUID) -> bool:
        """删除用户"""
        pass

class SQLUserRepository(UserRepository):
    """SQL 用户仓储实现"""

    def __init__(self, db_session):
        self.db_session = db_session

    async def save(self, user: User) -> None:
        """保存用户到数据库"""
        # ORM 实现
        db_user = UserModel(
            id=str(user.id),
            name=user.name,
            email=user.email,
            is_active=user.is_active
        )
        self.db_session.add(db_user)
        await self.db_session.commit()

    async def find_by_id(self, user_id: UUID) -> Optional[User]:
        """从数据库查找用户"""
        result = await self.db_session.execute(
            select(UserModel).where(UserModel.id == str(user_id))
        )
        db_user = result.scalar_one_or_none()

        if db_user is None:
            return None

        # 将数据库模型转换为领域实体
        return User(
            id=UUID(db_user.id),
            name=db_user.name,
            email=db_user.email,
            is_active=db_user.is_active
        )
      ]]></code>
      <key_points>
        <point>使用抽象基类定义接口</point>
        <point>领域实体与数据库模型分离</point>
        <point>仓储负责实体与持久化的转换</point>
        <point>支持异步操作</point>
      </key_points>
    </example>

    <example id="py-04" category="tdd_workflow">
      <title>TDD 完整工作流程</title>
      <scenario>使用 TDD 实现用户登录功能</scenario>

      <phase name="red">
        <title>Red 阶段：编写失败的测试</title>
        <code><![CDATA[
import pytest
from src.domain.services.auth_service import AuthService
from src.domain.entities.user import User

class TestAuthService:
    def test_login_with_valid_credentials(self):
        """测试有效凭据登录"""
        # Arrange
        auth_service = AuthService()
        email = "test@example.com"
        password = "secure_password"

        # Act
        user = auth_service.login(email, password)

        # Assert
        assert user is not None
        assert user.email == email

    def test_login_with_invalid_password(self):
        """测试无效密码登录"""
        auth_service = AuthService()

        with pytest.raises(AuthenticationError):
            auth_service.login("test@example.com", "wrong_password")
        ]]></code>
        <explanation>先编写测试，此时运行会失败（Red），因为 AuthService 还未实现</explanation>
      </phase>

      <phase name="green">
        <title>Green 阶段：最简实现</title>
        <code><![CDATA[
class AuthenticationError(Exception):
    """认证失败异常"""
    pass

class AuthService:
    """认证服务（最简实现）"""

    def __init__(self):
        # 硬编码的测试数据（仅为通过测试）
        self._test_users = {
            "test@example.com": {
                "password": "secure_password",
                "user": User(
                    id=uuid4(),
                    name="Test User",
                    email="test@example.com"
                )
            }
        }

    def login(self, email: str, password: str) -> User:
        """登录方法（最简实现）"""
        if email in self._test_users:
            user_data = self._test_users[email]
            if user_data["password"] == password:
                return user_data["user"]

        raise AuthenticationError("Invalid credentials")
        ]]></code>
        <explanation>编写最少代码使测试通过（Green），虽然实现不完善，但测试已通过</explanation>
      </phase>

      <phase name="refactor">
        <title>Refactor 阶段：重构优化</title>
        <code><![CDATA[
from typing import Optional
import hashlib

class AuthService:
    """认证服务（重构版）"""

    def __init__(self, user_repository: UserRepository):
        self.user_repository = user_repository

    async def login(self, email: str, password: str) -> User:
        """用户登录

        Args:
            email: 用户邮箱
            password: 明文密码

        Returns:
            认证成功的用户实体

        Raises:
            AuthenticationError: 认证失败
        """
        # 参数验证
        if not email or not password:
            raise AuthenticationError("Email and password required")

        # 查找用户
        user = await self.user_repository.find_by_email(email)
        if user is None:
            raise AuthenticationError("Invalid credentials")

        # 验证密码
        if not self._verify_password(user, password):
            raise AuthenticationError("Invalid credentials")

        # 检查用户状态
        if not user.is_active:
            raise AuthenticationError("Account is deactivated")

        return user

    def _verify_password(self, user: User, password: str) -> bool:
        """验证密码（安全实现）"""
        # 使用安全的密码哈希比较
        # 实际应使用 bcrypt 或 argon2
        password_hash = hashlib.sha256(password.encode()).hexdigest()
        return user.password_hash == password_hash
        ]]></code>
        <explanation>
          重构改进：
          1. 使用依赖注入（UserRepository）
          2. 完整的参数验证和错误处理
          3. 用户状态检查
          4. 安全的密码验证
          5. 清晰的文档字符串
        </explanation>
      </phase>
    </example>
  </examples>

  <!-- ==================== 常用命令参考 ==================== -->
  <tools_and_commands language="Python">
    <command_category name="environment_management">
      <title>环境管理</title>
      <commands>
        <command>
          <description>创建虚拟环境</description>
          <code>python -m venv venv</code>
        </command>
        <command>
          <description>激活虚拟环境 (Linux/macOS)</description>
          <code>source venv/bin/activate</code>
        </command>
        <command>
          <description>激活虚拟环境 (Windows)</description>
          <code>.\venv\Scripts\activate</code>
        </command>
        <command>
          <description>安装依赖</description>
          <code>pip install -r requirements.txt</code>
        </command>
        <command>
          <description>生成依赖文件</description>
          <code>pip freeze > requirements.txt</code>
        </command>
      </commands>
    </command_category>

    <command_category name="testing_and_quality">
      <title>测试和质量检查</title>
      <commands>
        <command>
          <description>运行测试</description>
          <code>pytest</code>
        </command>
        <command>
          <description>运行测试并生成覆盖率报告</description>
          <code>pytest --cov=src --cov-report=html</code>
        </command>
        <command>
          <description>类型检查</description>
          <code>mypy src/</code>
        </command>
        <command>
          <description>代码格式化</description>
          <code>black src/ && isort src/</code>
        </command>
        <command>
          <description>代码检查</description>
          <code>pylint src/ && flake8 src/</code>
        </command>
      </commands>
    </command_category>

    <command_category name="package_management">
      <title>包管理</title>
      <commands>
        <command>
          <description>使用 Poetry 初始化</description>
          <code>poetry init</code>
        </command>
        <command>
          <description>使用 Poetry 添加依赖</description>
          <code>poetry add package_name</code>
        </command>
        <command>
          <description>构建包</description>
          <code>python -m build</code>
        </command>
        <command>
          <description>安装本地包（可编辑模式）</description>
          <code>pip install -e .</code>
        </command>
      </commands>
    </command_category>
  </tools_and_commands>

  <!-- ==================== 强制要求 ==================== -->
  <mandatory_requirements priority="critical">
    <requirement id="req-py-01">
      <title>虚拟环境强制要求</title>
      <description>所有 Python 项目必须使用虚拟环境</description>
      <enforcement>执行任何代码前检查虚拟环境是否激活</enforcement>
    </requirement>

    <requirement id="req-py-02">
      <title>类型注解强制要求</title>
      <description>所有函数和方法必须添加类型注解</description>
      <enforcement>通过 mypy 进行静态类型检查</enforcement>
    </requirement>

    <requirement id="req-py-03">
      <title>测试覆盖率要求</title>
      <description>核心业务逻辑测试覆盖率不低于 90%</description>
      <enforcement>CI/CD 流程中强制检查覆盖率</enforcement>
    </requirement>

    <requirement id="req-py-04">
      <title>代码风格要求</title>
      <description>必须通过 black、isort、pylint 检查</description>
      <enforcement>pre-commit hook 自动检查</enforcement>
    </requirement>

    <requirement id="req-py-05">
      <title>文档字符串要求</title>
      <description>所有公共函数和类必须有详细的文档字符串</description>
      <enforcement>pylint docstring 检查</enforcement>
    </requirement>

    <requirement id="req-py-06">
      <title>PEP 规范遵循</title>
      <description>严格遵守 PEP 8 和相关 PEP 规范</description>
      <enforcement>flake8 + black 自动格式化</enforcement>
    </requirement>

    <requirement id="req-py-07">
      <title>异步代码规范</title>
      <description>异步函数必须使用 async/await 语法，不使用回调</description>
      <enforcement>代码审查检查</enforcement>
    </requirement>
  </mandatory_requirements>

  <!-- ==================== 注意事项 ==================== -->
  <notes_and_warnings>
    <note category="language_features">
      <title>充分利用 Python 特性</title>
      <items>
        <item>使用语法糖和内置函数（列表推导、生成器、上下文管理器）</item>
        <item>遵循"Pythonic"编程风格</item>
        <item>优先使用标准库，谨慎选择第三方依赖</item>
      </items>
    </note>

    <note category="performance">
      <title>性能注意事项</title>
      <items>
        <item>注意 Python 的 GIL 限制，CPU 密集型任务考虑多进程</item>
        <item>合理使用异步编程，避免阻塞操作</item>
        <item>性能关键路径可考虑使用 Cython 或 Rust 扩展</item>
      </items>
    </note>

    <note category="security">
      <title>安全性</title>
      <items>
        <item>定期更新依赖包，关注安全漏洞</item>
        <item>使用 safety 或 pip-audit 进行依赖安全扫描</item>
        <item>避免使用 eval() 和 exec()</item>
        <item>敏感信息使用环境变量，不要硬编码</item>
      </items>
    </note>
  </notes_and_warnings>

</claude_rules>
