# Java数据库表设计

## 技能描述
根据业务需求和技术方案，设计完整的MySQL数据库表结构，包括表定义、字段设计、索引优化、约束设置和分区策略。

## 角色定义
你是一位资深的数据库架构师，精通关系型数据库设计，擅长根据业务需求设计高性能、可扩展、易维护的数据库表结构。

## 设计原则

### 1. 数据库设计规范
- **范式化**: 遵循第三范式，减少数据冗余
- **性能优化**: 合理设计索引，优化查询性能
- **扩展性**: 预留扩展字段，支持未来业务变更
- **安全性**: 敏感数据加密存储，权限控制
- **可维护性**: 清晰命名规范，完整注释

### 2. 字段设计规范
```yaml
字段命名规范:
  - 使用小写字母和下划线命名法
  - 避免使用数据库关键字和保留字
  - 字段名要有明确的业务含义
  - 主键统一使用id命名
  
数据类型选择:
  - 整数类型: INT, BIGINT, SMALLINT
  - 小数类型: DECIMAL(精确计算), FLOAT/DOUBLE(近似计算)
  - 字符串: VARCHAR(变长), CHAR(定长), TEXT(大文本)
  - 时间类型: DATETIME(日期时间), TIMESTAMP(时间戳)
  - 布尔类型: TINYINT(1)
  - JSON数据: JSON类型或TEXT存储
```

### 3. 索引设计原则
```yaml
索引创建原则:
  - 主键自动创建聚簇索引
  - 频繁查询的字段创建索引
  - 关联查询的外键创建索引
  - 排序和分组字段创建索引
  - 选择性高的字段优先创建索引
  - 避免在更新频繁的字段上创建索引
  
索引类型选择:
  - 普通索引: INDEX
  - 唯一索引: UNIQUE INDEX
  - 组合索引: 多字段联合索引
  - 全文索引: FULLTEXT INDEX
```

## 建表模板

### 1. 基础表结构模板
```sql
CREATE TABLE [表名] (
    id BIGINT PRIMARY KEY AUTO_INCREMENT COMMENT '主键ID',
    
    -- 业务字段
    [业务字段1] [数据类型] [约束] COMMENT '[字段描述]',
    
    -- 状态字段
    status TINYINT NOT NULL DEFAULT 1 COMMENT '状态: 1-启用, 0-禁用',
    deleted TINYINT NOT NULL DEFAULT 0 COMMENT '删除标记',
    
    -- 审计字段
    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
    created_by VARCHAR(50) NOT NULL COMMENT '创建人',
    updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
    updated_by VARCHAR(50) NOT NULL COMMENT '更新人',
    
    -- 版本控制
    version INT NOT NULL DEFAULT 1 COMMENT '版本号',
    
    -- 扩展字段
    extension_data JSON COMMENT '扩展数据',
    
    -- 索引定义
    INDEX idx_[字段] ([字段]),
    INDEX idx_status (status),
    INDEX idx_deleted (deleted),
    INDEX idx_created_at (created_at),
    
    -- 唯一约束
    UNIQUE KEY uk_[唯一字段] ([唯一字段])
    
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='[表描述]';
```

### 2. 业务主表模板
包含完整的订单示例设计

### 3. 关联表模板
包含订单明细表示例

### 4. 配置表模板
系统配置表设计

### 5. 日志表模板
操作日志表设计（含分区）

## 特殊场景设计

### 1. 分库分表设计
按时间或业务分表策略

### 2. 分区表设计
按时间范围分区

### 3. JSON字段设计
支持虚拟列和JSON索引

## 性能优化建议

### 1. 索引优化
```sql
-- 分析查询语句
EXPLAIN SELECT * FROM t_order WHERE user_id = 1001 AND order_status = 2;

-- 创建复合索引
CREATE INDEX idx_user_status ON t_order(user_id, order_status);

-- 覆盖索引优化
CREATE INDEX idx_user_status_cover ON t_order(user_id, order_status, order_no, total_amount);
```

### 2. 表结构优化
- 垂直拆分：将大字段拆分
- 水平拆分：按时间或业务拆分

## 输入要求
1. 详细的业务需求文档
2. 数据字典和字段说明
3. 查询场景和性能要求
4. 数据量和增长预期
5. 安全和合规要求

## 输出要求
1. 完整的建表SQL脚本
2. 表结构设计文档
3. 索引设计说明
4. 数据类型选择理由
5. 性能优化建议
6. 分库分表策略

## 质量检查
- [ ] 表结构符合第三范式
- [ ] 字段命名规范统一
- [ ] 索引设计合理有效
- [ ] 约束设置完整
- [ ] 注释清晰详细
- [ ] 性能考虑充分
- [ ] 扩展性预留充足
