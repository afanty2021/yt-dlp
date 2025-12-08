[根目录](../../CLAUDE.md) > [yt_dlp](../) > **extractor (提取器模块)**

# extractor 提取器模块

## 模块职责

extractor 模块是 yt-dlp 的核心功能模块，负责：
- 为 1000+ 不同网站实现媒体内容提取逻辑
- 提供统一的提取器接口和基础类
- 支持视频、音频、播放列表、频道等内容提取
- 处理网站认证、区域限制、加密等技术挑战

## 入口与启动

### 主要入口
- **`__init__.py`**: 提取器注册和管理接口
- **`_extractors.py`**: 所有提取器的导入文件
- **`common.py`**: 基础提取器类，提供通用功能

### 提取器加载流程
```python
# 延迟加载机制
def import_extractors():
    from . import extractors  # 导入所有提取器

# 生成提取器类列表
def gen_extractor_classes():
    import_extractors()
    return list(_extractors_context.value.values())

# 实例化提取器
def gen_extractors():
    return [klass() for klass in gen_extractor_classes()]
```

## 对外接口

### 核心类和方法
- **`InfoExtractor`**: 基础提取器抽象类
- **`SearchInfoExtractor`**: 搜索提取器基类
- **`YoutubeTabIE`**: YouTube 标签页提取器
- **`GenericIE`**: 通用提取器，处理未知网站

### 主要方法
```python
class InfoExtractor:
    def extract(self, url):  # 提取媒体信息
    def _real_extract(self, url):  # 实际提取逻辑
    def _match_valid_url(self, url):  # URL 匹配验证
    def _download_webpage(self, url):  # 下载网页内容
    def _json_extract(self, json_string):  # JSON 数据提取
    def _html_search_regex(self, pattern, html):  # HTML 正则搜索
    def _parse_jwplayer_data(self, data):  # JWPlayer 数据解析
```

## 关键依赖与配置

### 内部依赖
```python
from ..compat import urllib
from ..utils import (
    ExtractorError,
    unescapeHTML,
    js_to_json,
    parse_duration,
    url_or_none,
)
from ..aes import aes_decrypt
from ..jsinterp import JSInterpreter
```

### 提取器分类

#### 视频平台类
- **`youtube.py`**: YouTube 提取器（核心）
- **`bilibili.py`**: 哔哩哔哩提取器
- **`vimeo.py`**: Vimeo 提取器
- **`dailymotion.py`**: Dailymotion 提取器

#### 音频平台类
- **`soundcloud.py`**: SoundCloud 提取器
- `spotify.py`: Spotify 提取器
- `bandcamp.py`: Bandcamp 提取器

#### 直播平台类
- **`twitch.py`**: Twitch 提取器
- `youtube.py`: YouTube 直播提取
- `douyin.py`: 抖音直播提取器

#### 新闻媒体类
- **`bbc.py`**: BBC 提取器
- `cnn.py`: CNN 提取器
- `reuters.py`: 路透社提取器

#### 社交媒体类
- **`twitter.py`**: X/Twitter 提取器
- `instagram.py`: Instagram 提取器
- `facebook.py`: Facebook 提取器
- `tiktok.py`: TikTok 提取器

### 提取器命名规范
- 以网站名称命名: `{website}.py`
- 类名以 `IE` 结尾: `{Website}IE`
- 搜索提取器: `{Website}SearchIE`

## 数据模型

### 基本数据结构
```python
{
    'id': str,           # 视频/音频 ID
    'title': str,        # 标题
    'description': str,  # 描述
    'uploader': str,     # 上传者
    'upload_date': str,  # 上传日期
    'duration': int,     # 时长（秒）
    'view_count': int,   # 观看次数
    'like_count': int,   # 点赞数
    'formats': [],       # 可用格式列表
    'subtitles': {},     # 字幕信息
    'thumbnails': [],    # 缩略图列表
    'url': str,          # 直接下载链接
    'ext': str,          # 文件扩展名
    'webpage_url': str,  # 网页链接
}
```

### 格式选择模型
```python
{
    'format_id': str,    # 格式 ID
    'url': str,          # 下载链接
    'ext': str,          # 扩展名
    'resolution': str,   # 分辨率
    'fps': float,        # 帧率
    'vcodec': str,       # 视频编码
    'acodec': str,       # 音频编码
    'filesize': int,     # 文件大小
    'tbr': float,        # 总比特率
    'vbr': float,        # 视频比特率
    'abr': float,        # 音频比特率
}
```

## 测试与质量

### 测试策略
- **单元测试**: 每个提取器的核心逻辑测试
- **集成测试**: 真实网站提取测试
- **回归测试**: 确保更新不破坏现有功能
- **性能测试**: 提取速度和内存使用测试

