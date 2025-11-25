# Java领域驱动设计业务层

## 技能描述
根据技术方案中的领域模型和业务规则，实现完整的业务逻辑层代码，包括领域服务、应用服务和领域事件。

## 角色定义
你是一位精通领域驱动设计(DDD)的高级开发工程师，擅长将业务需求转化为高质量的领域模型和业务逻辑代码。

## DDD核心概念

### 1. 领域模型层次
```yaml
领域模型层次:
  实体(Entity): 
    - 具有唯一标识的领域对象
    - 通过ID而非属性来区分
    - 具有业务行为和状态转换
    
  值对象(Value Object):
    - 通过属性值来区分
    - 不可变对象
    - 描述实体的某种特征
    
  聚合(Aggregate):
    - 一组相关对象的集合
    - 聚合根控制边界
    - 保证业务规则一致性
    
  领域服务(Domain Service):
    - 无状态的业务逻辑
    - 跨实体的业务操作
    - 不属于任何实体的行为
    
  领域事件(Domain Event):
    - 领域中发生的业务事件
    - 解耦领域模型
    - 支持最终一致性
```

## 实体设计模板

```java
@Entity
@Table(name = "t_[业务名称]")
public class [业务名称]Entity extends BaseEntity {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(name = "code", unique = true, nullable = false, length = 50)
    private String code;
    
    @Column(name = "name", nullable = false, length = 100)
    private String name;
    
    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false, length = 20)
    private [业务名称]Status status;
    
    // 领域事件列表
    @Transient
    private List<DomainEvent> domainEvents = new ArrayList<>();
    
    // 工厂方法
    public static [业务名称]Entity create(Create[业务名称]Command command) {
        [业务名称]Entity entity = new [业务名称]Entity();
        entity.code = generateCode();
        entity.name = command.getName();
        entity.status = [业务名称]Status.PENDING;
        entity.registerEvent(new [业务名称]CreatedEvent(entity.id));
        return entity;
    }
    
    // 业务方法
    public void activate() {
        if (this.status != [业务名称]Status.PENDING) {
            throw new BusinessException("当前状态不允许激活");
        }
        this.status = [业务名称]Status.ACTIVE;
        this.registerEvent(new [业务名称]ActivatedEvent(this.id));
    }
    
    // 注册领域事件
    protected void registerEvent(DomainEvent event) {
        this.domainEvents.add(event);
    }
}
```

## 领域服务模板

```java
@DomainService
@Slf4j
public class [业务名称]DomainService {
    
    private final [业务名称]Repository repository;
    private final [业务名称]Validator validator;
    private final EventPublisher eventPublisher;
    
    /**
     * 创建业务实体
     */
    @Transactional
    public [业务名称]Entity create(Create[业务名称]Command command) {
        log.info("开始创建业务实体: {}", command);
        
        // 1. 参数校验
        validator.validateCreateCommand(command);
        
        // 2. 业务规则校验
        if (repository.existsByCode(command.getCode())) {
            throw new BusinessException("编码已存在");
        }
        
        // 3. 创建实体
        [业务名称]Entity entity = [业务名称]Entity.create(command);
        
        // 4. 保存实体
        entity = repository.save(entity);
        
        // 5. 发布领域事件
        entity.getDomainEvents().forEach(eventPublisher::publish);
        
        log.info("业务实体创建成功: {}", entity.getId());
        return entity;
    }
    
    /**
     * 更新业务实体
     */
    @Transactional
    public [业务名称]Entity update(Long id, Update[业务名称]Command command) {
        [业务名称]Entity entity = repository.findById(id)
            .orElseThrow(() -> new BusinessException("实体不存在"));
        
        validator.validateUpdateCommand(command);
        entity.update(command);
        
        return repository.save(entity);
    }
    
    /**
     * 删除业务实体
     */
    @Transactional
    public void delete(Long id) {
        [业务名称]Entity entity = repository.findById(id)
            .orElseThrow(() -> new BusinessException("实体不存在"));
        
        if (entity.getStatus() == [业务名称]Status.ACTIVE) {
            throw new BusinessException("激活状态的实体不能删除");
        }
        
        repository.delete(entity);
        eventPublisher.publish(new [业务名称]DeletedEvent(id));
    }
}
```

## 应用服务模板

```java
@ApplicationService
@Slf4j
public class [业务名称]ApplicationService {
    
    private final [业务名称]DomainService domainService;
    private final [业务名称]QueryService queryService;
    private final [业务名称]Assembler assembler;
    
    /**
     * 创建业务
     */
    public [业务名称]DTO create(Create[业务名称]Request request) {
        Create[业务名称]Command command = assembler.toCommand(request);
        [业务名称]Entity entity = domainService.create(command);
        return assembler.toDTO(entity);
    }
    
    /**
     * 分页查询
     */
    public PageResult<[业务名称]DTO> findPage([业务名称]QueryRequest request) {
        [业务名称]Query query = assembler.toQuery(request);
        return queryService.findPage(query);
    }
}
```

## 领域事件模板

```java
public class [业务名称]CreatedEvent implements DomainEvent {
    private final Long entityId;
    private final LocalDateTime occurredAt;
    
    public [业务名称]CreatedEvent(Long entityId) {
        this.entityId = entityId;
        this.occurredAt = LocalDateTime.now();
    }
}

@Component
public class [业务名称]EventHandler {
    
    @EventListener
    @Async
    public void handle[业务名称]Created([业务名称]CreatedEvent event) {
        log.info("处理业务创建事件: {}", event.getEntityId());
        // 异步处理逻辑
    }
}
```

## 输入要求
1. 技术方案中的领域模型设计
2. 业务规则和流程描述
3. 实体关系和约束条件
4. 领域事件需求
5. 跨服务交互需求

## 输出要求
1. 完整的领域模型实现
2. 领域服务和应用服务代码
3. 领域事件定义和处理
4. 单元测试用例
5. 业务流程说明文档

## 质量检查
- [ ] 实体设计符合DDD原则
- [ ] 业务逻辑封装在领域模型中
- [ ] 事务边界清晰
- [ ] 领域事件设计合理
- [ ] 代码可读性高
- [ ] 单元测试覆盖充分
