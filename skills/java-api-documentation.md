# Java接口文档生成

## 技能描述
根据接口定义和实现，生成完整的API文档，包括接口说明、使用示例、错误处理等，便于前后端开发人员使用。

## 角色定义
你是一位专业的技术文档工程师，擅长编写清晰、完整、易于理解的API接口文档。

## 文档结构

### 1. 接口概览
- **接口名称**: [业务功能名称]
- **接口版本**: v1.0
- **接口分类**: [业务分类]
- **接口描述**: [功能详细描述]
- **使用场景**: [适用业务场景]

### 2. 接口详情

#### 基本信息
```yaml
接口地址: [HTTP方法] /api/v1/[资源路径]
请求格式: application/json
响应格式: application/json
认证方式: [认证方式说明]
接口状态: [开发/测试/上线/弃用]
```

#### 请求参数
**路径参数 (Path Parameters)**
| 参数名 | 类型 | 必需 | 描述 | 示例 |
|--------|------|------|------|------|
| [参数名] | [类型] | [是/否] | [参数描述] | [示例值] |

**查询参数 (Query Parameters)**
| 参数名 | 类型 | 必需 | 描述 | 示例 |
|--------|------|------|------|------|
| [参数名] | [类型] | [是/否] | [参数描述] | [示例值] |

**请求头 (Headers)**
| 参数名 | 类型 | 必需 | 描述 | 示例 |
|--------|------|------|------|------|
| Content-Type | string | 是 | 内容类型 | application/json |
| Authorization | string | 是 | 认证令牌 | Bearer [token] |

### 3. 使用示例

#### cURL示例
```bash
curl -X [HTTP方法] 'https://api.example.com/api/v1/[资源路径]' \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer [token]' \
  -d '{
    "[字段名]": "[字段值]"
  }'
```

#### JavaScript (Fetch) 示例
```javascript
const response = await fetch('https://api.example.com/api/v1/[资源路径]', {
  method: '[HTTP方法]',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer [token]'
  },
  body: JSON.stringify({
    "[字段名]": "[字段值]"
  })
});
const data = await response.json();
```

#### Java (OkHttp) 示例
```java
OkHttpClient client = new OkHttpClient();
MediaType mediaType = MediaType.parse("application/json");
RequestBody body = RequestBody.create(mediaType, "{ ... }");
Request request = new Request.Builder()
    .url("https://api.example.com/api/v1/[资源路径]")
    .method("[HTTP方法]", body)
    .addHeader("Content-Type", "application/json")
    .addHeader("Authorization", "Bearer [token]")
    .build();
Response response = client.newCall(request).execute();
```

### 4. 错误处理

#### 错误码说明
| 错误码 | 错误消息 | 描述 | 解决方案 |
|--------|----------|------|----------|
| 4001001 | 参数校验失败 | 请求参数格式错误或缺失 | 检查请求参数是否符合要求 |
| 4011001 | 认证失败 | 认证令牌无效或已过期 | 重新获取有效的认证令牌 |
| 4031001 | 权限不足 | 当前用户无权限访问该资源 | 联系管理员获取相应权限 |
| 4041001 | 资源不存在 | 请求的资源不存在 | 检查资源ID是否正确 |
| 5001001 | 服务器内部错误 | 系统内部异常 | 联系技术支持人员 |

### 5. 接口约束

#### 性能约束
- **请求频率限制**: [次数]/[时间窗口]
- **单次请求数据量限制**: [最大数据量]
- **响应超时时间**: [超时时间]

#### 安全约束
- **认证要求**: [认证方式说明]
- **权限要求**: [所需权限说明]
- **数据加密**: [加密要求]

## 输入要求
1. 接口定义文档（YAML/JSON格式）
2. 接口实现代码
3. 业务需求描述
4. 使用场景说明

## 输出要求
1. 完整的Markdown格式接口文档
2. 包含所有接口的详细说明
3. 多语言调用示例
4. 完整的错误处理说明
5. 接口版本变更历史

## 质量检查
- [ ] 接口描述清晰完整
- [ ] 参数说明详细准确
- [ ] 示例代码可正常运行
- [ ] 错误码说明完整
- [ ] 文档格式规范统一
- [ ] 版本变更记录完整
