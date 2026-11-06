# 部署说明（tianyi.win）

线上链路只有一条：

```
GitHub win-tianyi/win-tianyi.github.io (main)
        │  push 触发
        ▼
Cloudflare Pages 项目 win-tianyi-zola
        │  构建：Zola 0.23.6 → public/
        ▼
tianyi.win （Cloudflare 代理，橙云）
```

## 一、仓库

- 分支只有 `main`，内容就是站点源码（原先的 Hugo 站点已下线，`main` 历史已重写覆盖）。
- 旧 Hugo 站点快照备份在本地 `D:\AI\web\backup-hugo-main-780a57c.bundle`（原 commit `780a57c`），可用 `git clone backup-hugo-main-780a57c.bundle hugo-old` 还原。
- 构建产物 `public/` 已 gitignore，仓库里只放源码。

## 二、Cloudflare Pages 项目

项目名 `win-tianyi-zola`，Account ID `1b5ddf73a9f5a1af298bb4f527c2bea6`。

| 项 | 值 |
|---|---|
| Production branch | `main` |
| Framework preset | None |
| Build command | `curl -fsSL https://github.com/getzola/zola/releases/download/v0.23.6/zola-v0.23.6-x86_64-unknown-linux-gnu.tar.gz -o /tmp/zola.tar.gz && tar -xzf /tmp/zola.tar.gz -C /tmp && /tmp/zola --version && /tmp/zola build` |
| Build output directory | `public` |
| Root directory | 留空 |

**为什么构建命令里自己下载 zola**：Pages 构建镜像自带的是 asdf 装的 **Zola 0.22.1**（`/opt/buildhome/.asdf/shims/zola`），而本项目 `config.toml` 用了 0.23+ 才认识的 `skip_content_templating`，0.22 会直接 TOML 解析失败：

```
ERROR TOML parse error at line 10, column 1
10 | skip_content_templating = ["themes/**"]
   | ^^^^^^^^^^^^^^^^^^^^^^^
unknown field `skip_content_templating`
```

这台项目的环境变量 `ZOLA_VERSION` 实测不会被注入构建环境（构建日志里 `ENV ZOLA_VERSION=[]` 为空），所以版本固定写死在构建命令里，不依赖镜像预置版本，也不依赖环境变量。

## 三、域名

- `tianyi.win` 是 Zone（Zone ID `74684c3d14f6f380d90d07dd4fe93ffb`），NS 在 Cloudflare。
- apex 必须有一条 **CNAME `@` → `win-tianyi-zola.pages.dev`（Proxy 打开）**，Pages 才能校验通过。
- 如果 Custom domains 里 `tianyi.win` 停在 `pending`：到 Dashboard → Pages → `win-tianyi-zola` → Custom domains，把 `tianyi.win` 删掉再重新添加一次，Cloudflare 会用当前会话权限自动补上/改好那条 CNAME。

## 四、日常改动

```bash
git add -A && git commit -m "内容更新" && git push
```

push 到 `main` 后 Pages 自动构建，约 40 秒上线。手动触发：

```bash
curl -X POST -H "Authorization: Bearer $CF_API_TOKEN" -H 'Content-Type: application/json' -d '{}' \
  "https://api.cloudflare.com/client/v4/accounts/1b5ddf73a9f5a1af298bb4f527c2bea6/pages/projects/win-tianyi-zola/deployments"
```

## 五、验收清单

```bash
curl -s -o /dev/null -w '%{http_code}\n' https://tianyi.win/                     # 200
curl -s https://tianyi.win/ | grep -o '<title>[^<]*</title>'                     # Zola Docs · tianyi.win
curl -s https://tianyi.win/sitemap.xml | head -3                                 # <loc>https://tianyi.win/</loc>
curl -s -o /dev/null -w '%{http_code}\n' https://tianyi.win/documentation/       # 200
```
