# Vue 3 前端开发规范

## 目的

定义 Vue 3 前端开发规范，确保代码一致性、可维护性和高质量。

---

## 适用范围

- **Vue**: Vue 3 (Composition API)
- **构建**: Vite
- **语言**: TypeScript
- **包管理**: pnpm / npm
- **代码风格**: Google Style (通过 ESLint)

---

## 项目结构

```
src/
├── assets/          # 静态资源
├── components/      # 公共组件
│   ├── common/     # 通用组件
│   └── business/   # 业务组件
├── views/          # 页面组件
├── router/         # 路由配置
├── stores/         # Pinia 状态管理
├── api/            # API 接口
├── utils/          # 工具函数
├── types/          # TypeScript 类型
├── styles/         # 全局样式
└── App.vue
```

---

## 代码风格

### 组件命名
- 组件文件名使用 PascalCase: `UserProfile.vue`
- 组件名多个单词: `BaseButton.vue` (避免单词组件名)
- 注释使用中文

### Composition API
```vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'

// Props 定义
interface Props {
  userId: number
  userName?: string
}
const props = defineProps<Props>()

// Emit 定义
interface Emits {
  (e: 'update:modelValue', value: string): void
  (e: 'submit'): void
}
const emit = defineEmits<Emits>()

// 响应式数据
const count = ref(0)
const doubled = computed(() => count.value * 2)

// 方法
const increment = () => {
  count.value++
}

// 生命周期
onMounted(() => {
  console.log('组件已挂载')
})
</script>

<template>
  <div class="container">
    <h1>{{ userName }}</h1>
    <button @click="increment">{{ count }}</button>
  </div>
</template>

<style scoped lang="scss">
.container {
  padding: 20px;
}
</style>
```

---

## 状态管理 (Pinia)

```typescript
// stores/user.ts
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useUserStore = defineStore('user', () => {
  // State
  const user = ref<User | null>(null)
  const token = ref<string>('')
  
  // Getters
  const isLoggedIn = computed(() => !!token.value)
  const userName = computed(() => user.value?.name ?? '')
  
  // Actions
  async function login(username: string, password: string) {
    const response = await api.login({ username, password })
    user.value = response.user
    token.value = response.token
  }
  
  function logout() {
    user.value = null
    token.value = ''
  }
  
  return { user, token, isLoggedIn, userName, login, logout }
})
```

---

## API 封装

```typescript
// api/user.ts
import request from '@/utils/request'

export interface LoginRequest {
  username: string
  password: string
}

export interface LoginResponse {
  token: string
  user: User
}

export const userApi = {
  // 登录
  login(data: LoginRequest) {
    return request.post<LoginResponse>('/auth/login', data)
  },
  
  // 获取用户信息
  getUserInfo(userId: number) {
    return request.get<User>(`/users/${userId}`)
  },
  
  // 更新用户
  updateUser(userId: number, data: Partial<User>) {
    return request.put<User>(`/users/${userId}`, data)
  }
}
```

---

## TypeScript 规范

### 类型定义
```typescript
// types/user.ts
export interface User {
  id: number
  username: string
  email: string
  avatar?: string
  createdAt: string
}

export type UserRole = 'admin' | 'user' | 'guest'

export interface UserWithRole extends User {
  role: UserRole
}
```

### 组件 Props 类型
```typescript
// 使用 interface 定义 Props
interface Props {
  title: string
  count?: number
  users: User[]
  onSubmit?: (data: FormData) => void
}
```

---

## 组件规范

### 组件拆分
- 单个组件 < 300 行
- 复杂组件拆分为子组件
- 可复用逻辑提取为 composables

### Composables
```typescript
// composables/useCounter.ts
import { ref, computed } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const doubled = computed(() => count.value * 2)
  
  const increment = () => count.value++
  const decrement = () => count.value--
  const reset = () => count.value = initial
  
  return {
    count,
    doubled,
    increment,
    decrement,
    reset
  }
}

// 使用
<script setup>
import { useCounter } from '@/composables/useCounter'

const { count, doubled, increment } = useCounter(10)
</script>
```

---

## 路由规范

```typescript
// router/index.ts
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'Home',
      component: () => import('@/views/Home.vue')
    },
    {
      path: '/users',
      name: 'Users',
      component: () => import('@/views/Users.vue'),
      meta: { requiresAuth: true }
    }
  ]
})

// 路由守卫
router.beforeEach((to, from, next) => {
  const userStore = useUserStore()
  
  if (to.meta.requiresAuth && !userStore.isLoggedIn) {
    next('/login')
  } else {
    next()
  }
})

export default router
```

---

## 样式规范

### CSS 方案
- 优先使用 Scoped CSS
- 全局样式放 `styles/` 目录
- 使用 SCSS 预处理器
- 使用 CSS 变量管理主题

### BEM 命名
```scss
.user-card {
  &__header {
    // ...
  }
  
  &__body {
    // ...
  }
  
  &--large {
    // ...
  }
}
```

---

## 性能优化

### 按需加载
```typescript
// 路由懒加载
const Users = () => import('@/views/Users.vue')

// 组件懒加载
const HeavyComponent = defineAsyncComponent(() =>
  import('@/components/HeavyComponent.vue')
)
```

### 列表优化
```vue
<template>
  <!-- 使用 v-memo 优化大列表 -->
  <div v-for="item in list" :key="item.id" v-memo="[item.id]">
    {{ item.name }}
  </div>
  
  <!-- 虚拟滚动（大数据量） -->
  <VirtualScroller :items="largeList" />
</template>
```

---

## 测试规范

### 单元测试 (Vitest)
```typescript
import { mount } from '@vue/test-utils'
import { describe, it, expect } from 'vitest'
import UserCard from '@/components/UserCard.vue'

describe('UserCard', () => {
  it('渲染用户名称', () => {
    const wrapper = mount(UserCard, {
      props: {
        user: { id: 1, name: '张三' }
      }
    })
    
    expect(wrapper.text()).toContain('张三')
  })
  
  it('点击按钮触发事件', async () => {
    const wrapper = mount(UserCard)
    await wrapper.find('button').trigger('click')
    
    expect(wrapper.emitted()).toHaveProperty('submit')
  })
})
```

---

## 开发原则

- **组件化**: 合理拆分组件
- **类型安全**: 充分利用 TypeScript
- **性能优先**: 关注首屏加载和渲染性能
- **代码复用**: 提取 composables
- **注释清晰**: 复杂逻辑用中文注释
- **遵循规范**: ESLint + Prettier
