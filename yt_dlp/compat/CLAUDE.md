[根目录](../../CLAUDE.md) > [yt_dlp](../) > **compat (兼容层模块)**

# compat 兼容层模块

## 模块职责

compat 模块负责 Python 版本兼容性处理，确保 yt-dlp 在不同 Python 版本上正常运行：
- Python 3.10+ 版本兼容性
- 统一的接口封装
- 向后兼容支持
- 弃用功能管理
- 遗留代码维护

## 入口与启动

### 主要入口
- **`__init__.py`**: 兼容层公共接口
- **`compat_utils.py`**: 兼容性工具函数
- **`_deprecated.py`**: 已弃用功能
- **`_legacy.py`**: 遗留代码支持

### 版本支持
```python
# 最低版本要求
if sys.version_info < (3, 10):
    raise ImportError(
        'You are using an unsupported version of Python. '
        'Only Python versions 3.10 and above are supported by yt-dlp'
    )
```

## 对外接口

### 核心兼容功能

#### 版本检查
```python
import sys

def check_python_version():
    """检查 Python 版本兼容性"""
    return sys.version_info >= (3, 10)
```

#### 类型提示兼容
```python
from __future__ import annotations
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    # 仅在类型检查时执行的代码
    pass
```

## 关键依赖与配置

### Python 版本支持
- **最低版本**: Python 3.10
- **支持版本**: 3.10, 3.11, 3.12, 3.13, 3.14
- **实现**: CPython, PyPy

### 兼容性封装

#### urllib 兼容
```python
# 兼容 Python 2/3 的 urllib
from ..compat import urllib
from ..compat.urllib.request import Request, urlopen
from ..compat.urllib.parse import urlparse, urljoin
```

#### 遗留函数
```python
# 已弃用但保留的函数
def compat_str(s):
    """兼容字符串类型（已弃用，使用 str）"""
    warnings.warn('Use str instead', DeprecationWarning)
    return str(s)

def compat_b64decode(s):
    """兼容 Base64 解码（已弃用，使用 base64.b64decode）"""
    from base64 import b64decode
    return b64decode(s)
```

## 主要功能模块

### 工具函数 (`compat_utils.py`)
```python
def passthrough_module(module_name, target_module):
    """透传模块，将所有属性转发到目标模块

    Args:
        module_name: 当前模块名 __name__
        target_module: 目标模块名
    """
    import sys
    module = sys.modules[module_name]
    target = sys.modules[target_module]

    # 复制所有属性
    for name in dir(target):
        if not name.startswith('_'):
            setattr(module, name, getattr(target, name))
```

### 弃用功能 (`_deprecated.py`)
```python
# 已弃用的函数和类
# 保留用于向后兼容，但会发出警告

def compat_shlex_quote(s):
    """兼容 shlex.quote（已弃用）"""
    warnings.warn(
        'Use yt_dlp.utils.shell_quote instead',
        DeprecationWarning
    )
    from ..utils import shell_quote
    return shell_quote(s)
```

### 遗留代码 (`_legacy.py`)
```python
# 遗留的兼容代码
# 用于支持旧版本 API

class LegacyCompat:
    """遗留兼容类"""

    @staticmethod
    def old_method():
        """旧方法实现"""
        pass
```

### urllib 兼容 (`urllib/`)
```python
# Python 2/3 urllib 兼容层
# 统一 urllib 接口

from ..compat import urllib

# 使用统一接口
from ..compat.urllib.request import Request
from ..compat.urllib.parse import urlparse, urlencode
from ..compat.urllib.error import HTTPError, URLError
```

### 其他兼容模块

#### imghdr 兼容
```python
# 图像格式检测兼容
# Python 3.13+ 移除了 imghdr 模块
from ..compat import imghdr
```

#### shutil 兼容
```python
# 文件操作兼容
from ..compat import shutil
```

## 数据模型

### 版本信息
```python
import sys

PYTHON_VERSION = sys.version_info
IS_PY2 = PYTHON_VERSION[0] == 2
IS_PY3 = PYTHON_VERSION[0] == 3
MIN_VERSION = (3, 10)
MAX_VERSION = (3, 14)
```

### 弃用警告
```python
import warnings

def deprecated_function():
    """已弃用函数"""
    warnings.warn(
        'This function is deprecated and will be removed in a future version',
        DeprecationWarning,
        stacklevel=2
    )
```

