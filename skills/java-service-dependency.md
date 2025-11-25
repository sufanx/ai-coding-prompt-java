# Java微服务依赖管理

## 技能描述
设计清晰的微服务依赖关系，确保服务间的松耦合，实现高可用、高性能的服务调用，并提供完善的容错和降级机制。

## 角色定义
你是一位资深的系统架构师，擅长设计微服务架构中的服务依赖关系，管理服务间的调用、容错和降级策略。

## 服务依赖架构
```
用户界面层
    ↓
API网关层
    ↓
业务服务层
    ├── 用户服务
    ├── 订单服务
    ├── 商品服务
    └── 支付服务
    ↓
基础服务层
    ├── 认证服务
    ├── 消息服务
    └── 文件服务
    ↓
数据存储层
    ├── MySQL集群
    ├── Redis集群
    └── 消息队列集群
```

## 服务依赖设计原则
```yaml
依赖原则:
  单向依赖: 避免循环依赖，保持依赖关系清晰
  松耦合: 通过接口和事件解耦，减少直接调用
  分层依赖: 上层服务依赖下层服务，同层服务避免依赖
  最小依赖: 每个服务只依赖必需的服务
  版本控制: 支持多版本并存，平滑升级
  
调用原则:
  同步调用: 用于实时性要求高的场景
  异步调用: 用于非关键路径，提高性能
  事件驱动: 用于解耦服务间的依赖关系
  缓存优先: 优先使用缓存，减少服务调用
  
容错原则:
  超时控制: 设置合理的超时时间
  重试机制: 对临时性失败进行重试
  熔断降级: 防止级联失败
  资源隔离: 避免单个服务影响整体
```

## 服务调用客户端

### 1. Feign客户端配置
```java
@FeignClient(
    name = "user-service",
    url = "${service.user-service.url}",
    configuration = UserServiceClientConfig.class,
    fallback = UserServiceClientFallback.class
)
public interface UserServiceClient {
    @GetMapping("/api/v1/users/{userId}")
    ApiResponse<UserDTO> getUserById(@PathVariable("userId") Long userId);
    
    @PostMapping("/api/v1/users/batch")
    ApiResponse<List<UserDTO>> getUsersByIds(@RequestBody List<Long> userIds);
}
```

### 2. 客户端配置
```java
public class UserServiceClientConfig {
    @Bean
    public RequestInterceptor requestInterceptor() {
        return requestTemplate -> {
            String token = SecurityContextHolder.getContext().getAuthentication().getToken();
            requestTemplate.header("Authorization", "Bearer " + token);
            requestTemplate.header("X-Trace-Id", TraceContext.getTraceId());
        };
    }
    
    @Bean
    public Request.Options feignRequestOptions() {
        return new Request.Options(5000, 30000);
    }
}
```

### 3. 服务降级实现
```java
@Component
public class UserServiceClientFallback implements UserServiceClient {
    @Override
    public ApiResponse<UserDTO> getUserById(Long userId) {
        UserDTO defaultUser = UserDTO.builder()
            .id(userId)
            .username("未知用户")
            .status("UNKNOWN")
            .build();
        return ApiResponse.success(defaultUser);
    }
}
```

## 熔断器配置

### Circuit Breaker配置
```java
@Bean
public Customizer<Resilience4JCircuitBreakerFactory> circuitBreakerCustomizer() {
    return factory -> factory.configure(builder -> builder
        .timeLimiterConfig(TimeLimiterConfig.custom()
            .timeoutDuration(Duration.ofSeconds(3))
            .build())
        .circuitBreakerConfig(CircuitBreakerConfig.custom()
            .failureRateThreshold(50)
            .waitDurationInOpenState(Duration.ofMillis(1000))
            .slidingWindowSize(10)
            .minimumNumberOfCalls(5)
            .build()),
        "userService");
}
```

## 服务限流

### 限流配置
```java
@Bean
public RateLimiterRegistry rateLimiterRegistry() {
    return RateLimiterRegistry.of(
        RateLimiterConfig.custom()
            .limitForPeriod(100)
            .limitRefreshPeriod(Duration.ofSeconds(1))
            .timeoutDuration(Duration.ofMillis(100))
            .build()
    );
}
```

## 服务监控

### 1. 调用链监控
使用Span和Timer记录服务调用

### 2. 依赖关系可视化
生成服务依赖图和风险分析

## 输入要求
1. 系统架构图和服务划分
2. 业务流程和调用链
3. 性能指标和SLA要求
4. 容错和降级需求
5. 监控和告警要求

## 输出要求
1. 服务依赖关系图
2. 服务调用客户端代码
3. 熔断器和限流配置
4. 降级和容错策略
5. 健康检查和监控实现
6. 依赖风险评估报告

## 质量检查
- [ ] 依赖关系清晰无循环
- [ ] 容错机制完善
- [ ] 监控覆盖全面
- [ ] 性能指标明确
- [ ] 安全风险可控
- [ ] 扩展性考虑充分
