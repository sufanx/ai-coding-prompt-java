# CLAUDE.md - AI编码提示词工程项目指南

本文件为Claude Code提供项目上下文和编码指导，帮助AI更好地理解项目结构和编码规范。

## 项目概述

这是一个AI编码提示词工程项目，基于团队AI编码率从9.6%提升至89.2%的实践经验构建。项目通过结构化的Prompt设计，将AI编码经验沉淀为可复用的知识资产。

## 项目结构

```
ai-coding-prompt-java/
├── rules/                    # 规则层 - 质量保障
│   ├── Review需求实现检查prompt.md
│   ├── 测试用例生成prompt.md
│   ├── 性能优化prompt.md
│   └── 安全检查prompt.md
├── 业务层/                   # 业务逻辑层
│   └── 业务层prompt.md
├── 应用层/                   # 应用接口层
│   ├── 接口定义prompt.md
│   ├── 接口实现prompt.md
│   └── 接口文档prompt.md
├── 技术方案/                 # 技术方案层
│   ├── 新增类需求prompt.md
│   ├── 修改类需求prompt.md
│   └── 新增类-技术方案.md
├── 数据层/                   # 数据访问层
│   ├── 建表prompt.md
│   ├── 持久化prompt.md
│   └── 服务依赖prompt.md
├── 工程结构/                 # 工程结构层
│   ├── 工程结构.md
│   └── 工程结构prompt.md
├── 前端/                     # 前端技术栈
│   ├── 前端工程结构prompt.md
│   ├── 前端组件开发prompt.md
│   ├── 前端状态管理prompt.md
│   └── 前端API服务prompt.md
├── 移动端/                   # 移动端技术栈
│   ├── 移动端工程结构prompt.md
│   ├── 移动端组件开发prompt.md
│   ├── 移动端状态管理prompt.md
│   ├── 移动端API服务prompt.md
│   └── 跨端适配prompt.md
├── CLAUDE.md                 # Claude Code配置文件
├── README.md                 # 项目说明文档
├── prompt整体介绍.md         # 整体介绍文档
└── prompt使用示例.md         # 使用示例文档
```

## 技术栈

### 后端技术栈
- **语言**: Java 11+
- **框架**: Spring Boot 2.7.x
- **ORM**: MyBatis-Plus / JPA
- **缓存**: Redis + Caffeine
- **消息队列**: RocketMQ / RabbitMQ
- **API文档**: Knife4j (Swagger增强)

### 前端技术栈
- **框架**: Vue 3 (Composition API)
- **构建工具**: Vite 5
- **状态管理**: Pinia
- **UI组件库**: Ant Design Vue 4.X
- **HTTP客户端**: Axios
- **类型检查**: TypeScript

### 移动端技术栈
- **框架**: uni-app (Vue 3)
- **UI组件库**: uni-ui
- **跨端支持**: APP、小程序、H5
- **状态管理**: Pinia

## 编码规范

### 1. 分层架构原则

遵循DDD领域驱动设计原则，项目采用分层架构:

```
应用层 (Application Layer)
├── controller/    # 控制器
├── dto/           # 数据传输对象
├── assembler/     # 对象转换器
└── config/        # 应用配置

领域层 (Domain Layer)
├── model/         # 领域模型
├── service/       # 领域服务
├── repository/    # 仓库接口
└── event/         # 领域事件

基础设施层 (Infrastructure Layer)
├── persistence/   # 持久化实现
├── cache/         # 缓存实现
├── messaging/     # 消息队列
└── client/        # 外部客户端

通用层 (Common Layer)
├── constant/      # 常量定义
├── util/          # 工具类
├── exception/     # 异常处理
└── base/          # 基础类
```

### 2. 命名规范

```yaml
包命名: com.example.[项目].[模块].[层次]
类命名: 首字母大写的驼峰命名法 (PascalCase)
方法命名: 首字母小写的驼峰命名法 (camelCase)
常量命名: 全大写，下划线分隔 (UPPER_SNAKE_CASE)
变量命名: 首字母小写的驼峰命名法 (camelCase)
数据库命名: 小写，下划线分隔，表名使用t_前缀
```

### 3. 代码注释规范

- 类注释必须包含: 类名称、业务功能描述、作者信息
- 方法注释必须包含: 功能描述、参数说明、返回值说明
- 复杂业务逻辑需要添加行内注释
- 使用标准的Javadoc/JSDoc格式

