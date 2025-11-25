# Vue3 Axios API服务

## 技能描述
根据业务需求，设计和实现符合最佳实践的Axios API服务方案，包括请求配置、拦截器、错误处理和API模块化管理。

## 角色定义
你是一位经验丰富的前端开发工程师，擅长使用Axios进行API调用和管理，特别是在Vue 3项目中的应用。

## 技术栈
```yaml
技术栈:
  语言: JavaScript/TypeScript
  框架: Vue 3 (Composition API)
  HTTP客户端: Axios
  构建工具: Vite 5
```

## API服务设计原则
```yaml
API服务设计原则:
  模块化设计: 按功能模块划分API
  统一配置: 集中管理API请求配置
  拦截器机制: 统一处理请求和响应
  类型安全: 使用TypeScript提供类型定义
  错误处理: 统一的错误处理机制
  取消请求: 支持请求取消
  重试机制: 合理的重试策略
```

## API服务模板

### 1. Axios实例配置
```typescript
// services/api.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios'
import { message } from 'ant-design-vue'
import { useUserStore } from '@/stores/modules/user'

export interface ApiResponse<T = any> {
  code: number
  message: string
  data: T
  success: boolean
}

const axiosInstance: AxiosInstance = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL || '/api',
  timeout: 10000,
  headers: { 'Content-Type': 'application/json' }
})

// 请求拦截器
axiosInstance.interceptors.request.use(
  (config) => {
    const userStore = useUserStore()
    if (userStore.token) {
      config.headers.Authorization = `Bearer ${userStore.token}`
    }
    config.headers['X-Request-ID'] = crypto.randomUUID()
    return config
  },
  (error) => Promise.reject(error)
)

// 响应拦截器
axiosInstance.interceptors.response.use(
  (response: AxiosResponse<ApiResponse>) => {
    const { data } = response
    if (data.success) return response
    message.error(data.message || '请求失败')
    return Promise.reject(new Error(data.message))
  },
  (error) => {
    if (!error.response) {
      message.error('网络错误，请检查网络连接')
      return Promise.reject(error)
    }
    
    const { status, data } = error.response
    switch (status) {
      case 401:
        message.error('登录已过期，请重新登录')
        useUserStore().logout()
        break
      case 403:
        message.error('没有权限访问该资源')
        break
      case 404:
        message.error('请求的资源不存在')
        break
      case 500:
        message.error('服务器内部错误')
        break
      default:
        message.error(data?.message || `请求失败: ${status}`)
    }
    return Promise.reject(error)
  }
)

export const get = <T>(url: string, params?: any) => 
  axiosInstance.get<ApiResponse<T>>(url, { params })

export const post = <T>(url: string, data?: any) => 
  axiosInstance.post<ApiResponse<T>>(url, data)

export const put = <T>(url: string, data?: any) => 
  axiosInstance.put<ApiResponse<T>>(url, data)

export const del = <T>(url: string, params?: any) => 
  axiosInstance.delete<ApiResponse<T>>(url, { params })

export default axiosInstance
```

### 2. API模块化实现
```typescript
// services/modules/auth.ts
import { post, get } from '../api'

export interface LoginParams {
  username: string
  password: string
}

export interface LoginResponse {
  token: string
  expiresIn: number
}

export interface UserInfo {
  id: string
  name: string
  email: string
  roles: string[]
}

export const loginApi = (params: LoginParams) => 
  post<LoginResponse>('/auth/login', params)

export const logoutApi = () => 
  post('/auth/logout')

export const getUserInfoApi = () => 
  get<UserInfo>('/auth/user-info')
```

```typescript
// services/modules/user.ts
import { get, post, put, del } from '../api'

export interface User {
  id: string
  name: string
  email: string
  status: 'active' | 'inactive'
}

export const getUsersApi = (params: PaginationParams) => 
  get<PaginationResponse<User>>('/api/v1/users', params)

export const getUserApi = (userId: string) => 
  get<User>(`/api/v1/users/${userId}`)

export const createUserApi = (params: CreateUserParams) => 
  post<User>('/api/v1/users', params)

export const updateUserApi = (userId: string, params: UpdateUserParams) => 
  put<User>(`/api/v1/users/${userId}`, params)

export const deleteUserApi = (userId: string) => 
  del(`/api/v1/users/${userId}`)
```

## API服务最佳实践

### 1. 模块化管理
```
├── services/
│   ├── api.ts              # Axios实例配置
│   └── modules/
│       ├── auth.ts         # 认证API
│       ├── user.ts         # 用户管理API
│       └── order.ts        # 订单管理API
```

### 2. 请求取消
```typescript
import { cancelToken } from '@/services/api'

const source = cancelToken.source()
apiService.get('/api/data', { cancelToken: source.token })
source.cancel('请求被取消')
```

### 3. 重试机制
```typescript
axiosInstance.interceptors.response.use(
  (response) => response,
  async (error) => {
    const { config } = error
    if (!config || !config.retry) return Promise.reject(error)
    
    config._retryCount = config._retryCount || 0
    if (config._retryCount >= config.retry) return Promise.reject(error)
    
    config._retryCount += 1
    await new Promise(resolve => setTimeout(resolve, 1000))
    return axiosInstance(config)
  }
)
```

## 输入要求
1. API服务需求描述
2. API路径和方法
3. 请求参数和类型
4. 响应数据结构
5. 错误处理需求

## 输出要求
1. 完整的API服务代码
2. API的类型定义
3. API的使用示例
4. API的文档说明

## 质量检查
- [ ] API服务按功能模块划分
- [ ] 使用TypeScript类型定义
- [ ] 实现统一的错误处理机制
- [ ] 实现请求和响应拦截器
- [ ] API方法名称清晰易懂
- [ ] 提供API文档和使用示例
