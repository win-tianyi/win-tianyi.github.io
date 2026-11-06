# Zola 文档站 · tianyi.win

`main` 分支就是线上站点：一个 **Zola 静态站点**（内容来自 [getzola/zola](https://github.com/getzola/zola) 的 `docs/` 目录），
由 Cloudflare Pages 构建并托管在 <https://tianyi.win/>。

> 历史：2026-11 之前本仓库 `main` 是 hugoplate 的 Hugo 站点。该站点已下线，`main` 已被本 Zola 站点覆盖（分支历史是重写的）。

## 目录

```
config.toml      # 站点配置，base_url 必须与最终域名一致
content/         # documentation/ 与 themes/ 两棵内容树
templates/       # index / documentation / page / theme 模板
sass/            # 样式源文件
static/          # site.css 生成源、图片、_headers、CNAME
wrangler.toml    # Cloudflare Pages 读到的构建元数据（pages_build_output_dir）
public/          # 构建产物，已 gitignore
```

## 本地预览 / 构建

```bash
zola serve            # http://127.0.0.1:1111，热重载
zola build            # 产物输出到 ./public
```

Zola 版本必须是 **0.23.6**：0.23 移除了 shortcode、改用 Tera component，且 `config.toml` 里的 `skip_content_templating` 只有 0.23+ 认识，低版本会直接 TOML 解析失败。

## Cloudflare Pages 构建参数（项目 `win-tianyi-zola`）

| 项 | 值 |
|---|---|
| Production branch | `main` |
| Framework preset | None |
| Build command | `curl -fsSL https://github.com/getzola/zola/releases/download/v0.23.6/zola-v0.23.6-x86_64-unknown-linux-gnu.tar.gz -o /tmp/zola.tar.gz && tar -xzf /tmp/zola.tar.gz -C /tmp && /tmp/zola build` |
| Build output directory | `public` |
| Root directory | 留空 |

构建镜像自带的是 Zola **0.22.1**（asdf 安装，`/opt/buildhome/.asdf/shims/zola`），而且本项目实测 `ZOLA_VERSION` 环境变量不会注入构建环境（日志里 `ENV ZOLA_VERSION=[]` 为空），
所以版本固定在构建命令里显式下载 —— 不依赖镜像预装版本，也不依赖环境变量。

## 换域名只改一行

`config.toml` 第 4 行：

```toml
base_url = "https://tianyi.win/"
```

改完重新构建即可；所有 `get_url()`、`@/` 内部链接、sitemap、search 索引都会跟着变。

## 上游

- 源码：https://github.com/getzola/zola/tree/master/docs
- 许可：本仓内容遵循上游 EUPL-1.2 / MIT，文档版权归 Vincent Prouillet 与贡献者
