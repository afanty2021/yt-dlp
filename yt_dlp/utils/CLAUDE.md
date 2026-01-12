[根目录](../../CLAUDE.md) > [yt_dlp](../) > **utils (工具库模块)**

# utils 工具库模块

## 模块职责

utils 模块是 yt-dlp 的工具函数库，提供：
- 通用工具函数集合
- 格式化和解析函数
- 文件操作工具
- 进度报告和用户界面
- 加密解密功能
- 数据遍历和转换工具
- JavaScript 运行时支持

## 入口与启动

### 主要入口
- **`__init__.py`**: 工具库公共接口，导出所有公共函数
- **`_utils.py`**: 核心工具函数实现（大文件，6000+ 行）
- **`progress.py`**: 下载进度报告和终端显示

### 导入方式
```python
from yt_dlp.utils import (
    format_bytes,
    format_seconds,
    sanitize_filename,
    determine_ext,
)
```

## 对外接口

### 核心工具函数

#### 格式化函数
```python
def format_bytes(bytes):
    """格式化字节数为人类可读格式"""

def format_seconds(seconds):
    """格式化秒数为时间字符串"""

def format_field(obj, field, template):
    """格式化字段值"""

def render_table(headers, values):
    """渲染表格数据"""
```

#### 文件操作函数
```python
def sanitize_filename(filename, restricted=False, is_id=False):
    """清理文件名，移除非法字符"""

def determine_ext(url, default_ext='unknown_video'):
    """从 URL 确定文件扩展名"""

def prepend_extension(filename, ext, expected_ext=None):
    """在文件名前添加扩展名"""

def replace_extension(filename, ext, expected_ext=None):
    """替换文件扩展名"""

def make_dir(directory):
    """创建目录，支持路径扩展"""

def timeconvert(timestr):
    """转换时间字符串为秒数"""
```

#### 网络工具函数
```python
def update_url_query(url, query):
    """更新 URL 查询参数"""

def url_or_none(url):
    """验证 URL 格式"""

def parse_duration(s):
    """解析时长字符串为秒数"""

def extract_basic_auth(url):
    """从 URL 提取基本认证信息"""
```

#### 数据处理函数
```python
def int_or_none(v, scale=1, default=None, get_first=False):
    """安全转换为整数"""

def float_or_none(v, scale=1, default=None):
    """安全转换为浮点数"""

def bool_or_none(v, default=None):
    """安全转换为布尔值"""

def str_or_none(v, default=None):
    """安全转换为字符串"""

def parse_bytes(s):
    """解析字节字符串为字节数"""

def parse_codecs(s):
    """解析编解码器字符串"""
```

#### HTML/XML 处理
```python
def clean_html(html):
    """清理 HTML 标签"""

def unescapeHTML(s):
    """反转义 HTML 实体"""

def get_element_by_id(id, html):
    """通过 ID 获取 HTML 元素"""

def get_element_by_attribute(attribute, value, html):
    """通过属性获取 HTML 元素"""
```

## 关键依赖与配置

### 内部依赖
```python
from ..compat import urllib
from ..jsinterp import JSInterpreter
from .traversal import traverse_obj
```

### 外部依赖
- **标准库**: 大部分功能使用 Python 标准库
- **可选依赖**: 某些功能需要额外的 Python 库

### 工具函数分类

#### 字符串处理
- 大小写转换
- 编码处理
- 正则表达式工具
- 字符串清理

#### 数据结构
- 列表操作
- 字典操作
- 集合操作
- 有序集合

#### 日期时间
- 日期解析
- 时区处理
- 时间戳转换
- 日期范围

#### 编码解码
- Base64 编解码
- Unicode 处理
- 字符集转换
- URL 编解码

## 主要功能模块

### 进度报告 (`progress.py`)
```python
class ProgressLogger:
    """进度记录器"""

    def start(self, total_bytes):
        """开始进度跟踪"""

    def update(self, downloaded_bytes, speed, eta):
        """更新进度"""

    def finish(self):
        """完成进度跟踪"""

class MultilinePrinter:
    """多行终端输出"""

    def print(self, line):
        """打印一行"""
```

### 数据遍历 (`traversal.py`)
```python
def traverse_obj(obj, *path, **kwargs):
    """安全遍历嵌套数据结构

    Args:
        obj: 要遍历的对象
        *path: 路径规范，支持:
            - 字典键
            - 列表索引
            - 切片对象
            - 函数调用
        **kwargs:
            - get_all: 获取所有匹配
            - expected_type: 期望类型
            - default: 默认值

    Returns:
        匹配的值或默认值
    """
```

### JS 运行时 (`_jsruntime.py`)
```python
class NodeJsRuntime:
    """Node.js 运行时"""

    def evaluate(self, code, *args):
        """执行 JavaScript 代码"""

class QuickJsRuntime:
    """QuickJS 运行时"""

    def evaluate(self, code, *args):
        """执行 JavaScript 代码"""
```

