# 部署说明（Cloudflare Pages）

## 项目

| 项 | 值 |
| --- | --- |
| Pages 项目 | `win-tianyi-zola` |
| 生产分支 | `main` |
| 预览域名 | `https://win-tianyi-zola.pages.dev` |
| 自定义域 | `tianyi.win` |
| 构建系统版本 | 3 |

## 构建参数

- 构建命令：

```bash
curl -fsSL https://github.com/getzola/zola/releases/download/v0.23.6/zola-v0.23.6-x86_64-unknown-linux-gnu.tar.gz -o /tmp/zola.tar.gz \
  && tar -xzf /tmp/zola.tar.gz -C /tmp \
  && /tmp/zola --version \
  && /tmp/zola build
```

- 构建输出目录：`public`
- 根目录：留空
- 环境变量：`ZOLA_VERSION=0.23.6`（仅作记录，见下方说明，实际不生效）

## 为什么要在构建命令里手动下载 zola

Pages v3 构建镜像自带 asdf 管理的 **zola 0.22.1**（`/opt/buildhome/.asdf/shims/zola`），且构建环境不会把 `ZOLA_VERSION` 注入给 asdf，实测 `ENV ZOLA_VERSION=[]` 为空。用 0.22.1 构建会报：

```
ERROR TOML parse error at line 10, column 1
skip_content_templating = ["themes/**"]
unknown field skip_content_templating
```

Linkita 的 `theme.toml` 要求 `min_version = "0.23.6"`，所以构建命令里显式下载 0.23.6 二进制并直接调用，绕开镜像自带版本。

注意发布包命名是 `zola-v0.23.6-x86_64-unknown-linux-gnu.tar.gz`（带 `v`），写成 `zola-0.23.6-...` 会 404。

## 自定义域

Pages 项目里 `tianyi.win` 需要在 DNS 侧有一条：

```
tianyi.win  CNAME  win-tianyi-zola.pages.dev  (Proxied / 橙云)
```

在 Pages → 自定义域 → `tianyi.win` → 「完成 DNS 设置」→「添加记录」会自动写入。DNS 没配好时访问会返回 `error code: 1014`（CNAME target 不存在）。

## 验收清单

- [ ] `zola build` 本地通过：`-> Creating 6 pages (0 orphan) and 7 sections`
- [ ] push 后 Pages 构建状态 `success`
- [ ] `https://win-tianyi-zola.pages.dev/` 200，标题 `TIANYI`
- [ ] `https://win-tianyi-zola.pages.dev/en/` 200
- [ ] 自定义域 `https://tianyi.win/` 200（需 DNS 记录就位）
- [ ] 语言按钮中文页显示 `English`、英文页显示 `中文`
