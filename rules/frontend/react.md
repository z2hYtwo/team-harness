# React 前端开发规范

## 目的

定义 React 前端开发规范，确保代码一致性、可维护性和高质量。

---

## 适用范围

- **React**: React 18+
- **构建**: Vite / Create React App
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
├── pages/          # 页面组件
├── hooks/          # 自定义 Hooks
├── store/          # 状态管理 (Zustand/Redux)
├── api/            # API 接口
├── utils/          # 工具函数
├── types/          # TypeScript 类型
├── styles/         # 全局样式
└── App.tsx
```

---

## 代码风格

### 组件命名
- 组件文件名使用 PascalCase: `UserProfile.tsx`
- 注释使用中文
- 使用函数组件 + Hooks

### 函数组件
```tsx
import { useState, useEffect, FC } from 'react'

interface UserCardProps {
  userId: number
  userName: string
  onSubmit?: (data: FormData) => void
}

export const UserCard: FC<UserCardProps> = ({ 
  userId, 
  userName, 
  onSubmit 
}) => {
  const [count, setCount] = useState(0)
  
  useEffect(() => {
    console.log('组件已挂载')
  }, [])
  
  const handleClick = () => {
    setCount(count + 1)
  }
  
  return (
    <div className="user-card">
      <h1>{userName}</h1>
      <button onClick={handleClick}>点击: {count}</button>
    </div>
  )
}
```

---

## Hooks 规范

### 自定义 Hooks
```tsx
// hooks/useCounter.ts
import { useState, useCallback } from 'react'

export function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue)
  
  const increment = useCallback(() => {
    setCount(c => c + 1)
  }, [])
  
  const decrement = useCallback(() => {
    setCount(c => c - 1)
  }, [])
  
  const reset = useCallback(() => {
    setCount(initialValue)
  }, [initialValue])
  
  return { count, increment, decrement, reset }
}

// 使用
const { count, increment } = useCounter(10)
```

### Hooks 规则
- Hooks 只在顶层调用
- Hooks 只在 React 函数中调用
- 自定义 Hooks 以 `use` 开头
- 依赖数组要完整

---

## 状态管理 (Zustand)

```typescript
// store/userStore.ts
import { create } from 'zustand'

interface User {
  id: number
  name: string
  email: string
}

interface UserStore {
  user: User | null
  token: string
  isLoggedIn: boolean
  login: (username: string, password: string) => Promise<void>
  logout: () => void
}

export const useUserStore = create<UserStore>((set, get) => ({
  user: null,
  token: '',
  isLoggedIn: false,
  
  login: async (username, password) => {
    const response = await api.login({ username, password })
    set({ 
      user: response.user, 
      token: response.token,
      isLoggedIn: true 
    })
  },
  
  logout: () => {
    set({ user: null, token: '', isLoggedIn: false })
  }
}))

// 使用
const { user, login, logout } = useUserStore()
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

// Props 类型
export interface UserCardProps {
  user: User
  onEdit?: (user: User) => void
  onDelete?: (userId: number) => void
}
```

### 组件类型
```tsx
import { FC, ReactNode, PropsWithChildren } from 'react'

// 带 children 的组件
interface ContainerProps {
  title: string
}

export const Container: FC<PropsWithChildren<ContainerProps>> = ({ 
  title, 
  children 
}) => {
  return (
    <div>
      <h1>{title}</h1>
      {children}
    </div>
  )
}
```

---

## 性能优化

### React.memo
```tsx
// 避免不必要的重渲染
export const UserCard = React.memo<UserCardProps>(({ user }) => {
  return <div>{user.name}</div>
}, (prevProps, nextProps) => {
  // 自定义比较
  return prevProps.user.id === nextProps.user.id
})
```

### useMemo 和 useCallback
```tsx
function UserList({ users, onSelect }) {
  // 缓存计算结果
  const sortedUsers = useMemo(() => {
    return users.sort((a, b) => a.name.localeCompare(b.name))
  }, [users])
  
  // 缓存回调函数
  const handleClick = useCallback((id: number) => {
    onSelect(id)
  }, [onSelect])
  
  return (
    <ul>
      {sortedUsers.map(user => (
        <li key={user.id} onClick={() => handleClick(user.id)}>
          {user.name}
        </li>
      ))}
    </ul>
  )
}
```

### 代码分割
```tsx
import { lazy, Suspense } from 'react'

// 路由懒加载
const Users = lazy(() => import('./pages/Users'))

function App() {
  return (
    <Suspense fallback={<div>加载中...</div>}>
      <Routes>
        <Route path="/users" element={<Users />} />
      </Routes>
    </Suspense>
  )
}
```

---

## 样式方案

### CSS Modules
```tsx
import styles from './UserCard.module.css'

export const UserCard = () => {
  return (
    <div className={styles.container}>
      <h1 className={styles.title}>标题</h1>
    </div>
  )
}
```

### Tailwind CSS
```tsx
export const UserCard = () => {
  return (
    <div className="p-4 bg-white rounded-lg shadow">
      <h1 className="text-xl font-bold">标题</h1>
    </div>
  )
}
```

---

## 测试规范

### 组件测试 (Vitest + Testing Library)
```tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { describe, it, expect, vi } from 'vitest'
import { UserCard } from './UserCard'

describe('UserCard', () => {
  it('渲染用户信息', () => {
    const user = { id: 1, name: '张三', email: 'test@example.com' }
    render(<UserCard user={user} />)
    
    expect(screen.getByText('张三')).toBeInTheDocument()
  })
  
  it('点击触发回调', () => {
    const handleClick = vi.fn()
    render(<UserCard onEdit={handleClick} />)
    
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
})
```

---

## 最佳实践

### 组件设计
- 组件职责单一
- Props 类型明确
- 避免深层嵌套（< 3 层）
- 提取可复用逻辑为 Hooks

### 状态管理
- 本地状态用 useState
- 跨组件状态用 Context 或 Zustand
- 服务端状态用 React Query

### 命名规范
- 组件: PascalCase
- 函数/变量: camelCase
- 常量: UPPER_SNAKE_CASE
- 类型/接口: PascalCase

---

## 开发原则

- **类型安全**: 充分利用 TypeScript
- **性能优先**: 使用 memo、useMemo、useCallback
- **代码复用**: 提取自定义 Hooks
- **注释清晰**: 复杂逻辑用中文注释
- **遵循规范**: ESLint + Prettier
- **可测试性**: 便于单元测试
