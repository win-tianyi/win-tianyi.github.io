# Zola 文档站镜像 · tianyi.win

本仓库分支 `zola` 存放的是一个 **Zola 静态站点**（由 [getzola/zola](https://github.com/getzola/zola) 的 `docs/` 目录构建而来），
不是引擎源码。`main` 分支上的 Hugo 站点（robot.tianyi.win）保持不动。

## 目录

```
config.toml      # 站点配置，base_url 必须与最终域名一致
content/         # documentation/ 与 themes/ 两棵内容树
templates/       # index / documentation / page / theme 模板
sass/            # 样式源文件
static/          # site.css 生成源、图片、_headers、CNAME
wrangler.toml    # Cloudflare Pages 直接上传用（pages_build_output_dir）
public/          # 构建产物，已 gitignore
```

## 本地预览 / 构建

```bash
# Windows 下把 zola.exe 放到 PATH，或直接写完整路径
zola serve            # http://127.0.0.1:1111，热重载
zola build            # 产物输出到 ./public
```

Zola 版本需要 **0.23.6**（0.23 移除了 shortcode，改用 Tera component，低版本会构建失败）。

## Cloudflare Pages 构建参数

| 项 | 值 |
|---|---|
| Framework preset | None |
| Build command | `zola build` |
| Build output directory | `public` |
| Root directory | `/` |
| 环境变量 | `ZOLA_VERSION=0.23.6` |

v3 构建镜像自带 Zola（`ZOLA_VERSION` 可指定任意版本），无需自己下载二进制。

## 换域名只改一行

`config.toml` 第 4 行：

```toml
base_url = "https://tianyi.win/"
```

改完重新构建即可；所有 `get_url()`、`@/` 内部链接、sitemap、search 索引都会跟着变。

## 上游

- 源码：https://github.com/getzola/zola/tree/master/docs
- 许可：本仓内容遵循上游 EUPL-1.2 / MIT，文档版权归 Vincent Prouillet 与贡献者
