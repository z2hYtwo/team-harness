# Python 开发规范

## 目的

定义 Python 开发规范，确保代码一致性、可维护性和高质量。

---

## 适用范围

- **Python**: Python 3.10+
- **框架**: FastAPI, Flask, Django
- **AI/ML**: PyTorch, TensorFlow
- **环境**: Conda
- **系统**: Ubuntu
- **代码风格**: Google Python Style Guide + PEP 8

---

## 环境管理

### Conda 环境
```bash
# 创建环境
conda create -n project-name python=3.10

# 激活环境
conda activate project-name

# 导出环境
conda env export > environment.yml

# 从文件创建环境
conda env create -f environment.yml
```

### 依赖管理
```bash
# requirements.txt (生产依赖)
pip install -r requirements.txt

# requirements-dev.txt (开发依赖)
pip install -r requirements-dev.txt
```

---

## 项目结构

```
project-root/
├── src/                 # 源代码
│   ├── api/            # API 接口
│   ├── models/         # 数据模型
│   ├── services/       # 业务逻辑
│   ├── utils/          # 工具函数
│   └── config/         # 配置
├── tests/              # 测试代码
├── scripts/            # 脚本工具
├── data/               # 数据目录
├── models/             # 模型文件（AI 项目）
├── notebooks/          # Jupyter Notebooks
├── requirements.txt    # 依赖
├── pyproject.toml      # 项目配置
└── README.md
```

---

## 代码风格

### 基础规范
- 遵循 **Google Python Style Guide** + **PEP 8**
- 使用 4 空格缩进
- 行宽限制 100 字符
- 注释使用中文
- 使用类型提示 (Type Hints)

### 命名规范
```python
# 模块名: 小写+下划线
# user_service.py

# 类名: 大驼峰
class UserService:
    pass

# 函数/变量: 小写+下划线
def get_user_by_id(user_id: int) -> User:
    user_name = "张三"
    return user

# 常量: 全大写+下划线
MAX_RETRY_COUNT = 3
DEFAULT_TIMEOUT = 30

# 私有成员: 单下划线前缀
class User:
    def __init__(self):
        self._password = ""  # 私有属性
    
    def _internal_method(self):  # 私有方法
        pass
```

### 类型提示
```python
from typing import List, Dict, Optional, Union, Tuple

def process_users(
    users: List[Dict[str, str]],
    filter_by: Optional[str] = None
) -> Tuple[List[str], int]:
    """处理用户列表
    
    Args:
        users: 用户字典列表
        filter_by: 过滤条件
    
    Returns:
        处理后的用户名列表和总数
    """
    names = [u["name"] for u in users]
    return names, len(names)
```

---

## 函数和类

### 函数规范
```python
def calculate_total(
    items: List[Item],
    discount: float = 0.0,
    tax_rate: float = 0.1
) -> float:
    """计算总价
    
    Args:
        items: 商品列表
        discount: 折扣率 (0-1)
        tax_rate: 税率 (0-1)
    
    Returns:
        含税总价
    
    Raises:
        ValueError: 当折扣率或税率无效时
    """
    if not 0 <= discount <= 1:
        raise ValueError(f"折扣率必须在 0-1 之间: {discount}")
    
    subtotal = sum(item.price for item in items)
    after_discount = subtotal * (1 - discount)
    total = after_discount * (1 + tax_rate)
    
    return round(total, 2)
```

### 类规范
```python
from dataclasses import dataclass
from datetime import datetime

@dataclass
class User:
    """用户模型
    
    Attributes:
        id: 用户 ID
        username: 用户名
        email: 邮箱
        created_at: 创建时间
    """
    id: int
    username: str
    email: str
    created_at: datetime = datetime.now()
    
    def __post_init__(self):
        """初始化后的验证"""
        if not self.username:
            raise ValueError("用户名不能为空")
```

---

## 异常处理

```python
# ✅ 推荐：具体的异常类型
try:
    user = get_user(user_id)
except UserNotFoundException as e:
    logger.error(f"用户不存在: {user_id}", exc_info=True)
    raise
except DatabaseError as e:
    logger.error("数据库错误", exc_info=True)
    return None

# ✅ 推荐：自定义异常
class BusinessException(Exception):
    """业务异常基类"""
    def __init__(self, message: str, code: str):
        self.message = message
        self.code = code
        super().__init__(self.message)

class UserNotFoundException(BusinessException):
    """用户不存在异常"""
    def __init__(self, user_id: int):
        super().__init__(
            f"用户不存在: {user_id}",
            "USER_NOT_FOUND"
        )

# ❌ 避免：捕获所有异常（裸 except）
try:
    do_something()
except:  # 禁止裸 except
    pass
```

---

## 异步编程 (asyncio)

```python
import asyncio
from typing import List

# 异步函数
async def fetch_user(user_id: int) -> User:
    """异步获取用户"""
    async with httpx.AsyncClient() as client:
        response = await client.get(f"/users/{user_id}")
        return User(**response.json())

# 并发执行
async def fetch_multiple_users(user_ids: List[int]) -> List[User]:
    """并发获取多个用户"""
    tasks = [fetch_user(uid) for uid in user_ids]
    users = await asyncio.gather(*tasks)
    return users

# 统一使用 asyncio（不混用其他异步框架）
```

---

## FastAPI 规范

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel, Field
from typing import List

app = FastAPI()

