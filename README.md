# Nu
古今中外各领域女性资料整理


基于我们之前讨论的方案——**Hugo静态生成器 + 两栏参考文献布局 + GitHub托管**，下面是你需要创建的核心文件结构。

我会用树形图展示，并标注每个文件/文件夹的作用。你可以直接在你的电脑上按此结构创建，然后推送到GitHub。

```
your-site/
├── config.toml
├── content/
│   ├── reality/               # 现实人物
│   │   ├── china/
│   │   │   ├── ancient/
│   │   │   │   └── wangzhenyi.md
│   │   │   ├── modern/
│   │   │   │   └── qiujin.md
│   │   │   └── contemporary/
│   │   │       └── tuyouyou.md
│   │   ├── asia/
│   │   ├── europe/
│   │   ├── americas/
│   │   └── africa-oceania/
│   ├── myth/                  # 神话人物
│   │   ├── china-myth/
│   │   │   └── xiwangmu.md
│   │   ├── greek-myth/
│   │   └── other-myth/
│   └── topics/                # 专题页面（独立内容）
│       ├── shanhaijing/       # 《山海经》专题
│       │   └── index.md       # 专题正文
│       ├── nobel-women/       # 诺贝尔奖女性专题
│       │   └── index.md
│       └── first-women/       # 中国第一人专题
│           └── index.md
├── layouts/
│   ├── _default/
│   │   ├── baseof.html        # 基础骨架
│   │   ├── single.html        # 人物详情页（左文右证）
│   │   └── list.html          # 分类列表页（人物卡片网格）
│   ├── index.html             # 首页（英雄区+最新+随机+专题入口+数据统计）
│   ├── topics/                # 专题页专用模板
│   │   └── single.html        # 专题页模板（引言+特色内容+人物列表+额外资料）
│   └── partials/              # 可复用组件
│       ├── header.html
│       ├── footer.html
│       ├── hero.html          # 英雄区
│       ├── latest.html        # 最新添加人物
│       ├── random.html        # 随机推荐
│       ├── topics-grid.html   # 热门专题入口
│       ├── stats.html         # 数据统计
│       ├── card.html          # 人物卡片（复用）
│       └── references.html    # 右侧参考文献栏
├── static/
│   ├── css/
│   │   └── custom.css
│   ├── js/
│   │   ├── references.js      # 脚注高亮
│   │   ├── random.js          # 随机推荐逻辑（前端实现）
│   │   └── d3.js              # 关系网络可视化（可选）
│   └── images/
├── data/                      # 可选：存放统计数据（如人物总数）
│   └── stats.json
└── .github/workflows/
    └── hugo.yml
```

---

## 各文件/文件夹详细说明

### 1. 根目录核心文件
- **`config.toml`**：Hugo站点配置，包括网站标题、语言、主题、菜单、自定义参数等。你需要在这里定义主菜单（现实人物、神话传说、专题），并启用自定义模板路径。

### 2. `content/` – 所有人物文章
- 按“现实 vs 神话”分成两大目录，再按地域/文化进一步细分。  
- 每个人物是一个 `.md` 文件，在文件头部用 Front Matter 填写标签、分类、图片等。  
- 脚注使用标准 `[^1]` 格式，Hugo会自动生成脚注列表，再由模板提取到右侧栏。

### 3. `layouts/` – 控制页面显示
- **`_default/baseof.html`**：基础骨架，包含 `<html>`、`<head>`、`<body>` 以及 `{{ block }}` 占位符。
- **`_default/single.html`**：人物详情页模板。  
  - 这里实现 **左文右证** 布局：左侧 `.main-content` 渲染 `{{ .Content }}`，右侧 `.references` 用来显示参考文献。  
  - 需要编写脚本来提取脚注并动态高亮（可直接在模板内嵌JS或引用外部 `static/js/references.js`）。
- **`_default/list.html`**：列表页（如 `/reality/china/modern/`）模板。展示人物卡片网格，支持分页和排序。
- **`partials/`**：可复用组件，例如：
  - `header.html`：包含网站logo、主菜单、搜索框（可用Pagefind）。
  - `footer.html`：版权、来源声明等。
  - `sidebar.html`：右侧边栏（标签云、随机推荐）。注意人物详情页右侧已被参考文献占，侧边栏可放在左侧下方或单独处理。

### 4. `static/` – 静态资源
- **CSS**：控制两栏布局、响应式、脚注样式等。  
- **JS**：实现右侧参考文献动态高亮、移动端折叠面板等。  
- **images**：按人物分类存放，使用相对路径引用，如 `/images/china/ancient/wangzhenyi/portrait.jpg`。

### 5. 主题与自动部署
- 如果你不想从零写模板，可以选用一个现成主题（如 `hugo-theme-stack`），然后在其基础上修改 `single.html` 实现两栏布局。  
- **`.github/workflows/hugo.yml`**：可选的自动化部署配置，让你每次 `git push` 后，GitHub自动构建并发布到 GitHub Pages。Hugo官方有现成的工作流模板。

---

## 下一步操作建议

1. **先搭骨架**  
   在你的电脑上创建上述文件夹结构（可暂时忽略 `themes/` 和 `.github/`），并创建 `config.toml` 和一个测试人物（如 `content/reality/china/ancient/wangzhenyi.md`）。  
2. **写一个简单模板**  
   在 `layouts/_default/single.html` 中先用最简单的单栏布局输出 `{{ .Content }}`，确保站点能运行。  
3. **实现两栏布局**  
   加入 CSS 和 JS，实现左文右证。可以先硬编码几条参考文献测试。  
4. **完善其他页面**  
   添加列表页、首页、导航等。  
5. **部署到 GitHub**  
   将仓库推送到GitHub，开启Pages（如果使用自动构建，则直接推送即可）。

如果你需要，我可以帮你生成 **`config.toml` 示例**、**人物Markdown模板**、**`single.html` 的关键代码片段**，让你直接复制使用。你希望我从哪个文件开始？