### 测试文件结构
```
test/
├── test_youtube_misc.py      # YouTube 相关测试
├── test_download.py          # 下载功能测试
├── test_all_urls.py          # URL 匹配测试
├── test_InfoExtractor.py     # 基础提取器测试
└── testdata/                 # 测试数据
    └── *.json               # 模拟响应数据
```

### 质量保证
- **代码审查**: 新提取器需要代码审查
- **测试覆盖率**: 必须有对应的测试用例
- **文档**: 新提取器需要添加到支持网站列表
- **兼容性**: 确保与现有代码兼容

## 常见问题 (FAQ)

### Q: 如何添加新的提取器？
A: 继承 `InfoExtractor` 类，实现 `_real_extract()` 方法，在 `_extractors.py` 中注册。

### Q: 提取器如何处理认证？
A: 使用 `_login()` 方法处理登录，支持 Cookie、OAuth、用户名密码等方式。

### Q: 如何处理区域限制？
A: 使用 `_geo_bypass()` 方法进行地理限制绕过，支持代理和 VPN。

### Q: 懒加载提取器是什么？
A: 为提高启动速度，提取器采用懒加载机制，只有需要时才导入。

## 相关文件清单

### 核心文件
- `__init__.py` - 提取器管理和注册
- `_extractors.py` - 所有提取器导入
- `common.py` - 基础提取器类
- `generic.py` - 通用提取器

### 主要提取器（部分）
- `youtube.py` - YouTube（最大最复杂的提取器）
- `bilibili.py` - 哔哩哔哩
- `soundcloud.py` - SoundCloud
- `vimeo.py` - Vimeo
- `twitch.py` - Twitch
- `facebook.py` - Facebook
- `instagram.py` - Instagram
- `twitter.py` - X/Twitter
- `tiktok.py` - TikTok
- `nhk.py` - NHK（日本广播协会）
- `patreon.py` - Patreon
- `fc2.py` - FC2
- `tubitv.py` - TubiTV

### 新增提取器（2025.12.08）
- `alibaba.py` - 阿里巴巴商品详情页视频提取器

### 新增提取器（2025.11.12）
- `agalega.py` - Agalega 平台
- `bitmovin.py` - Bitmovin 视频平台
- `frontro.py` - Frontro 平台
- `netapp.py` - NetApp 视频服务
- `nowcanal.py` - Now Canal 视频平台
- `yfanefa.py` - YFA NEFA 平台

### 辅助模块
- `adobepass.py` - Adobe Pass 认证
- `anvato.py` - Anvato 平台
- `brightcove.py` - Brightcove 播放器
- `jwplatform.py` - JWPlatform
- `youtube.py` - YouTube 复杂逻辑

### 工具和测试
- `test/test_InfoExtractor.py` - 基础提取器测试
- `test/test_youtube_*.py` - YouTube 相关测试
- `devscripts/make_lazy_extractors.py` - 懒加载生成器

## 扩展接口

### 插件系统
```python
# 自定义提取器插件
class CustomIE(InfoExtractor):
    IE_NAME = 'custom'
    _VALID_URL = r'https?://custom\.com/watch/(?P<id>[^/]+)'

    def _real_extract(self, url):
        # 提取逻辑实现
        pass
```

### 懒加载优化
```python
# 提取器懒加载生成
python -m devscripts.make_lazy_extractors

# 结果: lazy_extractors.py
```

## 变更记录 (Changelog)

### 2025-12-08
- **新增提取器**:
  - 阿里巴巴（alibaba.py）：支持阿里巴巴商品详情页视频提取
- **核心提取器改进**:
  - YouTube：更新 EJS 到 0.3.2 版本，改进 JS 运行时错误提示
  - Archive.org：大幅改进，支持更多媒体类型和元数据提取
  - Loom：重构提取逻辑，提升稳定性
  - SportDeutschland：优化直播和视频点播提取
  - XHamster：修复视频解析问题，改进格式检测
- **辅助功能优化**:
  - Cookie 处理增强兼容性，支持更多网站
  - FFmpeg 后处理器优化集成
  - JS 运行时错误提示更加友好

### 2025-12-04
- **新增提取器支持**:
  - 6 个新平台提取器：agalega、bitmovin、frontro、netapp、nowcanal、yfanefa
  - 提取器总数突破 1000+
- **核心提取器改进**:
  - YouTube：新增 `use_ad_playback_context` 参数，改进广告处理
  - NHK：全面重构，提升稳定性和性能
  - FC2：改进直播流处理，正确显示离线状态
  - S4C：修复地理限制内容访问
  - Patreon：修复提取器问题
  - TubiTV：修复系列内容提取

### 2025-11-19
- 创建提取器模块文档
- 分析核心提取器架构和接口
- 识别主要提取器类别和文件
- 文档覆盖提取器模块 1000+ 文件概览

### 未来计划
- 详细分析核心提取器（YouTube, Bilibili 等）
- 补充提取器开发指南
- 添加提取器调试技巧