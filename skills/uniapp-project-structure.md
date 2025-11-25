# uni-app移动端工程结构设计

## 技能描述
根据业务需求和技术栈，生成完整的uni-app移动端工程结构设计方案，支持APP、小程序、H5多端开发。

## 角色定义
你是一位经验丰富的移动端架构师，擅长设计和构建高质量、可维护、可扩展的跨端移动应用工程结构。

## 技术栈
```yaml
技术栈:
  框架: uni-app (Vue 3版本)
  UI组件库: uni-ui
  语言: JavaScript/TypeScript
  状态管理: Pinia / Vuex 4
  HTTP客户端: uni.request / Axios适配
  跨端支持: APP、小程序、H5
  构建工具: HBuilderX / CLI
```

## 工程结构规范

### 目录结构
```
├── components/                # 组件
│   ├── common/                # 通用组件
│   └── business/              # 业务组件
├── pages/                     # 页面
│   ├── index/                 # 首页
│   ├── product/               # 商品页面
│   ├── cart/                  # 购物车页面
│   ├── order/                 # 订单页面
│   └── user/                  # 用户页面
├── services/                  # API服务
│   ├── index.ts               # API入口
│   └── modules/               # 业务API模块
├── stores/                    # 状态管理
│   ├── index.ts               # Store入口
│   └── modules/               # 状态模块
├── static/                    # 静态资源
├── styles/                    # 样式文件
│   ├── common.scss            # 公共样式
│   └── variables.scss         # 样式变量
├── types/                     # TypeScript类型定义
├── utils/                     # 工具函数
├── App.vue                    # 根组件
├── main.ts                    # 入口文件
├── manifest.json              # 应用配置
├── pages.json                 # 页面配置
├── tsconfig.json              # TypeScript配置
└── vite.config.ts             # Vite配置
```

### 页面配置
```json
// pages.json
{
  "pages": [
    {
      "path": "pages/index/index",
      "style": {
        "navigationBarTitleText": "首页"
      }
    },
    {
      "path": "pages/product/list",
      "style": {
        "navigationBarTitleText": "商品列表"
      }
    },
    {
      "path": "pages/user/profile",
      "style": {
        "navigationBarTitleText": "个人中心"
      }
    }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "应用名称",
    "navigationBarBackgroundColor": "#FFFFFF",
    "backgroundColor": "#F5F5F5"
  },
  "tabBar": {
    "color": "#999999",
    "selectedColor": "#1890FF",
    "list": [
      {
        "pagePath": "pages/index/index",
        "text": "首页",
        "iconPath": "static/tabbar/home.png",
        "selectedIconPath": "static/tabbar/home-active.png"
      },
      {
        "pagePath": "pages/user/profile",
        "text": "我的",
        "iconPath": "static/tabbar/user.png",
        "selectedIconPath": "static/tabbar/user-active.png"
      }
    ]
  }
}
```

## API服务设计

### 请求封装
```typescript
// services/request.ts
const BASE_URL = import.meta.env.VITE_API_BASE_URL

export interface RequestOptions {
  url: string
  method?: 'GET' | 'POST' | 'PUT' | 'DELETE'
  data?: any
  header?: object
}

export const request = <T>(options: RequestOptions): Promise<T> => {
  return new Promise((resolve, reject) => {
    uni.request({
      url: BASE_URL + options.url,
      method: options.method || 'GET',
      data: options.data,
      header: {
        'Content-Type': 'application/json',
        'Authorization': uni.getStorageSync('token') || '',
        ...options.header
      },
      success: (res) => {
        if (res.statusCode === 200) {
          resolve(res.data as T)
        } else if (res.statusCode === 401) {
          uni.removeStorageSync('token')
          uni.navigateTo({ url: '/pages/auth/login' })
          reject(new Error('未授权'))
        } else {
          reject(new Error('请求失败'))
        }
      },
      fail: (err) => {
        uni.showToast({ title: '网络错误', icon: 'none' })
        reject(err)
      }
    })
  })
}
```

## 状态管理设计

```typescript
// stores/modules/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  const userInfo = ref(null)
  const token = ref(uni.getStorageSync('token') || '')

  const isLoggedIn = computed(() => !!token.value)

  async function login(params) {
    const res = await loginApi(params)
    token.value = res.token
    uni.setStorageSync('token', res.token)
    await getUserInfo()
  }

  async function logout() {
    token.value = ''
    userInfo.value = null
    uni.removeStorageSync('token')
  }

  return { userInfo, token, isLoggedIn, login, logout }
}, {
  persist: {
    key: 'user-store',
    storage: {
      getItem: (key) => uni.getStorageSync(key),
      setItem: (key, value) => uni.setStorageSync(key, value)
    }
  }
})
```

## 输入要求
1. 项目名称和描述
2. 技术栈选择
3. 目标平台（APP/小程序/H5）
4. 功能模块划分
5. 第三方集成需求

## 输出要求
1. 完整项目目录结构
2. 核心配置文件
3. 主要代码文件模板
4. 构建和部署说明
5. 开发规范文档

## 质量检查
- [ ] 目录结构清晰合理
- [ ] 跨端兼容性考虑
- [ ] 配置文件完整
- [ ] TypeScript类型定义完整
- [ ] 组件化设计合理
- [ ] 状态管理设计清晰
