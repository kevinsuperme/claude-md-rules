# DDD + TDD 融合开发方法论（共享模块）

<ddd_tdd_methodology>
  <overview>
    本方法论将领域驱动设计（DDD）与测试驱动开发（TDD）深度融合，
    结合 AI 辅助开发的现代实践，提供一套系统化的软件开发方法。
  </overview>

  <!-- ==================== 核心设计原则 ==================== -->
  <core_design_principles>
    <principle name="readability_first">
      <title>可读性优先 (Readability First)</title>
      <description>
        始终牢记"代码是写给人看的，只是恰好机器可以执行"。清晰度高于一切。
      </description>
      <guidelines>
        <guideline>使用清晰、描述性的命名</guideline>
        <guideline>每个函数只做一件事</guideline>
        <guideline>避免深层嵌套和复杂的逻辑表达式</guideline>
        <guideline>添加必要的注释说明意图，而非实现细节</guideline>
      </guidelines>
    </principle>

    <principle name="dry">
      <title>DRY (Don't Repeat Yourself)</title>
      <description>
        绝不复制代码片段。通过抽象（如函数、类、模块）来封装和复用通用逻辑。
      </description>
      <guidelines>
        <guideline>识别重复的代码模式</guideline>
        <guideline>提取共享逻辑到独立的函数或类</guideline>
        <guideline>使用配置文件管理重复的配置项</guideline>
        <guideline>利用继承和组合复用代码</guideline>
      </guidelines>
    </principle>

    <principle name="high_cohesion_low_coupling">
      <title>高内聚，低耦合 (High Cohesion, Low Coupling)</title>
      <description>
        功能高度相关的代码应该放在一起（高内聚），而模块之间应尽量减少依赖（低耦合），
        以增强模块独立性和可维护性。
      </description>
      <guidelines>
        <guideline>每个模块有清晰的职责边界</guideline>
        <guideline>通过接口定义模块间的交互</guideline>
        <guideline>依赖注入而非硬编码依赖</guideline>
        <guideline>避免循环依赖</guideline>
      </guidelines>
    </principle>
  </core_design_principles>

  <!-- ==================== 领域驱动设计 (DDD) ==================== -->
  <domain_driven_design>
    <overview>
      采用 Domain Model（领域模型）并结合 SOLID 设计原则，
      以业务领域为核心组织代码结构。
    </overview>

    <key_concepts>
      <concept name="bounded_context">
        <title>限界上下文 (Bounded Context)</title>
        <description>
          明确定义每个领域的边界，避免概念混淆和职责不清。
        </description>
        <implementation>
          通过模块、包或命名空间划分不同的领域上下文
        </implementation>
      </concept>

      <concept name="ubiquitous_language">
        <title>统一语言 (Ubiquitous Language)</title>
        <description>
          技术团队与业务团队使用相同的术语，确保代码反映业务概念。
        </description>
        <implementation>
          代码中的类名、方法名应直接对应业务术语
        </implementation>
      </concept>

      <concept name="entity">
        <title>实体 (Entity)</title>
        <description>
          具有唯一标识的领域对象，其身份在整个生命周期中保持不变。
        </description>
        <characteristics>
          <characteristic>有唯一标识符（如 ID）</characteristic>
          <characteristic>可变性：属性可以改变，但身份不变</characteristic>
          <characteristic>生命周期：可能经历多个状态转换</characteristic>
        </characteristics>
      </concept>

      <concept name="value_object">
        <title>值对象 (Value Object)</title>
        <description>
          没有唯一标识，通过属性值来定义相等性的不可变对象。
        </description>
        <characteristics>
          <characteristic>不可变性：一旦创建不能修改</characteristic>
          <characteristic>值相等性：两个值对象属性相同则相等</characteristic>
          <characteristic>无副作用：方法不改变对象状态</characteristic>
        </characteristics>
      </concept>

      <concept name="aggregate">
        <title>聚合 (Aggregate)</title>
        <description>
          一组相关对象的集合，作为数据修改的单元，由聚合根管理。
        </description>
        <guidelines>
          <guideline>每个聚合有一个聚合根</guideline>
          <guideline>外部只能通过聚合根访问聚合内部对象</guideline>
          <guideline>聚合边界内的修改保证一致性</guideline>
        </guidelines>
      </concept>

      <concept name="repository">
        <title>仓储 (Repository)</title>
        <description>
          封装数据访问逻辑，提供类似集合的接口来操作聚合。
        </description>
        <responsibilities>
          <responsibility>持久化和检索聚合</responsibility>
          <responsibility>隐藏底层数据存储细节</responsibility>
          <responsibility>提供查询接口</responsibility>
        </responsibilities>
      </concept>

      <concept name="domain_service">
        <title>领域服务 (Domain Service)</title>
        <description>
          不自然属于任何实体或值对象的业务逻辑，封装在领域服务中。
        </description>
        <when_to_use>
          <scenario>操作涉及多个聚合</scenario>
          <scenario>业务逻辑不属于任何特定实体</scenario>
          <scenario>需要协调多个领域对象的交互</scenario>
        </when_to_use>
      </concept>
    </key_concepts>

    <layered_architecture>
      <layer name="interfaces">
        <title>接口层 (Interfaces / Presentation Layer)</title>
        <responsibilities>
          <responsibility>处理用户请求</responsibility>
          <responsibility>数据验证和转换</responsibility>
          <responsibility>调用应用层服务</responsibility>
        </responsibilities>
      </layer>

      <layer name="application">
        <title>应用层 (Application Layer)</title>
        <responsibilities>
          <responsibility>协调领域对象完成用例</responsibility>
          <responsibility>事务管理</responsibility>
          <responsibility>权限验证</responsibility>
        </responsibilities>
      </layer>

      <layer name="domain">
        <title>领域层 (Domain Layer)</title>
        <responsibilities>
          <responsibility>包含业务逻辑和规则</responsibility>
          <responsibility>定义实体、值对象、聚合</responsibility>
          <responsibility>实现领域服务</responsibility>
        </responsibilities>
      </layer>

      <layer name="infrastructure">
        <title>基础设施层 (Infrastructure Layer)</title>
        <responsibilities>
          <responsibility>数据持久化</responsibility>
          <responsibility>外部服务集成</responsibility>
          <responsibility>技术支持（日志、缓存等）</responsibility>
        </responsibilities>
      </layer>
    </layered_architecture>
  </domain_driven_design>

  <!-- ==================== 测试驱动开发 (TDD) ==================== -->
  <test_driven_development>
    <overview>
      每完成一个功能模块就立即编写相应的测试，确保代码质量和稳定性。
    </overview>

    <red_green_refactor_cycle>
      <title>Red-Green-Refactor 循环</title>
      <description>
        TDD 的核心工作流程，通过三个阶段的循环迭代开发。
      </description>

      <phase name="red">
        <title>Red（红）</title>
        <objective>先编写一个失败的测试用例</objective>
        <steps>
          <step>明确要实现的功能需求</step>
          <step>编写测试用例，描述期望的行为</step>
          <step>运行测试，确认测试失败（红色）</step>
          <step>失败原因应该是功能未实现，而非测试错误</step>
        </steps>
        <best_practices>
          <practice>测试用例应清晰表达业务需求</practice>
          <practice>一次只测试一个功能点</practice>
          <practice>使用描述性的测试名称</practice>
        </best_practices>
      </phase>

      <phase name="green">
        <title>Green（绿）</title>
        <objective>编写最少的代码使测试通过</objective>
        <steps>
          <step>实现能让测试通过的最简单代码</step>
          <step>不考虑代码优雅性，只关注功能实现</step>
          <step>运行测试，确认测试通过（绿色）</step>
          <step>确保所有已有测试也继续通过</step>
        </steps>
        <best_practices>
          <practice>避免过度设计和实现未测试的功能</practice>
          <practice>YAGNI 原则：You Aren't Gonna Need It</practice>
          <practice>快速迭代，小步前进</practice>
        </best_practices>
      </phase>

      <phase name="refactor">
        <title>Refactor（重构）</title>
        <objective>在保持测试通过的前提下优化代码结构</objective>
        <steps>
          <step>识别代码中的坏味道（Code Smells）</step>
          <step>应用重构技术改进代码</step>
          <step>持续运行测试，确保重构不破坏功能</step>
          <step>优化命名、提取函数、消除重复</step>
        </steps>
        <best_practices>
          <practice>每次重构后立即运行测试</practice>
          <practice>小步重构，避免大规模修改</practice>
          <practice>应用设计模式和SOLID原则</practice>
        </best_practices>
      </phase>
    </red_green_refactor_cycle>

    <testing_pyramid>
      <title>测试金字塔</title>
      <level name="unit_tests">
        <title>单元测试 (Unit Tests)</title>
        <proportion>70%</proportion>
        <characteristics>
          <characteristic>测试单个函数或类</characteristic>
          <characteristic>快速执行</characteristic>
          <characteristic>隔离外部依赖</characteristic>
        </characteristics>
      </level>

      <level name="integration_tests">
        <title>集成测试 (Integration Tests)</title>
        <proportion>20%</proportion>
        <characteristics>
          <characteristic>测试模块间交互</characteristic>
          <characteristic>包含数据库、API等外部依赖</characteristic>
          <characteristic>执行速度中等</characteristic>
        </characteristics>
      </level>

      <level name="e2e_tests">
        <title>端到端测试 (E2E Tests)</title>
        <proportion>10%</proportion>
        <characteristics>
          <characteristic>测试完整用户流程</characteristic>
          <characteristic>模拟真实环境</characteristic>
          <characteristic>执行速度慢</characteristic>
        </characteristics>
      </level>
    </testing_pyramid>

    <test_quality_standards>
      <standard name="coverage">
        <title>测试覆盖率</title>
        <requirements>
          <requirement>核心业务逻辑：90% 以上</requirement>
          <requirement>工具类和辅助函数：80% 以上</requirement>
          <requirement>整体代码覆盖率：75% 以上</requirement>
        </requirements>
      </standard>

      <standard name="independence">
        <title>测试独立性</title>
        <requirements>
          <requirement>测试间无依赖关系</requirement>
          <requirement>可任意顺序执行</requirement>
          <requirement>每个测试有独立的设置和清理</requirement>
        </requirements>
      </standard>

      <standard name="repeatability">
        <title>测试可重复性</title>
        <requirements>
          <requirement>相同输入产生相同结果</requirement>
          <requirement>不依赖外部状态</requirement>
          <requirement>避免时间、随机数等不确定因素</requirement>
        </requirements>
      </standard>
    </test_quality_standards>
  </test_driven_development>

  <!-- ==================== 渐进式开发策略 ==================== -->
  <progressive_development>
    <overview>
      每写一个单元就进行一轮测试，避免后期全局修改和发现系统性问题。
    </overview>

    <strategies>
      <strategy name="incremental_implementation">
        <title>增量式实现</title>
        <steps>
          <step>从最小可用功能开始</step>
          <step>逐步添加新特性</step>
          <step>每个增量都经过完整测试</step>
          <step>保持代码始终可运行</step>
        </steps>
      </strategy>

      <strategy name="continuous_integration">
        <title>持续集成</title>
        <practices>
          <practice>频繁提交代码到主分支</practice>
          <practice>每次提交触发自动化测试</practice>
          <practice>快速反馈构建和测试结果</practice>
          <practice>保持主分支始终可部署</practice>
        </practices>
      </strategy>

      <strategy name="refactoring_discipline">
        <title>重构纪律</title>
        <principles>
          <principle>小步重构，频繁提交</principle>
          <principle>重构前确保测试全部通过</principle>
          <principle>重构后立即运行测试验证</principle>
          <principle>不在重构时添加新功能</principle>
        </principles>
      </strategy>
    </strategies>
  </progressive_development>

  <!-- ==================== AI 辅助开发指导原则 ==================== -->
  <ai_assisted_development>
    <overview>
      结合 AI 工具提升软件设计和技术管理的标准，
      但核心的设计决策和质量把控仍需人工判断。
    </overview>

    <ai_tool_positioning>
      <title>AI 工具定位</title>
      <description>
        AI 应作为开发效率的倍增器，而非替代开发者的决策能力。
        AI 擅长代码生成、重构建议和错误检测，但核心的架构设计和业务逻辑判断仍需人工把控。
      </description>
    </ai_tool_positioning>

    <quality_standards_enhancement>
      <title>质量标准提升</title>
      <requirements>
        <requirement>使用 AI 辅助开发要求更高的软件设计和技术管理标准</requirement>
        <requirement>AI 生成的代码需要更严格的审查和验证</requirement>
        <requirement>建立 AI 代码的审查检查清单</requirement>
      </requirements>
    </quality_standards_enhancement>

    <code_review_intensification>
      <title>代码审查强化</title>
      <focus_areas>
        <area>业务逻辑的正确性</area>
        <area>安全性（输入验证、权限检查）</area>
        <area>性能表现（算法复杂度、资源使用）</area>
        <area>代码可维护性（命名、结构、注释）</area>
        <area>测试覆盖度和测试质量</area>
      </focus_areas>
    </code_review_intensification>

    <continuous_learning>
      <title>持续学习</title>
      <practices>
        <practice>从 AI 的建议中学习最佳实践</practice>
        <practice>教会 AI 项目特定的约定和模式</practice>
        <practice>建立和维护项目的编码规范文档</practice>
        <practice>定期审查和更新 AI 提示模板</practice>
      </practices>
    </continuous_learning>

    <toolchain_integration>
      <title>工具链整合</title>
      <integration_points>
        <point>代码补全和智能提示</point>
        <point>自动化测试生成</point>
        <point>文档自动编写</point>
        <point>代码重构建议</point>
        <point>Bug 检测和修复建议</point>
      </integration_points>
    </toolchain_integration>
  </ai_assisted_development>

  <!-- ==================== 领域边界清晰化 ==================== -->
  <clear_domain_boundaries>
    <overview>
      通过明确的 domain 关系对应，减少设计偏差和架构混乱。
    </overview>

    <boundary_definition_guidelines>
      <guideline>每个领域模块有明确的职责范围</guideline>
      <guideline>跨领域交互通过明确定义的接口</guideline>
      <guideline>避免领域概念泄漏到其他层</guideline>
      <guideline>使用上下文映射管理领域间关系</guideline>
    </boundary_definition_guidelines>

    <context_mapping_patterns>
      <pattern name="shared_kernel">
        <title>共享内核 (Shared Kernel)</title>
        <description>两个上下文共享部分领域模型</description>
      </pattern>

      <pattern name="customer_supplier">
        <title>客户-供应商 (Customer-Supplier)</title>
        <description>上游团队为下游团队提供服务</description>
      </pattern>

      <pattern name="anticorruption_layer">
        <title>防腐层 (Anticorruption Layer)</title>
        <description>隔离外部系统，避免其影响内部模型</description>
      </pattern>
    </context_mapping_patterns>
  </clear_domain_boundaries>
</ddd_tdd_methodology>
