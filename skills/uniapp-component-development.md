# uni-app移动端组件开发

## 技能描述
根据业务需求，设计和实现符合uni-app最佳实践的移动端组件，确保组件在APP、小程序、H5等多端的兼容性。

## 角色定义
你是一位经验丰富的移动端开发工程师，擅长使用uni-app开发高质量、可复用、跨平台兼容的移动端组件。

## 技术栈
```yaml
技术栈:
  框架: uni-app (Vue 3版本)
  UI组件库: uni-ui
  语言: JavaScript/TypeScript
  样式: SCSS
  跨端: APP、微信小程序、H5
```

## 组件设计原则

### 1. 跨端兼容原则
```yaml
跨端兼容:
  优先使用uni-app内置组件: view, text, image, scroll-view等
  条件编译处理平台差异: #ifdef, #ifndef
  使用rpx单位适配不同屏幕
  避免使用平台特有API
  使用uni-ui组件库
```

### 2. 组件分类
```yaml
组件分类:
  基础组件: 按钮、图标、标签等
  业务组件: 商品卡片、订单列表等
  页面组件: 完整页面
  布局组件: 导航栏、底部栏等
```

## 组件开发模板

### 业务组件模板
```vue
<template>
  <view class="product-card" @click="handleClick">
    <!-- 商品图片 -->
    <image 
      class="product-image" 
      :src="product.imageUrl" 
      mode="aspectFill"
    />
    
    <!-- 商品信息 -->
    <view class="product-info">
      <text class="product-title">{{ product.title }}</text>
      <view class="product-price-row">
        <text class="product-price">¥{{ product.price.toFixed(2) }}</text>
        <text class="product-original-price" v-if="product.originalPrice">
          ¥{{ product.originalPrice.toFixed(2) }}
        </text>
      </view>
      <view class="product-meta">
        <text class="product-sales">销量 {{ product.sales }}</text>
      </view>
    </view>
    
    <!-- 操作按钮 -->
    <view class="product-actions" v-if="showActions">
      <button 
        class="action-btn cart-btn" 
        @click.stop="handleAddCart"
      >
        加入购物车
      </button>
    </view>
  </view>
</template>

<script setup lang="ts">
interface Product {
  id: string
  title: string
  imageUrl: string
  price: number
  originalPrice?: number
  sales: number
}

const props = withDefaults(defineProps<{
  product: Product
  showActions?: boolean
}>(), {
  showActions: true
})

const emit = defineEmits<{
  (e: 'click', product: Product): void
  (e: 'addCart', product: Product): void
}>()

const handleClick = () => {
  emit('click', props.product)
}

const handleAddCart = () => {
  emit('addCart', props.product)
  uni.showToast({
    title: '已加入购物车',
    icon: 'success'
  })
}
</script>

<style lang="scss" scoped>
.product-card {
  display: flex;
  flex-direction: column;
  background-color: #fff;
  border-radius: 16rpx;
  overflow: hidden;
  box-shadow: 0 2rpx 12rpx rgba(0, 0, 0, 0.1);
}

.product-image {
  width: 100%;
  height: 300rpx;
}

.product-info {
  padding: 20rpx;
}

.product-title {
  font-size: 28rpx;
  font-weight: 500;
  color: #333;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  overflow: hidden;
  line-height: 1.4;
}

.product-price-row {
  display: flex;
  align-items: center;
  margin-top: 12rpx;
}

.product-price {
  font-size: 36rpx;
  font-weight: bold;
  color: #ff4d4f;
}

.product-original-price {
  font-size: 24rpx;
  color: #999;
  text-decoration: line-through;
  margin-left: 12rpx;
}

.product-meta {
  display: flex;
  align-items: center;
  margin-top: 8rpx;
}

.product-sales {
  font-size: 22rpx;
  color: #999;
}

.product-actions {
  padding: 0 20rpx 20rpx;
}

.action-btn {
  width: 100%;
  height: 64rpx;
  border: none;
  border-radius: 32rpx;
  font-size: 26rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cart-btn {
  background-color: #ff4d4f;
  color: #fff;
}
</style>
```

## 页面组件模板

```vue
<template>
  <view class="product-detail">
    <!-- 商品图片轮播 -->
    <swiper class="product-swiper" indicator-dots circular autoplay>
      <swiper-item v-for="(image, index) in product.images" :key="index">
        <image :src="image" mode="aspectFill" class="swiper-image" />
      </swiper-item>
    </swiper>

    <!-- 商品信息 -->
    <view class="product-info">
      <text class="product-title">{{ product.title }}</text>
      <text class="product-price">¥{{ product.price.toFixed(2) }}</text>
    </view>

    <!-- 规格选择 -->
    <view class="product-specs">
      <text class="specs-title">选择规格</text>
      <view class="specs-list">
        <view 
          v-for="(spec, index) in product.specs" 
          :key="index"
          class="spec-item"
          :class="{ active: selectedSpec === spec }"
          @click="selectedSpec = spec"
        >
          {{ spec }}
        </view>
      </view>
    </view>

    <!-- 底部操作栏 -->
    <view class="bottom-bar">
      <view class="bar-item" @click="addToCart">
        <text class="bar-icon">🛒</text>
        <text class="bar-text">加入购物车</text>
      </view>
      <view class="bar-item primary" @click="buyNow">
        <text class="bar-text">立即购买</text>
      </view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useProductStore } from '@/stores/product'
import { useCartStore } from '@/stores/cart'

const props = defineProps<{ productId: string }>()

const productStore = useProductStore()
const cartStore = useCartStore()

const product = ref({ images: [], specs: [] })
const selectedSpec = ref('')

onMounted(async () => {
  const result = await productStore.getProductById(props.productId)
  if (result) {
    product.value = result
    if (result.specs.length > 0) {
      selectedSpec.value = result.specs[0]
    }
  }
})

const addToCart = () => {
  if (!selectedSpec.value) {
    uni.showToast({ title: '请选择规格', icon: 'none' })
    return
  }
  cartStore.addToCart({
    productId: product.value.id,
    spec: selectedSpec.value,
    quantity: 1
  })
  uni.showToast({ title: '已加入购物车', icon: 'success' })
}

const buyNow = () => {
  if (!selectedSpec.value) {
    uni.showToast({ title: '请选择规格', icon: 'none' })
    return
  }
  uni.navigateTo({
    url: `/pages/order/confirm?productId=${product.value.id}&spec=${selectedSpec.value}`
  })
}
</script>
```

## 跨端适配技巧

### 条件编译
```vue
<!-- #ifdef MP-WEIXIN -->
<button open-type="share">分享给好友</button>
<!-- #endif -->

<!-- #ifdef H5 -->
<button @click="handleWebShare">分享</button>
<!-- #endif -->

<!-- #ifdef APP-PLUS -->
<button @click="handleAppShare">分享</button>
<!-- #endif -->
```

### 响应式设计
```scss
.container {
  width: 100%;
  padding: 20rpx;
  
  // 适配刘海屏
  padding-top: calc(20rpx + env(safe-area-inset-top));
  padding-bottom: calc(20rpx + env(safe-area-inset-bottom));
}
```

## 输入要求
1. 组件名称和描述
2. 组件类型
3. 功能需求
4. 目标平台
5. 交互逻辑

## 输出要求
1. 完整的组件代码
2. 组件的类型定义
3. 组件的使用示例
4. 跨端适配说明

## 质量检查
- [ ] 组件符合单一职责原则
- [ ] 多端兼容性良好
- [ ] 使用TypeScript类型定义
- [ ] 进行了性能优化
- [ ] 使用rpx单位适配
