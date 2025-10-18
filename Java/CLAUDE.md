<?xml version="1.0" encoding="UTF-8"?>
<!--
  CLAUDE.md - Java 开发规范
  Version: 2.0 (XML-Structured)
  Last Updated: 2025-10-19

  This file provides structured guidance to Claude Code (claude.ai/code)
  when working with Java code in this repository.

  Based on Claude Prompt Engineering Best Practices:
  - XML structure for clear boundaries
  - Modular design with shared components
  - Chain-of-thought reasoning support
  - Few-shot examples for accuracy
-->

<claude_rules version="2.0" language="Java">

  <!-- ==================== 元数据 ==================== -->
  <metadata>
    <language>Java</language>
    <language_version>17+</language_version>
    <framework_ecosystem>
      <primary>Spring Boot 3.5.0</primary>
      <orm>MyBatis Plus 3.5.12</orm>
      <security>Sa-Token 1.43.0</security>
      <utilities>Hutool 5.8.38, Guava 33.4.8</utilities>
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

  <!-- ==================== 核心编程原则（Java 特定） ==================== -->
  <guiding_principles language="Java">
    <overview>
      Java 企业级开发遵循 SOLID 原则，强调类型安全、接口清晰和架构稳定性。
    </overview>

    <principle name="solid_principles" priority="critical">
      <title>SOLID 设计原则</title>
      <description>
        Java 开发的核心设计原则，确保代码的可维护性和可扩展性。
      </description>

      <solid_breakdown>
        <principle id="S" name="Single Responsibility">
          <title>单一职责原则 (Single Responsibility Principle)</title>
          <definition>一个类应该只有一个引起它变化的原因</definition>
          <example>
            <good>
              <code><![CDATA[
// 好的示例：职责单一
public class UserService {
    private final UserRepository userRepository;

    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }
}

public class UserValidator {
    public void validate(User user) {
        if (user.getEmail() == null) {
            throw new ValidationException("Email is required");
        }
    }
}
              ]]></code>
            </good>
            <bad>
              <code><![CDATA[
// 不好的示例：职责混杂
public class UserService {
    public User findById(Long id) { /* ... */ }
    public void validateUser(User user) { /* ... */ }
    public void sendEmail(User user) { /* ... */ }
    public void generateReport(User user) { /* ... */ }
}
              ]]></code>
            </bad>
          </example>
        </principle>

        <principle id="O" name="Open-Closed">
          <title>开闭原则 (Open-Closed Principle)</title>
          <definition>软件实体应该对扩展开放，对修改封闭</definition>
          <example>
            <good>
              <code><![CDATA[
// 好的示例：通过接口扩展
public interface PaymentStrategy {
    void pay(BigDecimal amount);
}

public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(BigDecimal amount) { /* ... */ }
}

public class AlipayPayment implements PaymentStrategy {
    @Override
    public void pay(BigDecimal amount) { /* ... */ }
}
              ]]></code>
            </good>
          </example>
        </principle>

        <principle id="L" name="Liskov Substitution">
          <title>里氏替换原则 (Liskov Substitution Principle)</title>
          <definition>子类对象应该能够替换其父类对象而不影响程序正确性</definition>
        </principle>

        <principle id="I" name="Interface Segregation">
          <title>接口隔离原则 (Interface Segregation Principle)</title>
          <definition>客户端不应该依赖它不需要的接口</definition>
        </principle>

        <principle id="D" name="Dependency Inversion">
          <title>依赖倒置原则 (Dependency Inversion Principle)</title>
          <definition>高层模块不应该依赖低层模块，两者都应该依赖抽象</definition>
        </principle>
      </solid_breakdown>
    </principle>

    <principle name="constructor_injection" priority="mandatory">
      <title>构造器注入强制规范</title>
      <description>
        所有 ServiceImpl 实现类必须使用 @RequiredArgsConstructor 构造器注入模式，
        禁止使用 @Autowired 字段注入。
      </description>
      <rationale>
        <reason>构造器注入确保依赖不可变性</reason>
        <reason>便于单元测试（无需反射注入）</reason>
        <reason>明确依赖关系，避免循环依赖</reason>
        <reason>支持 final 字段，保证线程安全</reason>
      </rationale>
      <implementation>
        <good>
          <code><![CDATA[
@Service
@RequiredArgsConstructor
public class UserServiceImpl implements UserService {

    private final UserMapper userMapper;
    private final RedisTemplate<String, Object> redisTemplate;

    @Override
    public User getById(Long id) {
        // 业务逻辑
        return userMapper.selectById(id);
    }
}
          ]]></code>
        </good>
        <bad>
          <code><![CDATA[
@Service
public class UserServiceImpl implements UserService {

    @Autowired  // ❌ 禁止字段注入
    private UserMapper userMapper;

    @Autowired  // ❌ 禁止字段注入
    private RedisTemplate<String, Object> redisTemplate;
}
          ]]></code>
        </bad>
      </implementation>
    </principle>

    <principle name="unified_path_prefix" priority="mandatory">
      <title>统一路径前缀规范</title>
      <description>
        所有 Controller 必须使用统一的 /coder 前缀作为 API 根路径。
      </description>
      <path_format>/coder/{模块名}/{功能名}</path_format>
      <examples>
        <example>/coder/education/selection</example>
        <example>/coder/education/offering</example>
        <example>/coder/system/user</example>
      </examples>
      <implementation>
        <code><![CDATA[
@RestController
@RequestMapping("/coder/education/selection")
@RequiredArgsConstructor
public class SysCourseSelectionController {

    private final SysCourseSelectionService courseSelectionService;

    @SaCheckPermission("education:selection:list")
    @GetMapping("/list")
    public Result<List<SysCourseSelection>> list(@Validated CourseSelectionBo bo) {
        return Result.ok(courseSelectionService.list(bo));
    }
}
        ]]></code>
      </implementation>
    </principle>

    <principle name="permission_verification" priority="mandatory">
      <title>权限验证强制规范</title>
      <description>
        所有 Controller 的接口方法都必须配置 @SaCheckPermission 注解。
      </description>
      <permission_naming_convention>
        <format>{模块名}:{功能名}:{操作名}</format>
        <operations>
          <operation name="list">查询列表</operation>
          <operation name="page">分页查询</operation>
          <operation name="detail">查询详情</operation>
          <operation name="add">新增</operation>
          <operation name="edit">修改</operation>
          <operation name="delete">删除</operation>
          <operation name="status">状态变更</operation>
          <operation name="import">导入</operation>
          <operation name="export">导出</operation>
        </operations>
      </permission_naming_convention>
      <examples>
        <example>
          <permission>education:selection:list</permission>
          <description>教育模块 - 选课管理 - 查询列表</description>
        </example>
        <example>
          <permission>system:user:add</permission>
          <description>系统模块 - 用户管理 - 新增用户</description>
        </example>
      </examples>
      <implementation>
        <code><![CDATA[
import cn.dev33.satoken.annotation.SaCheckPermission;

@RestController
@RequestMapping("/coder/system/user")
@RequiredArgsConstructor
public class SysUserController {

    private final SysUserService userService;

    @SaCheckPermission("system:user:list")
    @GetMapping("/list")
    public Result<List<SysUser>> list(@Validated UserQueryBo bo) {
        return Result.ok(userService.list(bo));
    }

    @SaCheckPermission("system:user:add")
    @PostMapping("/add")
    public Result<Void> add(@Validated @RequestBody UserAddBo bo) {
        userService.add(bo);
        return Result.ok();
    }

    @SaCheckPermission("system:user:delete")
    @DeleteMapping("/{id}")
    public Result<Void> delete(@PathVariable Long id) {
        userService.deleteById(id);
        return Result.ok();
    }
}
        ]]></code>
      </implementation>
      <forbidden_behaviors>
        <prohibition>不添加 @SaCheckPermission 注解的接口方法</prohibition>
        <prohibition>使用模糊的权限命名（如 "user:manage"）</prohibition>
        <prohibition>在一个方法上配置多个不相关的权限</prohibition>
      </forbidden_behaviors>
    </principle>

    <principle name="service_layering" priority="mandatory">
      <title>Service 分层规范</title>
      <description>
        Service 层必须按功能模块分目录组织，不允许使用单一的 impl 目录。
      </description>
      <structure>
        <![CDATA[
src/main/java/com/example/project/
├── service/
│   ├── courseselection/
│   │   ├── SysCourseSelectionService.java
│   │   └── impl/
│   │       └── SysCourseSelectionServiceImpl.java
│   ├── courseoffering/
│   │   ├── SysCourseOfferingService.java
│   │   └── impl/
│   │       └── SysCourseOfferingServiceImpl.java
│   └── user/
│       ├── SysUserService.java
│       └── impl/
│           └── SysUserServiceImpl.java
        ]]>
      </structure>
      <package_naming>
        <format>org.leocoder.{project}.service.{module}</format>
        <example>org.leocoder.course.education.service.courseselection.SysCourseSelectionService</example>
      </package_naming>
    </principle>
  </guiding_principles>

  <!-- ==================== Java 特定执行指令 ==================== -->
  <actionable_instructions language="Java">

    <instruction_set category="plugin_architecture">
      <title>插件化架构开发</title>
      <description>
        项目采用 @Enable 注解实现功能模块的可插拔配置。
      </description>

      <available_plugins>
        <plugin name="EnableCoderSaToken">
          <description>Sa-Token 认证插件</description>
          <usage>@EnableCoderSaToken</usage>
        </plugin>
        <plugin name="EnableCoderEasyExcel">
          <description>Excel 处理插件</description>
          <usage>@EnableCoderEasyExcel</usage>
        </plugin>
        <plugin name="EnableCoderLimit">
          <description>限流插件</description>
          <usage>@EnableCoderLimit</usage>
        </plugin>
        <plugin name="EnableCoderRepeatSubmit">
          <description>防重提交插件</description>
          <usage>@EnableCoderRepeatSubmit</usage>
        </plugin>
        <plugin name="EnableCoderDesensitize">
          <description>数据脱敏插件</description>
          <usage>@EnableCoderDesensitize</usage>
        </plugin>
      </available_plugins>

      <adding_new_plugin>
        <steps>
          <step id="1">在 coder-common-thin-plugins 下创建新模块</step>
          <step id="2">创建 @Enable 注解用于启用插件</step>
          <step id="3">实现必要的配置类和工具类</step>
          <step id="4">在主启动类上使用 @Enable 注解启用</step>
        </steps>
      </adding_new_plugin>
    </instruction_set>

    <instruction_set category="data_access_layer">
      <title>数据访问层开发</title>

      <mybatis_plus_configuration>
        <primary_key_strategy>雪花算法 (ASSIGN_ID)</primary_key_strategy>
        <logical_delete>
          <deleted>0</deleted>
          <not_deleted>1</not_deleted>
        </logical_delete>
        <multi_datasource>
          <support>支持 master/slave 数据源动态切换</support>
          <annotation>@DS("dataSourceName")</annotation>
        </multi_datasource>
        <sql_monitoring>
          <tool>P6spy</tool>
          <purpose>SQL 性能监控</purpose>
        </sql_monitoring>
      </mybatis_plus_configuration>

      <file_organization>
        <entity>coder-common-thin-model/domain/pojo</entity>
        <mapper_interface>coder-common-thin-mybatisplus/mapper</mapper_interface>
        <mapper_xml>resources/mapper</mapper_xml>
      </file_organization>

      <example>
        <code><![CDATA[
// Entity
@Data
@TableName("sys_user")
public class SysUser {
    @TableId(type = IdType.ASSIGN_ID)
    private Long id;

    private String username;

    private String email;

    @TableLogic
    private Integer deleted;

    @TableField(fill = FieldFill.INSERT)
    private LocalDateTime createTime;

    @TableField(fill = FieldFill.INSERT_UPDATE)
    private LocalDateTime updateTime;
}

// Mapper
@Mapper
public interface SysUserMapper extends BaseMapper<SysUser> {

    /**
     * 根据用户名查询用户
     */
    @Select("SELECT * FROM sys_user WHERE username = #{username} AND deleted = 1")
    SysUser selectByUsername(@Param("username") String username);
}

// Service
@Service
@RequiredArgsConstructor
@DS("master")  // 指定数据源
public class SysUserServiceImpl extends ServiceImpl<SysUserMapper, SysUser>
    implements SysUserService {

    @Override
    public SysUser getByUsername(String username) {
        return baseMapper.selectByUsername(username);
    }
}
        ]]></code>
      </example>
    </instruction_set>

    <instruction_set category="testing_standards">
      <title>测试驱动开发规范</title>

      <test_framework>
        <primary>JUnit 5</primary>
        <mocking>Mockito</mocking>
        <assertions>AssertJ</assertions>
      </test_framework>

      <test_structure>
        <example><![CDATA[
@SpringBootTest
@AutoConfigureMockMvc
class UserServiceTest {

    @Autowired
    private UserService userService;

    @MockBean
    private UserMapper userMapper;

    @BeforeEach
    void setUp() {
        // 准备测试数据
    }

    @Test
    @DisplayName("根据ID查询用户 - 成功")
    void testGetById_Success() {
        // Arrange
        Long userId = 1L;
        SysUser mockUser = new SysUser();
        mockUser.setId(userId);
        mockUser.setUsername("testuser");

        when(userMapper.selectById(userId)).thenReturn(mockUser);

        // Act
        SysUser result = userService.getById(userId);

        // Assert
        assertThat(result).isNotNull();
        assertThat(result.getId()).isEqualTo(userId);
        assertThat(result.getUsername()).isEqualTo("testuser");

        verify(userMapper, times(1)).selectById(userId);
    }

    @Test
    @DisplayName("根据ID查询用户 - 用户不存在")
    void testGetById_UserNotFound() {
        // Arrange
        Long userId = 999L;
        when(userMapper.selectById(userId)).thenReturn(null);

        // Act & Assert
        assertThatThrownBy(() -> userService.getById(userId))
            .isInstanceOf(UserNotFoundException.class)
            .hasMessageContaining("User not found");
    }
}
        ]]></example>
      </test_structure>
    </instruction_set>
  </actionable_instructions>

  <!-- ==================== 项目架构 ==================== -->
  <project_architecture language="Java">
    <module_structure>
      <overview>
        基于 Spring Boot 3.5.0 的多模块企业级开发框架，采用插件化架构设计。
      </overview>

      <modules>
        <module name="coder-common-thin-web">
          <type>Web启动模块</type>
          <description>应用程序入口点</description>
        </module>
        <module name="coder-common-thin-common">
          <type>公共工具模块</type>
          <description>包含大量工具类、配置类和拦截器</description>
        </module>
        <module name="coder-common-thin-model">
          <type>数据模型模块</type>
          <description>包含POJO、BO、VO和数据验证</description>
        </module>
        <module name="coder-common-thin-mybatisplus">
          <type>数据访问层模块</type>
          <description>MyBatis Plus配置和Mapper</description>
        </module>
        <module name="coder-common-thin-modules">
          <type>业务模块容器</type>
          <description>包含各业务功能模块</description>
        </module>
        <module name="coder-common-thin-plugins">
          <type>插件模块</type>
          <description>包含各种功能插件</description>
        </module>
      </modules>
    </module_structure>

    <tech_stack>
      <core_framework>Spring Boot 3.5.0 + Java 17</core_framework>
      <database>MySQL 9.3.0 + MyBatis Plus 3.5.12</database>
      <cache>Redis (Spring Data Redis)</cache>
      <security>Sa-Token 1.43.0</security>
      <utilities>
        <utility>Hutool 5.8.38</utility>
        <utility>Fastjson2 2.0.57</utility>
        <utility>Guava 33.4.8</utility>
      </utilities>
      <business_features>
        <feature>EasyExcel 4.0.3</feature>
        <feature>Easy-Captcha 1.6.2</feature>
      </business_features>
    </tech_stack>

    <configuration_files>
      <main_config>application.yml</main_config>
      <env_config>application-dev.yml</env_config>
      <database_config>支持MySQL主从配置</database_config>
      <redis_config>使用Jackson2序列化，支持连接池</redis_config>
    </configuration_files>
  </project_architecture>

  <!-- ==================== Few-Shot 示例 ==================== -->
  <examples category="java_enterprise_patterns">

    <example id="java-01" category="controller_standard">
      <title>标准 Controller 实现</title>
      <scenario>实现符合规范的用户管理 Controller</scenario>
      <code><![CDATA[
package org.leocoder.system.controller;

import cn.dev33.satoken.annotation.SaCheckPermission;
import lombok.RequiredArgsConstructor;
import org.leocoder.common.core.domain.Result;
import org.leocoder.system.domain.bo.UserQueryBo;
import org.leocoder.system.domain.vo.UserVo;
import org.leocoder.system.service.user.SysUserService;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * 用户管理 Controller
 *
 * @author LeoCoder
 * @since 2025-10-19
 */
@RestController
@RequestMapping("/coder/system/user")
@RequiredArgsConstructor
public class SysUserController {

    private final SysUserService userService;

    /**
     * 查询用户列表
     */
    @SaCheckPermission("system:user:list")
    @GetMapping("/list")
    public Result<List<UserVo>> list(@Validated UserQueryBo bo) {
        List<UserVo> list = userService.queryList(bo);
        return Result.ok(list);
    }

    /**
     * 查询用户详情
     */
    @SaCheckPermission("system:user:detail")
    @GetMapping("/{id}")
    public Result<UserVo> getById(@PathVariable Long id) {
        UserVo user = userService.getById(id);
        return Result.ok(user);
    }

    /**
     * 新增用户
     */
    @SaCheckPermission("system:user:add")
    @PostMapping
    public Result<Void> add(@Validated @RequestBody UserAddBo bo) {
        userService.add(bo);
        return Result.ok();
    }

    /**
     * 修改用户
     */
    @SaCheckPermission("system:user:edit")
    @PutMapping
    public Result<Void> edit(@Validated @RequestBody UserEditBo bo) {
        userService.edit(bo);
        return Result.ok();
    }

    /**
     * 删除用户
     */
    @SaCheckPermission("system:user:delete")
    @DeleteMapping("/{ids}")
    public Result<Void> delete(@PathVariable Long[] ids) {
        userService.deleteByIds(ids);
        return Result.ok();
    }
}
      ]]></code>
      <key_points>
        <point>使用 @RequiredArgsConstructor 构造器注入</point>
        <point>统一路径前缀 /coder</point>
        <point>所有方法都有 @SaCheckPermission 权限验证</point>
        <point>使用 Result 统一返回格式</point>
        <point>BO（Business Object）用于接收参数，VO（View Object）用于返回</point>
      </key_points>
    </example>

    <example id="java-02" category="service_implementation">
      <title>标准 Service 实现</title>
      <scenario>实现符合 DDD 原则的 Service 层</scenario>
      <code><![CDATA[
package org.leocoder.system.service.user.impl;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import lombok.RequiredArgsConstructor;
import org.leocoder.common.exception.BusinessException;
import org.leocoder.system.domain.bo.UserAddBo;
import org.leocoder.system.domain.bo.UserEditBo;
import org.leocoder.system.domain.bo.UserQueryBo;
import org.leocoder.system.domain.pojo.SysUser;
import org.leocoder.system.domain.vo.UserVo;
import org.leocoder.system.mapper.SysUserMapper;
import org.leocoder.system.service.user.SysUserService;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * 用户服务实现
 *
 * @author LeoCoder
 * @since 2025-10-19
 */
@Service
@RequiredArgsConstructor
public class SysUserServiceImpl extends ServiceImpl<SysUserMapper, SysUser>
    implements SysUserService {

    private final SysUserMapper userMapper;

    @Override
    public List<UserVo> queryList(UserQueryBo bo) {
        LambdaQueryWrapper<SysUser> wrapper = new LambdaQueryWrapper<>();
        wrapper.like(bo.getUsername() != null, SysUser::getUsername, bo.getUsername())
               .like(bo.getEmail() != null, SysUser::getEmail, bo.getEmail())
               .eq(bo.getStatus() != null, SysUser::getStatus, bo.getStatus())
               .orderByDesc(SysUser::getCreateTime);

        List<SysUser> list = userMapper.selectList(wrapper);
        return list.stream()
                   .map(this::convertToVo)
                   .collect(Collectors.toList());
    }

    @Override
    public UserVo getById(Long id) {
        SysUser user = userMapper.selectById(id);
        if (user == null) {
            throw new BusinessException("用户不存在");
        }
        return convertToVo(user);
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void add(UserAddBo bo) {
        // 检查用户名是否已存在
        if (checkUsernameExists(bo.getUsername())) {
            throw new BusinessException("用户名已存在");
        }

        // 转换并保存
        SysUser user = convertFromAddBo(bo);
        userMapper.insert(user);
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void edit(UserEditBo bo) {
        SysUser existingUser = userMapper.selectById(bo.getId());
        if (existingUser == null) {
            throw new BusinessException("用户不存在");
        }

        // 检查用户名是否被其他用户占用
        if (!existingUser.getUsername().equals(bo.getUsername())
            && checkUsernameExists(bo.getUsername())) {
            throw new BusinessException("用户名已存在");
        }

        SysUser user = convertFromEditBo(bo);
        userMapper.updateById(user);
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void deleteByIds(Long[] ids) {
        Arrays.stream(ids).forEach(id -> {
            SysUser user = userMapper.selectById(id);
            if (user == null) {
                throw new BusinessException("用户不存在: " + id);
            }
            userMapper.deleteById(id);
        });
    }

    /**
     * 检查用户名是否存在
     */
    private boolean checkUsernameExists(String username) {
        LambdaQueryWrapper<SysUser> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(SysUser::getUsername, username);
        return userMapper.selectCount(wrapper) > 0;
    }

    /**
     * 实体转换为VO
     */
    private UserVo convertToVo(SysUser user) {
        UserVo vo = new UserVo();
        vo.setId(user.getId());
        vo.setUsername(user.getUsername());
        vo.setEmail(user.getEmail());
        vo.setStatus(user.getStatus());
        vo.setCreateTime(user.getCreateTime());
        return vo;
    }

    /**
     * AddBo转换为实体
     */
    private SysUser convertFromAddBo(UserAddBo bo) {
        SysUser user = new SysUser();
        user.setUsername(bo.getUsername());
        user.setEmail(bo.getEmail());
        user.setPassword(bo.getPassword()); // 实际应加密
        user.setStatus(1);
        return user;
    }

    /**
     * EditBo转换为实体
     */
    private SysUser convertFromEditBo(UserEditBo bo) {
        SysUser user = new SysUser();
        user.setId(bo.getId());
        user.setUsername(bo.getUsername());
        user.setEmail(bo.getEmail());
        user.setStatus(bo.getStatus());
        return user;
    }
}
      ]]></code>
      <key_points>
        <point>使用 @RequiredArgsConstructor 构造器注入</point>
        <point>继承 ServiceImpl 获得基础 CRUD 方法</point>
        <point>业务验证在 Service 层进行</point>
        <point>使用 @Transactional 确保事务一致性</point>
        <point>BO/VO 转换逻辑封装在私有方法中</point>
        <point>异常使用 BusinessException 统一处理</point>
      </key_points>
    </example>

    <example id="java-03" category="exception_handling">
      <title>统一异常处理</title>
      <scenario>实现全局异常处理器</scenario>
      <code><![CDATA[
package org.leocoder.common.exception;

import lombok.extern.slf4j.Slf4j;
import org.leocoder.common.core.domain.Result;
import org.springframework.validation.BindException;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.servlet.http.HttpServletRequest;
import java.util.stream.Collectors;

/**
 * 全局异常处理器
 *
 * @author LeoCoder
 * @since 2025-10-19
 */
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    /**
     * 业务异常
     */
    @ExceptionHandler(BusinessException.class)
    public Result<Void> handleBusinessException(BusinessException e, HttpServletRequest request) {
        log.error("业务异常: {} URL: {}", e.getMessage(), request.getRequestURI());
        return Result.fail(e.getMessage());
    }

    /**
     * 参数校验异常
     */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Result<Void> handleValidationException(MethodArgumentNotValidException e) {
        String message = e.getBindingResult().getFieldErrors().stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.joining("; "));
        log.error("参数校验失败: {}", message);
        return Result.fail("参数校验失败: " + message);
    }

    /**
     * 绑定异常
     */
    @ExceptionHandler(BindException.class)
    public Result<Void> handleBindException(BindException e) {
        String message = e.getBindingResult().getFieldErrors().stream()
            .map(FieldError::getDefaultMessage)
            .collect(Collectors.joining("; "));
        log.error("参数绑定失败: {}", message);
        return Result.fail("参数绑定失败: " + message);
    }

    /**
     * 权限异常
     */
    @ExceptionHandler(cn.dev33.satoken.exception.NotPermissionException.class)
    public Result<Void> handleNotPermissionException(
        cn.dev33.satoken.exception.NotPermissionException e) {
        log.error("权限不足: {}", e.getPermission());
        return Result.fail("权限不足: " + e.getPermission());
    }

    /**
     * 系统异常
     */
    @ExceptionHandler(Exception.class)
    public Result<Void> handleException(Exception e, HttpServletRequest request) {
        log.error("系统异常 URL: {}", request.getRequestURI(), e);
        return Result.fail("系统异常，请联系管理员");
    }
}
      ]]></code>
      <key_points>
        <point>使用 @RestControllerAdvice 全局异常拦截</point>
        <point>区分业务异常、参数校验异常、权限异常和系统异常</point>
        <point>所有异常都记录日志</point>
        <point>返回统一的 Result 格式</point>
        <point>参数校验错误信息合并展示</point>
      </key_points>
    </example>

    <example id="java-04" category="validation">
      <title>参数验证最佳实践</title>
      <scenario>使用 JSR-303 进行参数验证</scenario>
      <code><![CDATA[
package org.leocoder.system.domain.bo;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.*;

/**
 * 用户新增BO
 *
 * @author LeoCoder
 * @since 2025-10-19
 */
@Data
public class UserAddBo {

    @NotBlank(message = "用户名不能为空")
    @Length(min = 2, max = 20, message = "用户名长度必须在2-20个字符之间")
    @Pattern(regexp = "^[a-zA-Z0-9_]+$", message = "用户名只能包含字母、数字和下划线")
    private String username;

    @NotBlank(message = "邮箱不能为空")
    @Email(message = "邮箱格式不正确")
    private String email;

    @NotBlank(message = "密码不能为空")
    @Length(min = 6, max = 20, message = "密码长度必须在6-20个字符之间")
    private String password;

    @NotNull(message = "年龄不能为空")
    @Min(value = 1, message = "年龄必须大于0")
    @Max(value = 150, message = "年龄必须小于150")
    private Integer age;

    @Pattern(regexp = "^1[3-9]\\d{9}$", message = "手机号格式不正确")
    private String phone;
}

// 在 Controller 中使用
@RestController
@RequestMapping("/coder/system/user")
@RequiredArgsConstructor
public class SysUserController {

    private final SysUserService userService;

    @SaCheckPermission("system:user:add")
    @PostMapping
    public Result<Void> add(@Validated @RequestBody UserAddBo bo) {
        // @Validated 会自动触发参数校验
        // 校验失败会抛出 MethodArgumentNotValidException
        // 由 GlobalExceptionHandler 统一处理
        userService.add(bo);
        return Result.ok();
    }
}
      ]]></code>
      <key_points>
        <point>使用 JSR-303 注解进行声明式验证</point>
        <point>每个验证注解都提供清晰的错误消息</point>
        <point>使用 @Validated 触发验证</point>
        <point>复杂验证使用正则表达式</point>
        <point>验证失败由全局异常处理器统一处理</point>
      </key_points>
    </example>
  </examples>

  <!-- ==================== 常用命令参考 ==================== -->
  <tools_and_commands language="Java">
    <command_category name="build_and_run">
      <title>构建和运行</title>
      <commands>
        <command>
          <description>编译整个项目</description>
          <code>mvn clean compile</code>
        </command>
        <command>
          <description>打包项目</description>
          <code>mvn clean package</code>
        </command>
        <command>
          <description>运行应用程序</description>
          <code>cd coder-common-thin-web && mvn spring-boot:run</code>
        </command>
        <command>
          <description>运行打包后的jar</description>
          <code>java -jar coder-common-thin-web/target/coder-common-thin-web-1.0.0.jar</code>
        </command>
      </commands>
    </command_category>

    <command_category name="testing">
      <title>测试</title>
      <commands>
        <command>
          <description>运行所有测试</description>
          <code>mvn test</code>
        </command>
        <command>
          <description>运行单个模块测试</description>
          <code>mvn test -pl coder-common-thin-web</code>
        </command>
        <command>
          <description>生成测试覆盖率报告</description>
          <code>mvn clean test jacoco:report</code>
        </command>
      </commands>
    </command_category>

    <command_category name="code_quality">
      <title>代码质量检查</title>
      <commands>
        <command>
          <description>运行 Checkstyle</description>
          <code>mvn checkstyle:check</code>
        </command>
        <command>
          <description>运行 SpotBugs</description>
          <code>mvn spotbugs:check</code>
        </command>
        <command>
          <description>运行 PMD</description>
          <code>mvn pmd:check</code>
        </command>
      </commands>
    </command_category>
  </tools_and_commands>

  <!-- ==================== 强制要求 ==================== -->
  <mandatory_requirements priority="critical">
    <requirement id="req-java-01">
      <title>构造器注入强制要求</title>
      <description>所有 ServiceImpl 必须使用 @RequiredArgsConstructor</description>
      <enforcement>代码审查时严格检查</enforcement>
    </requirement>

    <requirement id="req-java-02">
      <title>权限验证强制要求</title>
      <description>所有 Controller 方法必须配置 @SaCheckPermission</description>
      <enforcement>代码审查时严格检查</enforcement>
    </requirement>

    <requirement id="req-java-03">
      <title>统一路径前缀要求</title>
      <description>所有 Controller 必须使用 /coder 前缀</description>
      <enforcement>代码审查时严格检查</enforcement>
    </requirement>

    <requirement id="req-java-04">
      <title>Service 分层要求</title>
      <description>Service 必须按功能模块分目录组织</description>
      <enforcement>项目结构审查</enforcement>
    </requirement>

    <requirement id="req-java-05">
      <title>测试覆盖率要求</title>
      <description>核心业务逻辑测试覆盖率不低于 80%</description>
      <enforcement>CI/CD 流程中强制检查</enforcement>
    </requirement>
  </mandatory_requirements>

  <!-- ==================== 数据库相关 ==================== -->
  <database_standards>
    <initialization>
      <sql_location>sql/coder-common-thin.sql</sql_location>
      <description>数据库初始化SQL</description>
    </initialization>

    <naming_conventions>
      <table>snake_case (例如: sys_user, course_selection)</table>
      <column>snake_case (例如: user_name, create_time)</column>
      <index>idx_{table}_{column} (例如: idx_user_username)</index>
    </naming_conventions>
  </database_standards>

</claude_rules>
