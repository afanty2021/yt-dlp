[根目录](../../CLAUDE.md) > [yt_dlp](../) > **downloader (下载器模块)**

# downloader 下载器模块

## 模块职责

downloader 模块负责实际的网络下载功能，支持多种媒体传输协议：
- HTTP/HTTPS 直链下载
- HLS (HTTP Live Streaming) 协议支持
- DASH (Dynamic Adaptive Streaming over HTTP) 协议支持
- RTMP/RTSP 流媒体协议
- 分片下载和断点续传
- 外部下载器集成

## 入口与启动

### 主要入口
- **`__init__.py`**: 下载器注册和获取接口
- **`common.py`**: 基础下载器类，提供通用功能
- **`external.py`**: 外部下载器集成接口

### 下载器选择逻辑
```python
def get_suitable_downloader(info_dict, params={}):
    """根据媒体格式和选项选择最合适的下载器"""
    if info_dict.get('protocol') == 'm3u8_native':
        return HlsFD
    elif info_dict.get('protocol') == 'dash':
        return DashFD
    elif params.get('external_downloader'):
        return get_external_downloader(params['external_downloader'])
    else:
        return HttpFD
```

## 对外接口

### 核心基类
```python
class FileDownloader:
    """所有下载器的基类"""

    def download(self, filename, info_dict):
        """执行下载的主要接口"""

    def real_download(self, filename, info_dict):
        """子类需要实现的实际下载逻辑"""
        raise NotImplementedError('Subclasses must implement this method')

    def add_progress_hook(self, hook):
        """添加进度回调函数"""

    def report_progress(self, progress):
        """进度报告回调"""
```

### 主要下载器实现

#### HttpFD - HTTP 下载器
- **文件**: `http.py`
- **功能**: 基础 HTTP/HTTPS 下载
- **特性**: 断点续传、速度限制、重试机制

#### HlsFD - HLS 下载器
- **文件**: `hls.py`
- **功能**: HLS 流媒体下载
- **特性**: m3u8 解析、分片下载、实时流支持

#### DashFD - DASH 下载器
- **文件**: `dash.py`
- **功能**: DASH 自适应流下载
- **特性**: 多轨道合并、音视频分离下载

#### FragmentFD - 分片下载器
- **文件**: `fragment.py`
- **功能**: 通用分片下载基类
- **特性**: 并行下载、分片重组

#### ExternalFD - 外部下载器
- **文件**: `external.py`
- **功能**: 外部程序集成
- **支持工具**: aria2, wget, curl, ffmpeg 等

## 关键依赖与配置

### 内部依赖
```python
from ..utils import (
    sanitize_open,
    RetryManager,
    format_bytes,
    timeconvert,
)
from ..networking import Request
from ..minicurses import MultilinePrinter
```

### 下载器配置选项
```python
params = {
    'ratelimit': 1024,              # 下载速度限制 (bytes/s)
    'retries': 10,                  # 重试次数
    'buffersize': 1024,             # 缓冲区大小
    'continuedl': True,             # 断点续传
    'nopart': False,                # 不使用 .part 临时文件
    'updatetime': True,             # 使用 Last-modified 头
    'http_chunk_size': 8192,        # HTTP 分块大小
    'external_downloader_args': {},  # 外部下载器参数
}
```

## 下载协议支持

### HTTP/HTTPS
- **下载器**: `HttpFD`
- **功能**: 直链下载、断点续传、条件请求
- **特性**:
  - Range 请求支持
  - 速度限制
  - 重试机制
  - User-Agent 自定义

### HLS (HTTP Live Streaming)
- **下载器**: `HlsFD`
- **功能**: m3u8 播放列表解析和分片下载
- **特性**:
  - 实时流支持
  - 加密流处理 (AES-128)
  - 多比特率选择
  - 直播录制

### DASH (Dynamic Adaptive Streaming)
- **下载器**: `DashFD`
- **功能**: MPD 清单解析和自适应流下载
- **特性**:
  - 音视频轨道分离
  - 多质量选择
  - 字幕轨道下载
  - 多语言支持

### RTMP/RTSP
- **下载器**: `rtmp.py`
- **功能**: 实时消息协议下载
- **依赖**: rtmpdump 工具
- **特性**:
  - 流媒体录制
  - 实时流处理
  - 认证支持