# Pydantic 模型
class CreateUserRequest(BaseModel):
    username: str = Field(..., min_length=3, max_length=20)
    email: str = Field(..., regex=r"^[\w\.-]+@[\w\.-]+\.\w+$")
    password: str = Field(..., min_length=8)
    
    class Config:
        schema_extra = {
            "example": {
                "username": "zhangsan",
                "email": "zhangsan@example.com",
                "password": "Password123"
            }
        }

class UserResponse(BaseModel):
    id: int
    username: str
    email: str
    
    class Config:
        orm_mode = True

# API 路由
@app.post("/users", response_model=UserResponse, status_code=201)
async def create_user(
    request: CreateUserRequest,
    service: UserService = Depends(get_user_service)
) -> UserResponse:
    """创建用户
    
    Args:
        request: 创建用户请求
        service: 用户服务（依赖注入）
    
    Returns:
        创建的用户信息
    
    Raises:
        HTTPException: 400 用户名已存在
    """
    try:
        user = await service.create_user(
            username=request.username,
            email=request.email,
            password=request.password
        )
        return UserResponse.from_orm(user)
    except UsernameExistsError:
        raise HTTPException(status_code=400, detail="用户名已存在")
```

---

## PyTorch 规范

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import Dataset, DataLoader

# 自定义数据集
class CustomDataset(Dataset):
    """自定义数据集"""
    
    def __init__(self, data_path: str):
        """初始化数据集
        
        Args:
            data_path: 数据路径
        """
        self.data = self._load_data(data_path)
    
    def __len__(self) -> int:
        return len(self.data)
    
    def __getitem__(self, idx: int) -> Tuple[torch.Tensor, torch.Tensor]:
        """获取单个样本
        
        Args:
            idx: 索引
        
        Returns:
            特征和标签
        """
        return self.data[idx]

# 模型定义
class MyModel(nn.Module):
    """自定义模型"""
    
    def __init__(self, input_dim: int, hidden_dim: int, output_dim: int):
        """初始化模型
        
        Args:
            input_dim: 输入维度
            hidden_dim: 隐藏层维度
            output_dim: 输出维度
        """
        super().__init__()
        self.fc1 = nn.Linear(input_dim, hidden_dim)
        self.relu = nn.ReLU()
        self.fc2 = nn.Linear(hidden_dim, output_dim)
    
    def forward(self, x: torch.Tensor) -> torch.Tensor:
        """前向传播
        
        Args:
            x: 输入张量
        
        Returns:
            输出张量
        """
        x = self.fc1(x)
        x = self.relu(x)
        x = self.fc2(x)
        return x

# 训练循环
def train_epoch(
    model: nn.Module,
    dataloader: DataLoader,
    optimizer: optim.Optimizer,
    criterion: nn.Module,
    device: torch.device
) -> float:
    """训练一个 epoch
    
    Args:
        model: 模型
        dataloader: 数据加载器
        optimizer: 优化器
        criterion: 损失函数
        device: 设备 (CPU/GPU)
    
    Returns:
        平均损失
    """
    model.train()
    total_loss = 0.0
    
    for batch_idx, (data, target) in enumerate(dataloader):
        data, target = data.to(device), target.to(device)
        
        optimizer.zero_grad()
        output = model(data)
        loss = criterion(output, target)
        loss.backward()
        optimizer.step()
        
        total_loss += loss.item()
    
    return total_loss / len(dataloader)
```

---

## 测试规范

```python
import pytest
from unittest.mock import Mock, patch

# 测试函数
def test_create_user_success():
    """测试创建用户 - 成功"""
    # Given
    service = UserService()
    request = CreateUserRequest(
        username="zhangsan",
        email="test@example.com",
        password="Password123"
    )
    
    # When
    user = service.create_user(request)
    
    # Then
    assert user is not None
    assert user.username == "zhangsan"

def test_create_user_username_exists():
    """测试创建用户 - 用户名已存在"""
    # Given
    service = UserService()
    service.repository.exists_by_username = Mock(return_value=True)
    
    # When & Then
    with pytest.raises(UsernameExistsError):
        service.create_user(request)

# 测试类
class TestUserService:
    """用户服务测试"""
    
    @pytest.fixture
    def service(self):
        """创建服务实例"""
        return UserService()
    
    def test_get_user_by_id(self, service):
        """测试根据 ID 获取用户"""
        user = service.get_user_by_id(1)
        assert user.id == 1
```

---

## 性能优化

```python
# 使用列表推导式
# ✅ 快速
squares = [x**2 for x in range(1000)]

# ❌ 慢
squares = []
for x in range(1000):
    squares.append(x**2)

# 使用生成器（大数据）
# ✅ 内存友好
def read_large_file(file_path: str):
    """逐行读取大文件"""
    with open(file_path) as f:
        for line in f:
            yield line.strip()

# 使用缓存
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n: int) -> int:
    """计算斐波那契数（带缓存）"""
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

---

## 开发原则

- **可读性优先**: Readable
- **显式优于隐式**: Explicit is better than implicit
- **简单优于复杂**: Simple is better than complex
- **类型提示**: 充分使用类型提示
- **文档字符串**: 函数/类必须有 docstring
- **异常处理**: 明确的异常类型，禁止裸 except
- **异步统一**: 统一使用 asyncio
- **测试覆盖**: 核心逻辑有单元测试
- **注释清晰**: 复杂逻辑用中文注释
- **遵循 PEP 8**: 代码风格一致
