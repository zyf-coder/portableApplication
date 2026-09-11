# 便捷工具 portableApplication

跨端实用工具：**无水印提取** + **证件照制作**。  
Web / PWA 单文件可直接用；Android WebView 壳可通过 Actions 打出 APK。

**当前版本：v1.1.0**

仓库：https://github.com/zyf-coder/portableApplication

## 功能

### 无水印提取
- 粘贴抖音 / 快手 / 小红书 / 微博 / B站 等分享链接（支持多行批量）
- 解析结果、复制链接、批量下载；视频可连带封面
- 失败时展开「手动直链下载」

### 证件照制作
- 一寸 25×35mm（295×413px）、二寸 35×49mm（413×579px），300dpi
- 四步向导：选尺寸 → 传照片构图 → 换背景 → 预览导出
- 自动抠图换底（白/红/蓝/青/自定义），导出 JPG/PNG
- **全程本地处理，照片不上传**

## 快速运行（Web / PWA）

```bash
# 任选一种
python -m http.server 8080
npx serve .
```

浏览器打开 `http://localhost:8080`。手机同一 Wi-Fi 访问电脑 IP，浏览器菜单选「添加到主屏幕」即可当 App 用。

## Android APK

工程在 `android/`（WebView 加载 `assets/www/`）。

### 方式 A：GitHub Actions（推荐）
1. 推送到本仓库后，打开 **Actions → Android APK → Run workflow**
2. 完成后在该次运行的 **Artifacts** 下载 `bianjie-tools-apk`
3. 打 `v*` 标签时会自动创建 Release 并挂上 APK

```bash
git tag v1.1.0
git push origin v1.1.0
```

### 方式 B：本机打包（需 Android SDK + JDK 17）
```powershell
mkdir android\app\src\main\assets\www -Force
Copy-Item index.html,manifest.webmanifest,sw.js,icon-192.png,icon-512.png `
  android\app\src\main\assets\www\
cd android
# 首次：gradle wrapper --gradle-version 8.5
.\gradlew assembleDebug
# 产物：android\app\build\outputs\apk\debug\app-debug.apk
```

> 产物为 **debug** 签名包，仅供自用安装；上架需自行配置 release 签名。

## 目录

| 路径 | 说明 |
|------|------|
| `index.html` | 主应用 |
| `manifest.webmanifest` / `sw.js` | PWA |
| `android/` | WebView Android 工程 |
| `.github/workflows/android-apk.yml` | 自动打 APK |
| `CHANGELOG.md` | 版本迭代 |

逻辑模块（脚本内聚，便于迁到 Flutter/FastAPI）：`link_parser`、`video_downloader`、`id_photo`、`background_removal`。

## 接入真实解析后端

社交链接当前为演示模式。对接 FastAPI：

```http
POST /parse
{ "url": "https://v.douyin.com/..." }
→ { "media_url": "...", "cover_url": "...", "type": "video"|"image" }
```

在 `index.html` 的 `parseOne` 中改为请求该接口即可；直链下载已可用。

## 合规

- 仅下载自己拥有版权或已获授权的内容
- 不得去除他人作品水印后二次传播
- 请遵守各平台服务条款

## 版本

见 [CHANGELOG.md](./CHANGELOG.md)。