### WebSocket
- **下载器**: `websocket.py`
- **功能**: WebSocket 协议下载
- **特性**:
  - 实时数据流
  - 双向通信
  - 自定义协议支持

## 数据模型

### 下载信息字典
```python
info_dict = {
    'url': str,              # 下载链接
    'protocol': str,         # 协议类型 ('http', 'hls', 'dash' 等)
    'ext': str,              # 文件扩展名
    'filesize': int,         # 文件大小 (bytes)
    'fragment_base_url': str, # 分片基础 URL
    'fragments': [           # 分片列表
        {
            'url': str,
            'path': str,
            'duration': float
        }
    ],
    'http_headers': dict,    # HTTP 请求头
    'downloader_options': dict # 下载器特定选项
}
```

### 进度状态
```python
progress = {
    'status': str,           # 状态: 'downloading', 'finished', 'error'
    'downloaded_bytes': int, # 已下载字节数
    'total_bytes': int,      # 总字节数
    'speed': float,          # 下载速度 (bytes/s)
    'eta': int,              # 预估剩余时间 (seconds)
    'elapsed': float,        # 已用时间 (seconds)
    'filename': str,         # 文件名
    'tmpfilename': str,      # 临时文件名
}
```

## 测试与质量

### 测试覆盖
- **单元测试**: 每个下载器的核心逻辑测试
- **集成测试**: 真实下载场景测试
- **网络测试**: 各种网络条件下的下载测试
- **性能测试**: 下载速度和资源使用测试

### 相关测试文件
- `test/test_downloader_http.py` - HTTP 下载器测试
- `test/test_downloader_external.py` - 外部下载器测试
- `test/test_networking.py` - 网络层测试
- `test/test_http_proxy.py` - 代理测试

## 常见问题 (FAQ)

### Q: 如何选择合适的下载器？
A: 系统会根据 URL 和协议自动选择，也可通过 `--downloader` 参数手动指定。

### Q: 如何提高下载速度？
A: 使用外部下载器 (如 aria2)，调整分片大小，使用多个连接。

### Q: 如何处理下载失败？
A: 增加重试次数，使用代理，检查网络连接，更新 yt-dlp。

### Q: 支持哪些外部下载器？
A: aria2c, wget, curl, ffmpeg, avconv 等常见工具。

## 相关文件清单

### 核心下载器
- `common.py` - 基础下载器类 (FileDownloader)
- `http.py` - HTTP/HTTPS 下载器 (HttpFD)
- `hls.py` - HLS 下载器 (HlsFD)
- `dash.py` - DASH 下载器 (DashFD)
- `external.py` - 外部下载器接口 (ExternalFD)

### 协议特定下载器
- `fragment.py` - 分片下载器基类 (FragmentFD)
- `rtmp.py` - RTMP 下载器 (RtmpFD)
- `rtsp.py` - RTSP 下载器 (RtspFD)
- `websocket.py` - WebSocket 下载器 (WebSocketFD)
- `mhtml.py` - MHTML 下载器

### 特殊用途下载器
- `f4m.py` - Adobe HDS 下载器 (F4mFD)
- `ism.py` - Microsoft Smooth Streaming 下载器 (IsmFD)
- `niconico.py` - Niconico 特殊下载器
- `fc2.py` - FC2 Video 特殊下载器
- `bunnycdn.py` - BunnyCDN 特殊下载器

### 配置和工具
- `__init__.py` - 下载器注册和获取
- `youtube_live_chat.py` - YouTube 直播聊天下载器

## 扩展接口

### 自定义下载器
```python
class CustomFD(FileDownloader):
    FD_NAME = 'custom'

    def real_download(self, filename, info_dict):
        # 实现自定义下载逻辑
        return True
```

### 下载器注册
```python
# 注册新下载器
_FILE_DOWNLOADER_CLASSES = {
    'http': HttpFD,
    'hls': HlsFD,
    'custom': CustomFD,
}
```

## 变更记录 (Changelog)

### 2025-12-04
- **下载器改进**:
  - 修复外部下载器路径处理问题
  - 改进分片下载性能
  - 增强断点续传可靠性
  - 优化缓冲区管理

### 2025-11-19
- 创建下载器模块文档
- 分析所有下载器类型和协议支持
- 文档覆盖 16 个下载器文件
- 建立下载器选择和配置机制说明

### 未来计划
- 详细分析核心下载器实现
- 补充性能优化建议
- 添加下载器开发指南