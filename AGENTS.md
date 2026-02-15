# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

这是一个基于 Hugo 静态网站生成器的个人博客/文档站点"曜风"，使用 hugo-book 主题。项目通过 GitHub Actions 自动构建并部署到 GitHub Pages。

> 参见 [AGENT.md](AGENT.md) 了解本项目可用的 Agent 配置和使用规范。

## 核心命令

### 本地开发
```bash
# 启动开发服务器(热重载)
hugo server

# 带优化的开发服务器
hugo server --minify
```

### 构建与部署
```bash
# 生产构建(输出到 public/ 目录)
hugo --gc --minify

# 构建并指定缓存目录
hugo --gc --minify --cacheDir /path/to/cache
```

### 内容管理
```bash
# 创建新内容页面(基于 archetypes/default.md 模板)
hugo new content/文章名称.md

# 创建指定章节的文章
hugo new content/docs/章节/文章名称.md
```

### Git 子模块
```bash
# 初始化/更新主题子模块
git submodule update --init --recursive

# 更新主题到最新版本
cd themes/hugo-book
git pull
```

## 项目架构

### 目录结构
```
├── archetypes/          # 内容模板(default.md 定义新文章的前置元数据)
├── assets/             # 自定义资源(Sass/CoffeeScript,会覆盖主题资源)
├── content/            # 网站内容(Markdown 文章)
├── data/               # 数据文件(JSON/YAML/TOML,供模板调用)
├── i18n/               # 国际化翻译文件
├── layouts/            # 自定义布局(可覆盖主题模板)
├── static/             # 静态资源(图片、CSS、JS、favicon 等)
├── public/             # 构建输出目录(生成的静态网站,不提交到 Git)
├── themes/hugo-book/   # hugo-book 主题(git submodule)
├── hugo.toml           # Hugo 主配置文件
└── .github/workflows/  # GitHub Actions CI/CD 配置
```

### hugo-book 主题特性
- **文档风格**: 专为文档/教程设计的书籍风格布局
- **菜单系统**: 支持 `menu.before`、`menu.after` 自定义导航
- **多语言**: 支持多语言内容(通过 `contentDir` 分离不同语言)
- **搜索功能**: 内置全文搜索(需要配置)
- **短代码**: 扩展短代码支持(Mermaid 图表、KaTeX 数学公式、按钮等)
- **主题切换**: 支持亮/暗模式(`BookTheme` 参数)

### 配置文件
- **hugo.toml**: 基础配置
  - `baseURL`: 生产环境 URL
  - `theme`: 使用的主题名称
  - `languageCode`: 语言代码
  - `[caches.images]`: 图片处理缓存配置

### 内容结构
hugo-book 主题推荐的内容组织:
```
content/
├── docs/               # 文档章节
│   ├── chapter1/       # 子章节会自动创建侧边栏菜单
│   └── chapter2/
└── _index.md           # 章节封面页
```

### Git 子模块管理
主题 `themes/hugo-book` 通过 git submodule 引用:
- 更新子模块: `git submodule update --remote themes/hugo-book`
- 检出特定版本: `cd themes/hugo-book && git checkout <tag>`
- 主题仓库: https://github.com/alex-shpak/hugo-book

## CI/CD 工作流

GitHub Actions (`.github/workflows/hugo.yaml`) 自动化部署:

**触发条件**: 推送到 `main` 分支或手动触发

**环境配置**:
- Go 1.25.5
- Node.js 24.12.0
- Hugo 0.154.2 extended
- Dart Sass 1.97.1
- 时区: Asia/Shanghai

**构建优化**:
- 递归检出子模块(`submodules: recursive`)
- 启用 Hugo 缓存(`--cacheDir`)
- 垃圾回收和压缩(`--gc --minify`)
- 使用 Dart Sass 处理主题样式

**部署流程**:
1. 安装依赖(Hugo、Go、Node.js、Dart Sass)
2. 恢复 Hugo 缓存
3. 构建站点到 `public/`
4. 保存缓存供下次使用
5. 上传 artifact 并部署到 GitHub Pages

## 开发注意事项

### 主题自定义
1. **布局覆盖**: 在 `layouts/` 目录创建与主题相同的路径结构可覆盖模板
2. **资源覆盖**: `assets/` 中的文件会覆盖主题的 `assets/` 文件
3. **静态文件**: `static/` 目录的文件会直接复制到网站根目录

### 内容最佳实践
- 使用 front matter 定义元数据(title、date、draft 等)
- 文章文件名会成为 URL slug(可通过 `slug` 参数覆盖)
- 使用 `weight` 参数控制章节排序
- 主题支持 `BookToC` 参数控制目录显示
- 设置 `BookSearch: true` 启用搜索功能

### 图像处理
- 配置了 `[caches.images]` 以优化图像处理性能
- Hugo 支持图像 resizing、cropping、滤镜等处理
- 生产构建时建议使用 `--gc` 清理未使用的资源缓存

### 本地测试
推荐在部署前本地测试:
```bash
hugo server --buildDrafts --buildFuture
```

## 扩展阅读
- Hugo 文档: https://gohugo.io/documentation/
- hugo-book 主题文档: 见 `themes/hugo-book/README.md` 或主题示例站点
- GitHub Pages 部署: https://docs.github.com/en/pages
