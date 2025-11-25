# Vue3 Pinia状态管理

## 技能描述
根据业务需求，设计和实现符合最佳实践的Pinia状态管理方案，包括状态定义、操作方法、持久化存储和状态共享。

## 角色定义
你是一位经验丰富的前端开发工程师，擅长使用Pinia进行状态管理，特别是在Vue 3项目中的应用。

## 技术栈
```yaml
技术栈:
  语言: JavaScript/TypeScript
  框架: Vue 3 (Composition API)
  状态管理: Pinia
  构建工具: Vite 5
```

## 状态管理设计原则

### 1. 状态管理原则
```yaml
状态管理原则:
  单一数据源: 应用的状态应该集中管理
  状态不可变: 状态更新应该通过actions进行
  清晰的状态结构: 状态结构应该清晰，易于理解和维护
  模块化设计: 按功能模块划分状态
  类型安全: 充分利用TypeScript提供类型定义
  持久化策略: 合理使用持久化存储
```

### 2. 状态分类
```yaml
状态分类:
  全局状态: 整个应用共享的状态，如用户信息、主题设置等
  模块状态: 特定模块的状态，如订单列表、商品详情等
  组件状态: 单个组件内部的状态，使用ref/reactive管理
```

## Store开发模板

### 1. 基本Store模板
```typescript
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

interface UserInfo {
  id: string
  name: string
  email: string
  roles: string[]
}

export const useUserStore = defineStore('user', () => {
  // State
  const userInfo = ref<UserInfo | null>(null)
  const token = ref<string>(localStorage.getItem('token') || '')
  const loading = ref<boolean>(false)

  // Getters
  const isAuthenticated = computed(() => !!token.value)
  const hasPermission = (permission: string) => {
    return userInfo.value?.roles.includes(permission) || false
  }

  // Actions
  async function login(params: LoginParams) {
    loading.value = true
    try {
      const response = await loginApi(params)
      token.value = response.data.token
      localStorage.setItem('token', token.value)
      await getUserInfo()
      return { success: true }
    } catch (err) {
      return { success: false, error: '登录失败' }
    } finally {
      loading.value = false
    }
  }

  async function logout() {
    token.value = ''
    userInfo.value = null
    localStorage.removeItem('token')
  }

  return {
    userInfo, token, loading,
    isAuthenticated, hasPermission,
    login, logout
  }
}, {
  persist: {
    key: 'user-store',
    storage: localStorage,
    paths: ['token']
  }
})
```

### 2. Store模块化设计
```typescript
// stores/index.ts
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)

export default pinia
```

### 3. Store使用示例
```vue
<template>
  <div>
    <template v-if="userStore.isAuthenticated">
      <p>欢迎，{{ userStore.userInfo?.name }}!</p>
      <button @click="userStore.logout">登出</button>
    </template>
    <template v-else>
      <button @click="handleLogin">登录</button>
    </template>
  </div>
</template>

<script setup lang="ts">
import { useUserStore } from '@/stores/modules/user'

const userStore = useUserStore()

const handleLogin = async () => {
  await userStore.login({ username: 'admin', password: '123456' })
}
</script>
```

## 状态管理最佳实践

### 1. 合理划分状态
- 全局状态: 用户信息、主题设置、权限信息
- 模块状态: 订单管理、商品管理
- 组件状态: 表单数据、弹窗显示/隐藏

### 2. 持久化策略
```typescript
persist: {
  key: 'user-store',
  storage: localStorage,
  paths: ['token'] // 只持久化token
}

// 敏感数据使用sessionStorage
persist: {
  key: 'auth-store',
  storage: sessionStorage
}
```

### 3. 性能优化
```typescript
// 使用computed缓存计算结果
const doubleCount = computed(() => count.value * 2)

// 使用订阅监听状态变化
const unsubscribe = userStore.$subscribe((mutation, state) => {
  console.log('状态变化:', mutation, state)
})

// 组件卸载时取消订阅
onUnmounted(() => unsubscribe())
```

## 输入要求
1. 状态管理需求描述
2. 状态结构设计
3. 状态操作需求
4. 持久化策略需求
5. 性能要求

## 输出要求
1. 完整的Store代码
2. Store的类型定义
3. Store的使用示例
4. Store的文档说明

## 质量检查
- [ ] 状态结构清晰、易于理解
- [ ] 按功能模块划分状态
- [ ] 使用TypeScript类型定义
- [ ] 合理使用持久化存储
- [ ] 实现了必要的Getters和Actions
- [ ] 考虑了性能优化
