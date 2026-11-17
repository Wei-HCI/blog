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

## 本地预览

这台机器没装 Ruby，`bundle exec jekyll s` 跑不起来。两种办法：

- 直接 push，看 GitHub Actions 的构建结果（最省事）
- 想本地实时预览就装 Ruby 3.4 + `bundle install`，或 `docker run --rm -it -v "$PWD":/srv/jekyll -p 4000:4000 jekyll/jekyll jekyll s`

## 容易踩的三件事

1. **日期写成未来** → 构建成功但页面 404，看起来像部署失败
2. **文件名用中文** → URL 会变成一长串 `%E4%B8%AD%E6%96%87`，用英文短横线
3. **`tags` 里带空格或逗号** → YAML 会切错，标签用中英文都行，但别加空格
