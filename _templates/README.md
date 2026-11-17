# 推特式写作 / 动态流

这个目录是给「短写法」用的，不参与构建（下划线开头的目录 Jekyll 会跳过）。

| 文件/路径 | 作用 |
| --- | --- |
| `_templates/micro-post.md` | 单条动态模板：复制它开始写 |
| `_tabs/feed.html` | `/blog/feed/` 动态流页面，只收 `microblog: true` 的帖子 |
| `_posts/` | 真正发布的文件都放这里，文件名 `YYYY-MM-DD-slug.md` |

## 发一条动态（3 步）

1. 复制 `_templates/micro-post.md` → `_posts/2026-11-17-你的slug.md`（日期用当天）
2. 改 `title`、`date`、`tags`，写正文（第一段就是结论）
3. `git add -A && git commit -m "post: xxx" && git push`
   → GitHub Actions 自动构建，1-2 分钟后出现在 `/blog/` 和 `/blog/feed/`

## 字段速查

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| `title` | 建议 | 一句完整的话，≤ 20 字；也是列表里的标题 |
| `date` | 必填 | 不能是未来时间，否则不发布 |
| `categories` | 建议 | 固定用 `[动态]`，归档页靠它分组 |
| `tags` | 建议 | 2-3 个，标签页和搜索靠它 |
| `microblog` | **关键** | `true` 才进动态流；不加就是普通文章 |
| `pin` | 可选 | `true` 置顶，同时影响首页和动态流 |
| `toc` / `comments` | 可选 | 短动态建议都设 `false` |
| `description` | 可选 | SEO 摘要，留空自动取正文 |
| `hidden` | 可选 | `true` 首页和动态流都不显示（留作草稿） |

## 约定（决定它像不像推文）

- 一条只讲一个判断。讲不完就拆成「系列串」发，标题带 `01/02/03`
- 第一段 ≤ 2 行，是结论不是背景
- 代码块只留关键 15-25 行，外面必须有一句「看哪一行」
- 图片放 `assets/img/`，正文里写 `/assets/img/xxx.png`（`/blog` 前缀会自动补）
- 站内链接写成 `{{ site.baseurl }}/posts/xxx/`，别写死 `/posts/xxx/`

## 不要动态流了？

删除 `_tabs/feed.html` 即可，侧边栏的「动态」会消失，文章不受影响。
想改「写一条」按钮跳转的仓库，改 `_tabs/feed.html` 顶部的 `repo` 变量。

## 本地预览（已装好）

Ruby 3.4.12 + Devkit 装在 `C:\Ruby34-x64`（新开的终端已在 PATH 里），依赖已 `bundle install`
（`.bundle/config` 跳过了 test 组，本地不装 html-proofer）。

```bash
bundle exec jekyll s
# 首页 http://127.0.0.1:4000/blog/   动态流 http://127.0.0.1:4000/blog/feed/
```

改 md 保存后自动重建，刷新浏览器即可。终端若报 `ruby: command not found`，开一个新终端再跑。

## 页内发帖 / 改帖（博主入口，密码登录）

`/blog/feed/` 右上角有个小锁「博主入口」。访客只看到只读流；你输入密码登录后：

- 顶部出现发帖框：正文（Markdown）、标签、可选配图，点「发」→ 写进 `_posts/`，1-2 分钟后上线
- 每条动态底部出现「编辑」：原地改这条的 Markdown（含 front matter），点「保存」
- 每条动态底部出现「删除」：确认后从仓库删掉这个 md，1-2 分钟后从网站消失；
  GitHub 提交历史里仍有记录，误删可以找回（帖子里的配图不会一起删）
- 网页上发 / 改 / 删都会直接在 GitHub 上提交，本地写之前先 `git pull`

### 第一次设置（只做一次）

1. 建 token：github.com/settings/personal-access-tokens/new → Only select repositories
   选 `blog` → Repository permissions → Contents: Read and write → 有效期选最长
2. feed 页点「博主入口」→「首次设置 / 更换 token」→ 粘 token、设一个 ≥ 12 位的密码 →「加密保存」
3. 1-2 分钟后，任何设备打开 feed 页，输密码就能登录

### 原理与注意

- token 用你的密码加密（PBKDF2 60 万轮 + AES-GCM）后存成 `assets/mb-vault.json`；
  站点上只有密文，密码本身不存在任何地方
- 密文是公开的，所以**密码要够长**：别用生日、常用密码；4 个随机词或 14 位以上最稳
- 「这台设备保持登录」不勾 = 关掉标签页就退出（用别人电脑时别勾）
- token 过期或撤销后：输对密码会提示「token 已过期」→ 点「更换 token / 密码」粘新 token，
  密码可以照旧
- 忘了密码：用新 token 重新走一遍「首次设置」即可，旧密文会被覆盖
- 怀疑泄露：去 GitHub 设置撤销 token，立刻失效

## 容易踩的三件事

1. **日期写成未来** → 构建成功但页面 404，看起来像部署失败
2. **文件名用中文** → URL 会变成一长串 `%E4%B8%AD%E6%96%87`，用英文短横线
3. **`tags` 里带空格或逗号** → YAML 会切错，标签用中英文都行，但别加空格
