# CLAUDE - PHP 开发规范

This file provides guidance to Claude Code (claude.ai/code) when working with PHP code in this repository.

### 第一部分：核心编程原则 (Guiding Principles)
这是我们合作的顶层思想，指导所有具体的行为。

#### 基础设计原则
- **可读性优先 (Readability First)：** 遵循 PHP-FIG 标准，编写清晰、一致的代码，使其他开发者能够轻松理解和维护。
- **DRY (Don't Repeat Yourself)：** 通过类、接口、特性和组合模式来消除重复，充分利用 PHP 的面向对象特性。
- **高内聚，低耦合 (High Cohesion, Low Coupling)：** 利用 PHP 的命名空间和自动加载系统实现清晰的代码组织。

#### DDD + TDD 融合开发方法论
- **领域驱动设计 (Domain-Driven Design)：** 采用 Domain Model 并结合 PHP 的强类型（PHP 7+），以业务领域为核心组织代码结构。
- **测试驱动开发 (Test-Driven Development)：** 使用 PHPUnit 框架，每完成一个功能模块就立即编写相应的测试。
- **渐进式开发策略：** 利用 PHP 的即时编译特性，快速验证实现。
- **领域边界清晰：** 通过 PHP 命名空间系统明确 domain 关系对应。
- **AI 辅助质量保障：** 结合 AI 工具和 PHP 的静态分析工具（PHPStan、Psalm）提升代码质量。

### 第二部分：具体执行指令 (Actionable Instructions)
这是 Claude 在 PHP 开发中需要严格遵守的具体操作指南。

#### 沟通与语言规范
- **默认语言：** 请默认使用简体中文进行所有交流、解释和思考过程的陈述。
- **代码与术语：** 所有代码实体（变量名、函数名、类名等）及技术术语必须保持英文原文。
- **注释规范：** 代码注释应使用中文，遵循 PHPDoc 注释规范。
- **类型声明强制要求：** 所有函数和方法必须添加返回类型注解和参数类型声明，提高代码可读性和IDE支持。

#### 批判性反馈与破框思维
- **审慎分析：** 必须以审视和批判的眼光分析输入，主动识别潜在的问题和 PHP 最佳实践违背。
- **坦率直言：** 指出非 PHP 惯用的代码模式，推荐更优雅的 PHP 解决方案。
- **严厉质询：** 对于违背 PSR 规范或 PHP 最佳实践的代码，必须明确指出并提供改进建议。

#### 开发与调试策略 (Development & Debugging Strategy)

##### 问题解决策略
- **坚韧不拔的解决问题：** 充分利用 PHP 的调试工具（Xdebug、PHP Debug Bar）和错误日志系统进行问题定位。
- **逐个击破：** 使用 PHP 的交互式命令行工具（PsySH）逐步验证每个组件的功能。
- **探索有效替代方案：** 优先考虑 PHP 标准库和成熟的第三方库解决方案。
- **禁止伪造实现：** 严禁使用空方法或占位符作为功能实现，所有代码必须具备真实逻辑。

##### 测试驱动开发 (TDD) 规范
- **PHPUnit 优先原则：** 使用 PHPUnit 作为主要测试框架，充分利用其断言和数据提供者功能。
- **Red-Green-Refactor 循环：** 
  1. **Red（红）：** 编写失败的测试用例，使用 PHPUnit 的异常断言验证错误处理
  2. **Green（绿）：** 编写最少的代码使测试通过
  3. **Refactor（重构）：** 利用 PHP 的现代特性优化代码
- **测试覆盖率：** 使用 PHPUnit 的覆盖率报告确保测试覆盖率达到 90% 以上
- **文档化测试：** 在 PHPDoc 中包含可执行的示例代码

##### AI 辅助开发指导原则
- **静态分析集成：** 结合 PHPStan、Psalm、PHP-CS-Fixer 等工具确保代码质量
- **IDE 增强：** 利用 AI 辅助的代码补全和重构建议
- **代码审查：** 对 AI 生成的代码进行 PSR 标准检查

#### 依赖管理
- **Composer 必备：** 所有 PHP 项目**必须**使用 Composer 进行依赖管理
- **环境管理：** 
  ```bash
  # 创建项目
  composer create-project vendor/package-name
  
  # 安装依赖
  composer install
  
  # 添加新依赖
  composer require vendor/package-name
  ```
- **自动加载：** 严格遵循 PSR-4 自动加载规范
- **版本锁定：** 始终包含 composer.lock 文件，确保环境一致性

#### 项目与代码维护
- **PSR 规范遵循：** 严格遵守 PSR-1、PSR-2、PSR-12 代码风格规范
- **文档注释：** 遵循 PHPDoc 标准，为所有公共 API 编写详细的文档注释
- **导入管理：** 使用完整的命名空间或使用合理的 use 语句
- **及时清理：** 定期清理未使用的导入、变量和方法

## 常用命令

### 环境管理
```bash
# 安装 Composer
# 访问 https://getcomposer.org/ 下载或运行以下命令
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php composer-setup.php
php -r "unlink('composer-setup.php');"

# 全局安装 Composer (可选)
move composer.phar /usr/local/bin/composer  # Linux/macOS
move composer.phar C:\bin\composer.phar      # Windows

# 初始化新项目
composer init

# 安装依赖
composer install

# 添加新依赖
composer require symfony/framework-bundle

# 添加开发依赖
composer require --dev phpunit/phpunit
```

### 测试和质量检查
```bash
# 运行测试
./vendor/bin/phpunit

# 运行测试并生成覆盖率报告
./vendor/bin/phpunit --coverage-html coverage

# 静态分析 (PHPStan)
./vendor/bin/phpstan analyze src

# 代码风格检查 (PHP-CS-Fixer)
./vendor/bin/php-cs-fixer fix --dry-run --diff

# 自动修复代码风格
./vendor/bin/php-cs-fixer fix

# PSR 兼容性检查 (PHP CodeSniffer)
./vendor/bin/phpcs --standard=PSR12 src
```

## 项目架构概览

### 推荐项目结构
```
project/
├── src/
│   ├── Domain/          # 领域模型
│   │   ├── Entities/    # 实体类
│   │   ├── ValueObjects/ # 值对象
│   │   ├── Services/    # 领域服务
│   │   └── Repositories/ # 仓储接口
│   ├── Application/     # 应用层
│   │   ├── UseCases/    # 用例
│   │   ├── DTOs/        # 数据传输对象
│   │   └── Services/    # 应用服务
│   ├── Infrastructure/  # 基础设施层
│   │   ├── Persistence/  # 仓储实现
│   │   ├── Adapters/    # 适配器
│   │   └── External/    # 外部系统集成
│   └── Presentation/    # 表示层
│       ├── Controllers/ # 控制器
│       ├── Middleware/  # 中间件
│       ├── Requests/    # 请求验证
│       └── Responses/   # 响应格式化
├── tests/
│   ├── Unit/           # 单元测试
│   ├── Integration/    # 集成测试
│   └── Feature/        # 功能测试
├── config/             # 配置文件
├── public/             # 公共入口目录
├── resources/          # 资源文件
├── storage/            # 存储目录
├── composer.json       # Composer 配置
├── composer.lock       # 依赖版本锁定
└── README.md           # 项目说明
```

### 技术栈推荐
- **Web 框架：** Symfony / Laravel / Laminas
- **ORM：** Doctrine / Eloquent
- **测试框架：** PHPUnit + Mockery
- **依赖注入：** PHP-DI / 框架内置容器
- **静态分析：** PHPStan / Psalm
- **代码格式化：** PHP-CS-Fixer
- **API 文档：** Swagger/OpenAPI (with zircote/swagger-php)
- **缓存：** Redis / Memcached

## 开发指南

### 领域驱动设计 (DDD) 实施指南

#### 领域模型组织
```php
<?php

namespace App\Domain\Entities;

use Ramsey\Uuid\Uuid;

class User
{
    /**
     * @var string
     */
    private $id;

    /**
     * @var string
     */
    private $name;

    /**
     * @var string
     */
    private $email;

    /**
     * User constructor.
     * @param string $name
     * @param string $email
     */
    public function __construct(string $name, string $email)
    {
        $this->id = Uuid::uuid4()->toString();
        $this->name = $name;
        $this->email = $email;
    }

    /**
     * @return string
     */
    public function getId(): string
    {
        return $this->id;
    }

    /**
     * @return string
     */
    public function getName(): string
    {
        return $this->name;
    }

    /**
     * @return string
     */
    public function getEmail(): string
    {
        return $this->email;
    }

    /**
     * 更改用户邮箱
     * @param string $newEmail
     * @throws \InvalidArgumentException
     */
    public function changeEmail(string $newEmail): void
    {
        if (!$this->isValidEmail($newEmail)) {
            throw new \InvalidArgumentException('Invalid email format');
        }
        $this->email = $newEmail;
    }

    /**
     * 验证邮箱格式
     * @param string $email
     * @return bool
     */
    private function isValidEmail(string $email): bool
    {
        return filter_var($email, FILTER_VALIDATE_EMAIL) !== false;
    }
}
```

#### 值对象设计
```php
<?php

namespace App\Domain\ValueObjects;

class Money
{
    /**
     * @var float
     */
    private $amount;

    /**
     * @var string
     */
    private $currency;

    /**
     * Money constructor.
     * @param float $amount
     * @param string $currency
     * @throws \InvalidArgumentException
     */
    public function __construct(float $amount, string $currency = 'USD')
    {
        if ($amount < 0) {
            throw new \InvalidArgumentException('Amount cannot be negative');
        }
        
        $this->amount = $amount;
        $this->currency = $currency;
    }

    /**
     * @return float
     */
    public function getAmount(): float
    {
        return $this->amount;
    }

    /**
     * @return string
     */
    public function getCurrency(): string
    {
        return $this->currency;
    }

    /**
     * 相加两个相同币种的金额
     * @param Money $other
     * @return Money
     * @throws \InvalidArgumentException
     */
    public function add(Money $other): Money
    {
        if ($this->currency !== $other->currency) {
            throw new \InvalidArgumentException('Cannot add different currencies');
        }
        
        return new Money(
            $this->amount + $other->amount,
            $this->currency
        );
    }

    /**
     * 比较两个金额是否相等
     * @param Money $other
     * @return bool
     */
    public function equals(Money $other): bool
    {
        return $this->currency === $other->currency && 
               $this->amount === $other->amount;
    }
}
```

#### 仓储模式
```php
<?php

namespace App\Domain\Repositories;

use App\Domain\Entities\User;

interface UserRepository
{
    /**
     * 保存用户
     * @param User $user
     */
    public function save(User $user): void;

    /**
     * 根据ID查找用户
     * @param string $id
     * @return User|null
     */
    public function findById(string $id): ?User;

    /**
     * 根据邮箱查找用户
     * @param string $email
     * @return User|null
     */
    public function findByEmail(string $email): ?User;

    /**
     * 查找所有用户
     * @return User[]
     */
    public function findAll(): array;

    /**
     * 删除用户
     * @param User $user
     */
    public function delete(User $user): void;
}
```

#### 仓储实现示例
```php
<?php

namespace App\Infrastructure\Persistence;

use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepository;
use Doctrine\ORM\EntityManagerInterface;

class DoctrineUserRepository implements UserRepository
{
    /**
     * @var EntityManagerInterface
     */
    private $entityManager;

    /**
     * DoctrineUserRepository constructor.
     * @param EntityManagerInterface $entityManager
     */
    public function __construct(EntityManagerInterface $entityManager)
    {
        $this->entityManager = $entityManager;
    }

    /**
     * @inheritDoc
     */
    public function save(User $user): void
    {
        $this->entityManager->persist($user);
        $this->entityManager->flush();
    }

    /**
     * @inheritDoc
     */
    public function findById(string $id): ?User
    {
        return $this->entityManager->find(User::class, $id);
    }

    /**
     * @inheritDoc
     */
    public function findByEmail(string $email): ?User
    {
        return $this->entityManager
            ->getRepository(User::class)
            ->findOneBy(['email' => $email]);
    }

    /**
     * @inheritDoc
     */
    public function findAll(): array
    {
        return $this->entityManager
            ->getRepository(User::class)
            ->findAll();
    }

    /**
     * @inheritDoc
     */
    public function delete(User $user): void
    {
        $this->entityManager->remove($user);
        $this->entityManager->flush();
    }
}
```

### 测试驱动开发最佳实践

#### 单元测试示例
```php
<?php

namespace Tests\Unit\Domain\Entities;

use App\Domain\Entities\User;
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    /**
     * 测试用户创建
     */
    public function testUserCreation()
    {
        $user = new User('张三', 'zhangsan@example.com');
        
        $this->assertNotNull($user->getId());
        $this->assertEquals('张三', $user->getName());
        $this->assertEquals('zhangsan@example.com', $user->getEmail());
    }
    
    /**
     * 测试邮箱更改成功
     */
    public function testChangeEmailSuccess()
    {
        $user = new User('张三', 'zhangsan@example.com');
        $user->changeEmail('new@example.com');
        
        $this->assertEquals('new@example.com', $user->getEmail());
    }
    
    /**
     * 测试邮箱格式无效
     */
    public function testChangeEmailInvalidFormat()
    {
        $this->expectException(\InvalidArgumentException::class);
        $this->expectExceptionMessage('Invalid email format');
        
        $user = new User('张三', 'zhangsan@example.com');
        $user->changeEmail('invalid-email');
    }
}
```

#### 值对象测试示例
```php
<?php

namespace Tests\Unit\Domain\ValueObjects;

use App\Domain\ValueObjects\Money;
use PHPUnit\Framework\TestCase;

class MoneyTest extends TestCase
{
    /**
     * 测试金额创建
     */
    public function testMoneyCreation()
    {
        $money = new Money(100.0, 'USD');
        
        $this->assertEquals(100.0, $money->getAmount());
        $this->assertEquals('USD', $money->getCurrency());
    }
    
    /**
     * 测试默认货币为USD
     */
    public function testDefaultCurrencyIsUSD()
    {
        $money = new Money(100.0);
        $this->assertEquals('USD', $money->getCurrency());
    }
    
    /**
     * 测试负值金额抛出异常
     */
    public function testNegativeAmountThrowsException()
    {
        $this->expectException(\InvalidArgumentException::class);
        $this->expectExceptionMessage('Amount cannot be negative');
        
        new Money(-50.0, 'USD');
    }
    
    /**
     * 测试相同币种相加
     */
    public function testAddSameCurrency()
    {
        $money1 = new Money(100.0, 'USD');
        $money2 = new Money(50.0, 'USD');
        $result = $money1->add($money2);
        
        $this->assertEquals(150.0, $result->getAmount());
        $this->assertEquals('USD', $result->getCurrency());
    }
    
    /**
     * 测试不同币种相加抛出异常
     */
    public function testAddDifferentCurrenciesThrowsException()
    {
        $this->expectException(\InvalidArgumentException::class);
        $this->expectExceptionMessage('Cannot add different currencies');
        
        $money1 = new Money(100.0, 'USD');
        $money2 = new Money(50.0, 'EUR');
        $money1->add($money2);
    }
    
    /**
     * 测试金额相等比较
     */
    public function testEquals()
    {
        $money1 = new Money(100.0, 'USD');
        $money2 = new Money(100.0, 'USD');
        $money3 = new Money(50.0, 'USD');
        $money4 = new Money(100.0, 'EUR');
        
        $this->assertTrue($money1->equals($money2));
        $this->assertFalse($money1->equals($money3));
        $this->assertFalse($money1->equals($money4));
    }
}
```

### PHP 特有编程模式与最佳实践

#### 类型安全
```php
<?php

// 推荐使用强类型声明
function calculateTotal(int $quantity, float $price): float
{
    return $quantity * $price;
}

// 使用空合并运算符处理可选参数
function greet(string $name, string $greeting = null): string
{
    $greeting = $greeting ?? 'Hello';
    return "$greeting, $name!";
}

// 使用类型检查和错误处理
function processUserData(array $userData): bool
{
    try {
        // 验证类型
        if (!isset($userData['id']) || !is_int($userData['id'])) {
            throw new \InvalidArgumentException('User ID must be an integer');
        }
        
        // 处理数据
        return true;
    } catch (\Exception $e) {
        // 记录错误
        error_log('Error processing user data: ' . $e->getMessage());
        return false;
    }
}
```

#### 依赖注入
```php
<?php

namespace App\Application\Services;

use App\Domain\Repositories\UserRepository;
use App\Domain\Entities\User;

class UserService
{
    /**
     * @var UserRepository
     */
    private $userRepository;

    /**
     * UserService constructor.
     * @param UserRepository $userRepository
     */
    public function __construct(UserRepository $userRepository)
    {
        $this->userRepository = $userRepository;
    }

    /**
     * 创建用户
     * @param string $name
     * @param string $email
     * @return User
     * @throws \InvalidArgumentException
     */
    public function createUser(string $name, string $email): User
    {
        // 检查邮箱是否已存在
        if ($this->userRepository->findByEmail($email) !== null) {
            throw new \InvalidArgumentException('Email already exists');
        }
        
        $user = new User($name, $email);
        $this->userRepository->save($user);
        
        return $user;
    }
}
```

#### 接口与抽象类
```php
<?php

// 定义接口
interface LoggerInterface
{
    public function log(string $message, string $level = 'info'): void;
}

// 实现接口
class FileLogger implements LoggerInterface
{
    /**
     * @var string
     */
    private $logFile;

    /**
     * FileLogger constructor.
     * @param string $logFile
     */
    public function __construct(string $logFile)
    {
        $this->logFile = $logFile;
    }

    /**
     * @inheritDoc
     */
    public function log(string $message, string $level = 'info'): void
    {
        $timestamp = date('Y-m-d H:i:s');
        $logMessage = "[$timestamp] [$level] $message\n";
        file_put_contents($this->logFile, $logMessage, FILE_APPEND);
    }
}

// 抽象类示例
abstract class BaseService
{
    /**
     * @var LoggerInterface
     */
    protected $logger;

    /**
     * BaseService constructor.
     * @param LoggerInterface $logger
     */
    public function __construct(LoggerInterface $logger)
    {
        $this->logger = $logger;
    }

    /**
     * 记录操作
     * @param string $action
     * @param array $data
     */
    protected function logOperation(string $action, array $data = []): void
    {
        $dataString = json_encode($data);
        $this->logger->log("$action: $dataString");
    }
}
```

### 性能优化策略

1. **使用适当的数据结构**：根据操作特点选择合适的数据结构
2. **延迟加载**：使用 PHP 的 lazy loading 特性，按需加载对象
3. **缓存策略**：合理使用缓存减少数据库查询
4. **避免重复计算**：使用 memoization 模式缓存计算结果
5. **数据库优化**：使用索引，避免 N+1 查询问题
6. **代码优化**：避免在循环中进行昂贵操作，使用 PHP 内置函数而非自定义函数完成标准操作

### 安全最佳实践

1. **输入验证**：所有用户输入必须经过严格验证
2. **输出转义**：所有输出到浏览器的数据必须进行适当转义
3. **参数化查询**：使用预处理语句防止 SQL 注入
4. **防止 XSS 攻击**：使用 htmlspecialchars() 处理输出内容
5. **防止 CSRF 攻击**：实现 CSRF 令牌验证
6. **密码安全**：使用 password_hash() 和 password_verify() 处理密码
7. **最小权限原则**：应用程序只应拥有完成其任务所需的最小权限
8. **错误处理**：生产环境不暴露详细错误信息
9. **依赖审计**：定期检查和更新依赖以修复安全漏洞

### 代码示例：安全处理用户输入
```php
<?php

namespace App\Presentation\Controllers;

use App\Application\Services\UserService;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\HttpFoundation\Response;

class UserController
{
    /**
     * @var UserService
     */
    private $userService;

    /**
     * UserController constructor.
     * @param UserService $userService
     */
    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    /**
     * 创建用户
     * @param Request $request
     * @return Response
     */
    public function create(Request $request): Response
    {
        try {
            // 获取并验证输入
            $name = $request->request->get('name');
            $email = $request->request->get('email');
            
            // 基本验证
            if (empty($name) || empty($email)) {
                return new Response('Missing required fields', Response::HTTP_BAD_REQUEST);
            }
            
            // 使用服务层创建用户
            $user = $this->userService->createUser($name, $email);
            
            // 返回安全的响应
            $response = [
                'id' => htmlspecialchars($user->getId()),
                'name' => htmlspecialchars($user->getName()),
                'email' => htmlspecialchars($user->getEmail())
            ];
            
            return new Response(
                json_encode($response),
                Response::HTTP_CREATED,
                ['Content-Type' => 'application/json']
            );
        } catch (\InvalidArgumentException $e) {
            return new Response($e->getMessage(), Response::HTTP_BAD_REQUEST);
        } catch (\Exception $e) {
            // 记录详细错误但返回通用消息
            error_log('Error creating user: ' . $e->getMessage());
            return new Response('An error occurred', Response::HTTP_INTERNAL_SERVER_ERROR);
        }
    }
}
```