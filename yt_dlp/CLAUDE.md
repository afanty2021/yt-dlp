[根目录](../../CLAUDE.md) > **yt_dlp (核心模块)**

# yt_dlp 核心模块

## 模块职责

yt_dlp 核心模块是整个项目的中央控制单元，负责：
- 主程序入口点和命令行界面
- 全局配置管理和选项解析
- 核心下载流程协调
- 插件系统集成
- 用户界面和进度显示

## 入口与启动

### 主入口文件
- **`__init__.py`**: 包初始化，版本检查，导入核心功能
- **`__main__.py`**: Python 模块执行入口 (`python -m yt_dlp`)
- **`YoutubeDL.py`**: 核心类，主要的下载逻辑实现

### 启动流程
```python
# 命令行入口
yt-dlp URL  # 调用 __main__.py -> main()

# 程序化入口
from yt_dlp import YoutubeDL
with YoutubeDL({'outtmpl': '%(title)s.%(ext)s'}) as ydl:
    ydl.download(['URL'])
```

## 对外接口

### 核心 API
- **`YoutubeDL`**: 主要的下载器类，提供完整的下载功能
- **`main()`**: 命令行入口函数
- **`parseOpts()`**: 命令行选项解析
- **`list_extractor_classes()`**: 获取支持的提取器列表

### 关键方法
- `YoutubeDL.download()`: 执行下载
- `YoutubeDL.extract_info()`: 提取媒体信息
- `YoutubeDL.prepare_filename()`: 准备输出文件名

## 关键依赖与配置

### 主要模块依赖
```python
# 内部依赖
from .extractor import gen_extractor_classes
from .downloader import get_suitable_downloader
from .postprocessor import get_postprocessor
from .networking import Request
from .compat import urllib
from .cookies import load_cookies
from .cache import Cache
```

### 配置管理
- **`options.py`**: 命令行选项定义和默认值
- **`globals.py`**: 全局状态和插件目录管理
- **配置文件支持**:
  - `yt-dlp.conf` / `yt-dlp.txt`
  - 用户配置目录: `~/.config/yt-dlp/`
  - 系统配置目录

## 核心文件说明

### `YoutubeDL.py`
- **行数**: 约 6000+ 行
- **职责**: 主下载器类实现
- **关键类**: `YoutubeDL`
- **主要功能**:
  - 选项管理和验证
  - 下载流程控制
  - 格式选择和排序
  - 后处理链管理
  - 错误处理和重试

### `__init__.py`
- **职责**: 包初始化，公共 API 导出
- **关键函数**:
  - `main()`: CLI 入口点
  - `_hide_login_info()`: 敏感信息过滤
  - 版本兼容性检查

### `options.py`
- **职责**: 命令行选项定义
- **内容**: 1000+ 行选项定义
- **关键函数**: `parseOpts()`

## 关键常量和配置

### 全局常量
- `__version__`: 版本信息（从 `version.py` 导入）
- `__license__`: 许可证信息
- `IN_CLI`: 是否在命令行模式中运行
- `LAZY_EXTRACTORS`: 是否使用懒加载提取器

### 默认配置
```python
DEFAULT_OUTTMPL = '%(title)s [%(id)s].%(ext)s'
POSTPROCESS_WHEN = 'after_filter'
NUMBER_RE = re.compile(r'(\\d+)')
```

## 测试与质量

### 相关测试文件
- `test/test_YoutubeDL.py`: 核心 YoutubeDL 类测试
- `test/test_download.py`: 下载功能集成测试
- `test/test_config.py`: 配置解析测试
- `test/test_execution.py`: 命令行执行测试

### 质量保证
- **类型提示**: 核心模块部分使用类型提示
- **错误处理**: 完善的异常处理机制
- **日志记录**: 多级别日志支持

## 常见问题 (FAQ)

### Q: 如何自定义下载选项？
A: 创建 `YoutubeDL` 实例时传入选项字典，或使用配置文件。

### Q: 程序化使用 vs 命令行使用？
A: 程序化使用提供更多控制，命令行使用更简单直接。

### Q: 如何调试下载问题？
A: 使用 `--verbose` 或 `--print-traffic` 选项获取详细信息。

## 相关文件清单

### 核心文件
- `__init__.py` - 包初始化和公共 API
- `__main__.py` - 模块执行入口
- `YoutubeDL.py` - 主下载器类（核心）
- `options.py` - 选项解析
- `globals.py` - 全局状态
- `version.py` - 版本信息

### 辅助模块
- `aes.py` - AES 加密解密
- `cache.py` - 缓存实现
- `cookies.py` - Cookie 处理
- `jsinterp.py` - JavaScript 解释器
- `minicurses.py` - 终端进度显示
- `plugins.py` - 插件系统
- `socks.py` - SOCKS 代理支持
- `update.py` - 自动更新
- `webvtt.py` - WebVTT 字幕处理

### 配置文件
- `yt-dlp.conf` / `yt-dlp.txt` - 主配置文件
- `~/.config/yt-dlp/config` - 用户配置
- `yt-dlp.plugin` - 插件配置

## 变更记录 (Changelog)

### 2025-11-19
- 创建核心模块文档
- 分析主要入口文件和核心类
- 文档覆盖核心模块 15 个关键文件