## 测试与质量

### 测试覆盖
- **版本测试**: 各 Python 版本测试
- **兼容性测试**: 向后兼容性验证
- **弃用测试**: 弃用警告测试

### 相关测试文件
- `test/test_compat.py` - 兼容层测试

## 常见问题 (FAQ)

### Q: 为什么需要兼容层？
A: 为了支持多个 Python 版本（3.10-3.14），需要处理版本差异。

### Q: 已弃用函数会被移除吗？
A: 会在未来版本中移除，建议迁移到新 API。

### Q: 如何使用兼容层？
A: 直接从 `yt_dlp.compat` 导入，会自动选择正确的实现。

### Q: Python 3.10 之前的版本支持吗？
A: 不支持，最低要求是 Python 3.10。

## 相关文件清单

### 核心文件
- `__init__.py` - 兼容层公共接口
- `compat_utils.py` - 兼容性工具函数
- `_deprecated.py` - 已弃用功能
- `_legacy.py` - 遗留代码支持

### urllib 兼容
- `urllib/__init__.py` - urllib 兼容层
- `urllib/request.py` - urllib.request 兼容
- `urllib/parse.py` - urllib.parse 兼容

### 其他兼容
- `imghdr.py` - 图像格式检测兼容
- `shutil.py` - 文件操作兼容

## 使用示例

### 使用兼容层
```python
# 导入兼容的 urllib
from yt_dlp.compat import urllib
from yt_dlp.compat.urllib.request import Request, urlopen
from yt_dlp.compat.urllib.parse import urlparse, urlencode

# 使用兼容的函数
url = 'https://example.com'
parsed = urlparse(url)
```

### 版本检查
```python
import sys
from yt_dlp.compat import check_python_version

if not check_python_version():
    raise RuntimeError('Python 3.10+ required')
```

### 迁移弃用 API
```python
# 旧 API（已弃用）
from yt_dlp.compat import compat_str
s = compat_str(b'hello')  # 弃用

# 新 API（推荐）
s = str(b'hello', 'utf-8')  # 推荐
```

## 扩展接口

### 添加新兼容函数
```python
# 在 compat_utils.py 中添加
def compat_new_function():
    """新兼容函数"""
    pass

# 在 __init__.py 中导出
from .compat_utils import compat_new_function
```

### 注册弃用警告
```python
import warnings

def deprecated_api():
    """已弃用的 API"""
    warnings.warn(
        'Use new_api() instead',
        DeprecationWarning,
        stacklevel=2
    )
```

## 兼容策略

### 版本支持策略
- **支持窗口**: 当前版本 - 2 到 + 1
- **弃用期**: 至少一个主版本
- **移除策略**: 在次版本中移除

### 弃用流程
1. 标记为弃用，发出警告
2. 提供迁移指南
3. 在适当版本中移除

### 向后兼容
- 保留公共 API
- 新增功能通过参数控制
- 避免破坏性变更

## 变更记录 (Changelog)

### 2026-01-12
- 创建兼容层模块文档
- 分析兼容性处理机制
- 文档覆盖兼容层 8 个文件
- 建立版本兼容指南

### 功能特点
- 支持 Python 3.10-3.14
- 统一的兼容接口
- 完整的弃用管理
- 向后兼容保证

### 未来计划
- 详细分析兼容性实现
- 补充迁移指南
- 添加版本升级建议

## 版本兼容矩阵

| Python 版本 | 支持状态 | 推荐版本 |
|------------|---------|---------|
| 3.10 | ✅ 支持 | 稳定 |
| 3.11 | ✅ 支持 | 推荐 |
| 3.12 | ✅ 支持 | 推荐 |
| 3.13 | ✅ 支持 | 最新 |
| 3.14 | ✅ 支持 | 开发 |
| < 3.10 | ❌ 不支持 | - |

## 迁移指南

### 从旧版本迁移
1. 升级到 Python 3.10+
2. 更新弃用 API 调用
3. 运行测试验证
4. 查看弃用警告

### 弃用 API 替换
```python
# 旧 API → 新 API
compat_str → str
compat_b64decode → base64.b64decode
compat_urlparse → urllib.parse
compat_shlex_quote → utils.shell_quote
```
