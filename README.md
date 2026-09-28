# 深圳市中溢海国际货运代理有限公司 — 静态网站

由 WordPress 备份转换生成的双语静态 HTML 网站（中文默认 + 英文 `/en/`）。

## 目录结构

```
szchinasea.com/
├── index.html                  # 中文首页
├── en/index.html               # 英文首页
├── 海运服务/ 空运服务/ ...       # 中文服务/公司/新闻页面（每页一个目录 + index.html）
├── en/海运服务/ ...             # 对应英文页面
├── 伟大的祖国，我的母亲-生日快乐！/  # 公司新闻文章（中/英）
├── category/公司新闻/           # 新闻分类归档页（中/英）
├── assets/fonts/               # 本地化的 Google 字体（Merriweather / Roboto / Roboto Slab）
├── wp-content/                 # 主题/插件样式、脚本、上传图片（全部本地化）
├── wp-includes/                # WordPress 核心静态资源（jQuery 等）
└── llms.txt                    # 站点页面清单（已改为干净 URL）
```

## 打开方式

- 直接双击 `index.html` 即可离线浏览（所有样式、图片、字体、脚本均已本地化，无需联网）
- 或启动本地服务器浏览：`python3 -m http.server 8092 --directory szchinasea.com`

## 双语切换

- 中文站：`/`；英文站：`/en/`
- 每个页面底部（及页头语言切换器）可切换 Chinese / English

## 转换说明

- 来源：WordPress（Blocksy 主题 + Elementor 页面构建 + TranslatePress 双语插件）
- 方法：本地搭建 WordPress + MySQL 还原备份 → 全站镜像抓取渲染后的 HTML → 清理演示数据 → 链接本地化（`?p=` 旧式链接修正为干净路径）→ Google Fonts 本地化 → 全站断链校验
- 页面数：42 个 HTML 页面（中英各 21 个，含新闻分类页与 1 篇真实新闻）

## 已知说明

1. 「公司新闻 / 行业新闻」页在备份中即未渲染文章列表（源站动态组件被跳过），静态版保持与源站一致，仅显示标题；真实新闻《伟大的祖国，我的母亲-生日快乐！》页面完整保留。
2. 页头「货物跟踪 / 海关编码查询」等为外链工具（中国船舶网、海关编码查询站等），保持原外链。
3. 英文标题（`<title>`）为备份中 TranslatePress 的翻译状态，部分未翻译的仍显示中文，与源站一致。
4. 静态版不含后端动态功能（如评论提交、登录弹窗），仅保留页面展示效果。

## 菜单下拉修复记录（2026-09-28）

**现象**：页面导航菜单（核心业务 / 关于我们 / 新闻资讯 等）的下拉子菜单无法弹出。

**根因**：Blocksy 与 Elementor 采用 webpack 分包加载 JS。主题运行时通过页面内联配置 `ct_localizations.public_url` 设置资源加载路径，该路径在抓取时残留为源站地址 `http://localhost:8090/...`，导致浏览器加载动态 JS 分块（chunk）时连接失败 → 主题初始化中断 → 下拉菜单事件未绑定。

**修复**：
1. 从源站补齐全部动态 JS 分块文件（blocksy 142 个、elementor 615 个、blocksy-companion 等）；
2. 将 42 个页面内联配置中的 `http://localhost:8090` 统一改写为按页面深度计算的相对路径（HTTP 与 file:// 双击打开均可用）。

**验证**：本地 HTTP、file:// 双击、公网链接三种访问方式下，中英文页面下拉菜单 hover 均正常展开（`.ct-active` 生效），控制台无 JS 错误。
