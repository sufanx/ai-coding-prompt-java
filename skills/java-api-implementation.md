# Java RESTful接口实现

## 技能描述
根据接口定义和领域服务，实现完整的Spring Boot应用层接口，包括参数校验、业务编排、异常处理和响应封装。

## 角色定义
你是一位精通应用层开发的工程师，擅长将领域服务封装成RESTful接口，处理Web层逻辑和跨切面关注点。

## 实现规范

### 1. 应用层职责边界
- **负责**: 请求参数解析、参数校验、权限验证、业务编排、响应封装
- **不负责**: 具体业务逻辑实现（委托给领域服务）、数据持久化（通过仓库接口）

### 2. 接口实现结构
```
应用层 (application/)
├── controller/           # RESTful接口实现
├── assembler/            # DTO与领域对象转换
├── dto/                  # 请求/响应数据传输对象
│   ├── request/
│   └── response/
└── interceptor/          # 横切关注点处理
```

### 3. Controller实现模板
```java
@RestController
@RequestMapping("/api/v1/[资源路径]")
@Validated
@Tag(name = "[业务]接口", description = "[业务功能描述]")
public class [业务]Controller {
    
    private final [业务]Service service;
    private final [业务]Assembler assembler;
    
    @Operation(summary = "[接口摘要]")
    @ApiResponses({
        @ApiResponse(responseCode = "200", description = "成功"),
        @ApiResponse(responseCode = "400", description = "参数错误"),
        @ApiResponse(responseCode = "401", description = "未授权")
    })
    @[HTTP方法]Mapping("/[路径]")
    public ResponseEntity<ApiResponse<[响应类型]>> [方法名](
            @Valid @RequestBody [请求类型] request) {
        
        // 1. 参数业务校验
        validateRequest(request);
        
        // 2. 转换为领域对象
        [领域对象] domainObject = assembler.toDomainObject(request);
        
        // 3. 调用领域服务
        [结果对象] result = service.[业务方法](domainObject);
        
        // 4. 封装响应结果
        [响应类型] response = assembler.toResponse(result);
        
        return ResponseEntity.ok(ApiResponse.success(response));
    }
}
```

### 4. DTO设计模板
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class [DTO名称] {
    
    @Schema(description = "[字段描述]", example = "[示例值]", required = true)
    @NotNull(message = "[字段名]不能为空")
    @Size(max = [最大长度], message = "[字段名]长度不能超过[最大长度]")
    private [类型] [字段名];
    
    @AssertTrue(message = "业务校验失败")
    public boolean isValid() {
        // 跨字段业务校验逻辑
        return [校验结果];
    }
}
```

### 5. 装配器模板
```java
@Component
public class [业务]Assembler {
    
    public [领域对象] toDomainObject([请求DTO] request) {
        return [领域对象].builder()
            .[属性](request.get[属性]())
            .build();
    }
    
    public [响应DTO] toResponse([领域对象] domainObject) {
        return [响应DTO].builder()
            .[属性](domainObject.get[属性]())
            .build();
    }
}
```

## 横切关注点处理

### 1. 认证授权拦截器
```java
@Component
public class AuthInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, 
                           HttpServletResponse response, 
                           Object handler) throws Exception {
        // 权限验证逻辑
    }
}
```

### 2. 日志记录拦截器
```java
@Component
public class LoggingInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, 
                           HttpServletResponse response, 
                           Object handler) throws Exception {
        log.info("[请求开始] {} {}", request.getMethod(), request.getRequestURI());
        return true;
    }
}
```

## 输入要求
1. 接口定义文档（OpenAPI/Swagger格式）
2. 领域服务接口定义
3. 数据模型和DTO定义
4. 横切关注点需求（认证、日志、监控等）

## 输出要求
1. 完整的Controller实现代码
2. 请求/响应DTO实现
3. 装配器实现
4. 异常处理机制
5. 拦截器配置
6. 单元测试用例

## 质量检查
- [ ] 参数校验完整且正确
- [ ] 异常处理机制完善
- [ ] 日志记录完整
- [ ] 领域服务调用正确
- [ ] 响应封装符合规范
- [ ] 单元测试覆盖率高
