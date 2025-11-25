# Claude Skills - AI编码技能库

## 📋 简介

本目录包含一系列Claude技能文件，用于辅助AI编码工作。这些技能是从AI编码提示词工程项目中的prompt模板转换而来，专门为Claude优化。

## 🎯 技能分类

### Java后端技能

| 技能文件 | 说明 | 原始prompt |
|---------|------|-----------|
| `java-architecture-new-class.md` | 新增类技术方案设计 | 技术方案/新增类需求prompt.md |
| `java-architecture-modify-class.md` | 修改类技术方案设计 | 技术方案/修改类需求prompt.md |
| `java-api-definition.md` | RESTful接口定义 | 应用层/接口定义prompt.md |
| `java-api-implementation.md` | RESTful接口实现 | 应用层/接口实现prompt.md |
| `java-api-documentation.md` | 接口文档生成 | 应用层/接口文档prompt.md |
| `java-database-schema.md` | 数据库表设计 | 数据层/建表prompt.md |
| `java-data-persistence.md` | 数据持久化设计 | 数据层/持久化prompt.md |
| `java-service-dependency.md` | 微服务依赖管理 | 数据层/服务依赖prompt.md |
| `java-ddd-business-layer.md` | DDD业务层实现 | 业务层/业务层prompt.md |
| `java-requirement-review.md` | 需求实现检查 | rules/Review需求实现检查prompt.md |
| `java-test-case-generation.md` | 测试用例生成 | rules/测试用例生成prompt.md |

### Vue3前端技能

| 技能文件 | 说明 | 原始prompt |
|---------|------|-----------|
| `vue3-project-structure.md` | 前端工程结构设计 | 前端/前端工程结构prompt.md |
| `vue3-component-development.md` | 组件开发 | 前端/前端组件开发prompt.md |
| `vue3-pinia-state-management.md` | Pinia状态管理 | 前端/前端状态管理prompt.md |
| `vue3-axios-api-service.md` | Axios API服务 | 前端/前端API服务prompt.md |

### uni-app移动端技能

| 技能文件 | 说明 | 原始prompt |
|---------|------|-----------|
| `uniapp-project-structure.md` | 移动端工程结构设计 | 移动端/移动端工程结构prompt.md |
| `uniapp-component-development.md` | 移动端组件开发 | 移动端/移动端组件开发prompt.md |

## 🚀 使用方式

### 在Claude中使用

1. **直接引用技能**: 在对话中提及技能名称，Claude会根据技能内容提供专业指导
2. **结合具体需求**: 描述您的具体需求，Claude会参考相关技能生成代码或方案

### 示例对话

```
用户: 我需要设计一个订单管理系统的数据库表结构

Claude: [参考java-database-schema.md技能，提供专业的数据库设计方案]
```

## 📂 技能结构

每个技能文件包含以下部分：

1. **技能描述** - 技能的用途和目标
2. **角色定义** - Claude在该技能下扮演的角色
3. **核心概念/原则** - 相关的设计原则和最佳实践
4. **模板/示例** - 代码模板和示例
5. **输入要求** - 使用该技能需要提供的信息
6. **输出要求** - 技能应该产出的内容
7. **质量检查** - 验证输出质量的检查清单

## 🔧 技术栈

### 后端技术栈
- Java 11+
- Spring Boot 2.7.x
- MyBatis-Plus / JPA
- MySQL 8.0+
- Redis
- RocketMQ / RabbitMQ

### 前端技术栈
- Vue 3 (Composition API)
- TypeScript
- Vite 5
- Pinia
- Ant Design Vue 4.x
- Axios

### 移动端技术栈
- uni-app (Vue 3)
- uni-ui
- TypeScript
- 支持APP/小程序/H5

## 📖 相关文档

- [prompt整体介绍.md](../prompt整体介绍.md) - 了解完整的prompt体系
- [prompt使用示例.md](../prompt使用示例.md) - 查看详细的使用示例
- [README.md](../README.md) - 项目总体说明

## 🤝 贡献

欢迎提交新的技能或改进现有技能：

1. Fork本仓库
2. 创建技能文件
3. 提交Pull Request

## 📄 许可证

MIT License