### 网络工具 (`networking.py`)
```python
def determine_protocol(info_dict):
    """确定媒体协议类型"""

def handle_youtubedl_headers(headers):
    """处理 YouTube-DL 风格的请求头"""

def make_HTTPS_handler(params, **kwargs):
    """创建 HTTPS 处理器"""
```

## 数据模型

### 媒体扩展名映射
```python
MEDIA_EXTENSIONS = {
    'audio': {
        'mp3': 'mp3',
        'm4a': 'm4a',
        'aac': 'aac',
        'opus': 'opus',
        'flac': 'flac',
        'wav': 'wav',
    },
    'video': {
        'mp4': 'mp4',
        'webm': 'webm',
        'mkv': 'mkv',
        'avi': 'avi',
        'mov': 'mov',
    }
}
```

### 输出模板类型
```python
OUTTMPL_TYPES = {
    'id': '视频 ID',
    'title': '视频标题',
    'ext': '文件扩展名',
    'uploader': '上传者',
    'upload_date': '上传日期',
    'duration': '时长',
    'view_count': '观看次数',
    'like_count': '点赞数',
}
```

### 格式字段
```python
def format_field(obj, field, template, default=None):
    """格式化单个字段

    Args:
        obj: 数据对象
        field: 字段名（支持嵌套）
        template: 格式模板
        default: 默认值

    Returns:
        格式化后的字符串
    """
```

## 测试与质量

### 测试覆盖
- **单元测试**: 每个工具函数的测试
- **边界测试**: 极端值和异常情况
- **性能测试**: 大数据处理性能

### 相关测试文件
- `test/test_utils.py` - 工具函数测试
- `test/test_traversal.py` - 数据遍历测试
- `test/test_jsinterp.py` - JavaScript 解释器测试

## 常见问题 (FAQ)

### Q: 如何清理文件名中的特殊字符？
A: 使用 `sanitize_filename()` 函数。

### Q: 如何格式化字节大小？
A: 使用 `format_bytes()` 函数，会自动选择合适的单位。

### Q: 如何安全遍历嵌套数据？
A: 使用 `traverse_obj()` 函数，支持复杂的路径规范。

### Q: 如何解析时长字符串？
A: 使用 `parse_duration()` 函数，支持多种格式。

## 相关文件清单

### 核心文件
- `__init__.py` - 公共接口导出
- `_utils.py` - 核心工具函数（6000+ 行）
- `progress.py` - 进度报告和终端显示

### 数据处理
- `traversal.py` - 数据遍历工具
- `_deprecated.py` - 已弃用函数
- `_legacy.py` - 遗留支持

### 运行时支持
- `_jsruntime.py` - JavaScript 运行时
- `networking.py` - 网络工具

### 工具类
- `lazy_list.py` - 懒加载列表
- `outtmpl.py` - 输出模板处理

## 使用示例

### 文件名清理
```python
from yt_dlp.utils import sanitize_filename

filename = "Video: Special/Characters? <Test>.mp4"
clean = sanitize_filename(filename)
# 结果: "Video Special Characters Test.mp4"
```

### 格式化大小
```python
from yt_dlp.utils import format_bytes

size = 1234567890
formatted = format_bytes(size)
# 结果: "1.15GiB"
```

### 数据遍历
```python
from yt_dlp.utils import traverse_obj

data = {
    'videos': [
        {'title': 'Video 1', 'formats': [{'url': 'http://...'}]},
        {'title': 'Video 2', 'formats': []},
    ]
}

# 获取第一个视频的第一个格式 URL
url = traverse_obj(data, ('videos', 0, 'formats', 0, 'url'))
```

### 时间格式化
```python
from yt_dlp.utils import format_seconds, parse_duration

# 格式化秒数
formatted = format_seconds(3661)  # "1:01:01"

# 解析时长字符串
seconds = parse_duration("1:23:45")  # 5025
```

## 扩展接口

### 自定义工具函数
```python
def custom_formatter(value):
    """自定义格式化函数"""
    if isinstance(value, int):
        return f"{value:,}"
    return str(value)
```

### 注册自定义函数
```python
# 在 _utils.py 中添加新函数
# 在 __init__.py 中导出
```

## 性能优化

### 缓存机制
- 正则表达式缓存
- 编译后的函数缓存
- 重复计算缓存

### 内存优化
- 生成器代替列表
- 及时释放大对象
- 流式处理数据

## 变更记录 (Changelog)

### 2026-01-12
- 创建工具库模块文档
- 分析所有工具函数分类
- 文档覆盖工具库 7 个核心文件
- 建立工具函数使用指南

### 功能特点
- 提供 6000+ 行工具函数代码
- 支持字符串、文件、网络、数据处理
- 提供进度报告和用户界面
- 支持 JavaScript 运行时集成

### 未来计划
- 详细分析核心工具函数实现
- 补充性能优化建议
- 添加自定义工具函数开发指南