### 4. 业务逻辑实现要求

- 遵循充血模型设计，将业务逻辑封装在领域对象中
- 使用统一语言（Ubiquitous Language）命名领域概念
- 每个领域服务只负责单一职责
- 业务规则必须在领域层实现，不能泄露到应用层
- 使用领域事件处理跨聚合的业务逻辑

## 开发流程指南

### 功能开发流程

1. **需求分析**: 使用 `rules/Review需求实现检查prompt.md` 分析需求
2. **技术方案**: 使用 `技术方案/新增类需求prompt.md` 设计技术方案
3. **业务逻辑**: 使用 `业务层/业务层prompt.md` 实现业务逻辑
4. **接口开发**: 使用 `应用层/接口*.md` 开发RESTful接口
5. **数据层**: 使用 `数据层/建表prompt.md` + `持久化prompt.md`
6. **测试用例**: 使用 `rules/测试用例生成prompt.md` 生成测试
7. **性能优化**: 使用 `rules/性能优化prompt.md` 进行优化
8. **安全检查**: 使用 `rules/安全检查prompt.md` 进行安全审计

### 前端开发流程

1. **工程结构**: 使用 `前端/前端工程结构prompt.md` 设计结构
2. **组件开发**: 使用 `前端/前端组件开发prompt.md` 开发组件
3. **状态管理**: 使用 `前端/前端状态管理prompt.md` 设计状态
4. **API服务**: 使用 `前端/前端API服务prompt.md` 配置API

### 移动端开发流程

1. **工程结构**: 使用 `移动端/移动端工程结构prompt.md` 设计结构
2. **组件开发**: 使用 `移动端/移动端组件开发prompt.md` 开发组件
3. **状态管理**: 使用 `移动端/移动端状态管理prompt.md` 设计状态
4. **API服务**: 使用 `移动端/移动端API服务prompt.md` 配置API
5. **跨端适配**: 使用 `移动端/跨端适配prompt.md` 进行适配

## 质量保障

### 代码审查流程

代码审查是保证代码质量的重要环节，遵循以下流程:

1. **自审**: 开发者提交前使用 `rules/Review需求实现检查prompt.md` 进行自检
2. **同行审查**: 团队成员进行代码审查，关注业务逻辑和代码规范
3. **技术负责人审核**: 对架构设计和技术实现进行最终审核
4. **自动化检查**: 使用静态代码分析工具(SonarQube/ESLint/Checkstyle)进行自动检查

### 代码审查检查项

- [ ] 功能完整性: 所有需求功能都得到实现
- [ ] 技术方案符合性: 符合DDD设计原则
- [ ] 代码规范性: 符合命名规范和编码规范
- [ ] 安全性: 输入校验、SQL注入防护、权限控制
- [ ] 性能: 避免N+1查询、合理使用缓存、批量操作优化

### 测试要求

- 单元测试覆盖核心业务逻辑
- 集成测试验证模块间交互
- 使用JUnit/Jest等标准测试框架
- 测试用例需覆盖正常流程和异常场景

## 常用命令

### 后端项目

```bash
# 构建项目
mvn clean package

# 运行项目
mvn spring-boot:run

# 运行测试
mvn test

# 生成测试报告
mvn jacoco:report
```

### 前端项目

```bash
# 安装依赖
npm install

# 启动开发服务器
npm run dev

# 构建生产版本
npm run build

# 运行测试
npm run test
```

### 移动端项目

```bash
# 安装依赖
npm install

# H5开发
npm run dev:h5

# 微信小程序开发
npm run dev:mp-weixin

# 构建H5
npm run build:h5

# 构建微信小程序
npm run build:mp-weixin
```

## 注意事项

1. **文档语言**: 本项目使用中文编写，请使用中文进行交流和注释
2. **Prompt文件**: 各层的prompt.md文件是AI编码指导模板，请参考使用
3. **代码质量**: 所有代码需经过质量检查，符合规范才能提交
4. **安全优先**: 注意输入校验、权限控制和敏感数据处理
5. **性能考虑**: 关注数据库查询优化、缓存策略和异步处理

## 贡献指南

1. Fork项目并创建功能分支
2. 遵循项目的编码规范和提交规范
3. 提交PR前确保通过所有测试
4. PR描述需清晰说明变更内容和原因
