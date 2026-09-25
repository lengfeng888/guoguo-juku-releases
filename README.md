# 果果剧库 v5 公开发布库

本仓库只提供编译后的程序包和说明文档，不包含源码、账号数据、日志、缓存、签名密钥或视频文件。

> 请仅使用你有权访问、播放和下载的内容，并遵守来源站点的服务条款、版权要求及所在地区法律。

> 2026-09-25 更新：登录状态调整为 365 天滑动续期，修复登录后路径回跳；“更新剧库”现在一次点击自动续载到源站目录结束或任务超时，中断后从检查点继续。

## 下载

| 平台 | 架构 | 安装包 |
| --- | --- | --- |
| 全部平台 | 六个平台合集 | `guoguo_v5_all_platforms.zip` |
| macOS | Apple Silicon | `guoguo_v5_darwin_arm64.zip` |
| macOS | Intel | `guoguo_v5_darwin_amd64.zip` |
| Linux | ARM64 | `guoguo_v5_linux_arm64.zip` |
| Linux | AMD64 | `guoguo_v5_linux_amd64.zip` |
| Windows | ARM64 | `guoguo_v5_windows_arm64.zip` |
| Windows | AMD64 | `guoguo_v5_windows_amd64.zip` |

每个 ZIP 内只包含对应平台的可执行程序。首次启动会在程序目录创建 `data/`，下载内容默认保存到 `短剧下载/`。

## 使用说明

完整说明见 [完整说明.md](完整说明.md)，包括：

- 快速启动和管理员账号
- Web UI、剧库、追剧、历史和下载
- 在线播放、续播、画质和移动端操作
- TVBox 配置、最高画质和播放缓存
- fake-IP DNS 下的海报兼容
- Docker、反向代理和 Emby
- 数据备份、安全注意事项和故障排查
- 六平台编译和发布流程

## TVBox 接口和配置地址

TVBox 不同版本的设置项名称可能不同，按下面的用途填写：

| TVBox 设置项 | 使用地址 | 用途 |
| --- | --- | --- |
| 配置地址 / 订阅地址 | `http://运行程序电脑的局域网IP:8999/tvbox.json` | 推荐，直接返回完整站点配置 |
| 点播接口 / 接口 | `http://运行程序电脑的局域网IP:8999/api/tvbox` | TVBox 只允许填接口时使用 |
| 兼容接口 | `http://运行程序电脑的局域网IP:8999/api.php/provide/vod` | 苹果 CMS 风格兼容地址 |

当前 Mac 的局域网地址实测为 `192.168.10.7`，所以现在可以直接填写：

```text
配置地址：http://192.168.10.7:8999/tvbox.json
点播接口：http://192.168.10.7:8999/api/tvbox
兼容接口：http://192.168.10.7:8999/api.php/provide/vod
```

如果 IP 发生变化，在 Mac 终端执行：

```bash
ipconfig getifaddr en0
```

如果返回为空，再试 `en1` 或 `en2`。

公网或反向代理示例：

```text
https://tv.example.com/tvbox.json
https://example.com/juku/tvbox.json
https://example.com/juku/api/tvbox
```

电视或手机上的 TVBox 不要填写 `127.0.0.1`，因为那会指向电视自己。填好后保存配置并重新加载首页，TVBox 会显示站点“果果剧库”。

TVBox 播放请求会自动携带 `client=tvbox&quality=highest`。有 `1080p` 和 `720p` 时选择 `1080p`，只有 `720p` 时回退到 `720p`。

配置文件会同时下发程序自己的 `danmaku` 弹幕源。TVBox 按当前剧名和集数读取对应的红果本集评论，不再回退到客户端内置库，避免同名影视或优酷条目只返回一条不相关弹幕。

## macOS 快速启动

Apple Silicon：

```bash
unzip guoguo_v5_darwin_arm64.zip
chmod +x juku_darwin_arm64
./juku_darwin_arm64 -open=false
```

Intel：

```bash
unzip guoguo_v5_darwin_amd64.zip
chmod +x juku_darwin_amd64
./juku_darwin_amd64 -open=false
```

## Linux 快速启动

```bash
unzip guoguo_v5_linux_amd64.zip
chmod +x juku_linux_amd64
./juku_linux_amd64 -open=false
```

ARM64 将文件名替换为 `juku_linux_arm64`。

## Windows 快速启动

解压后运行：

```text
juku_windows_amd64.exe -open=false
```

ARM64 将文件名替换为 `juku_windows_arm64.exe`。

## 默认访问地址

```text
http://127.0.0.1:8999
```

默认监听 `0.0.0.0:8999`。局域网设备访问：

```text
http://运行程序电脑的局域网IP:8999
```

服务器或公网部署前，请在完整说明中阅读登录、反向代理和安全配置。

## v5 主要包含

- macOS、Linux、Windows 的 ARM64 / AMD64 六平台包
- 登录状态 365 天滑动续期，登录后支持安全的站内路径回跳
- 更新剧库一次点击自动续载，超时或中断后从检查点继续
- TVBox 显式使用 `quality=highest`
- 有 `1080p` 和 `720p` 时选择 `1080p`
- HLS 多分辨率最高档选择
- TVBox 与普通网页播放会话画质隔离
- fake-IP DNS 海报兼容
- `::ffff:0:xxxx:xxxx` 地址规范化
- 保留图片代理的内网和 SSRF 防护
- 全量 Go 回归测试和 SHA-256 校验

## 校验安装包

下载后可在当前目录执行：

```bash
shasum -a 256 -c SHA256SUMS.txt
```

全部显示 `OK` 表示文件完整。校验文件由本仓库中的 ZIP 文件生成。

## 重要提醒

- 不要在公开网络直接暴露未配置登录和 HTTPS 的 `8999` 端口。
- 不要分享自己的 `data/`、日志、Cookie、Emby API Key 或下载视频。
- 旧版程序必须替换并重启后，TVBox 和图片修复才会生效。
- TVBox 更新程序后建议重新加载配置并清理一次播放缓存。
- 源站只提供 720p 时不会生成真正的 1080p。
- 第三方站源可能随时变化接口、分页或媒体地址。
