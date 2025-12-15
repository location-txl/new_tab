# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

基于 [chrome-extension-boilerplate-react-vite](https://github.com/Jonghakseo/chrome-extension-boilerplate-react-vite) 的 Chrome 扩展项目。使用 React 19、TypeScript、Vite 和 Turborepo 构建，支持 Chrome 和 Firefox。

## 常用命令

```bash
# 开发
pnpm dev              # Chrome 开发模式（HMR）
pnpm dev:firefox      # Firefox 开发模式

# 构建
pnpm build            # Chrome 生产构建
pnpm build:firefox    # Firefox 生产构建

# 代码质量
pnpm lint             # ESLint 检查
pnpm lint:fix         # 自动修复 lint 问题
pnpm type-check       # TypeScript 类型检查
pnpm format           # Prettier 格式化

# 测试与打包
pnpm e2e              # 端到端测试
pnpm zip              # 打包扩展为 zip

# 依赖安装
pnpm i <pkg> -w                # 安装到根目录
pnpm i <pkg> -F <module>       # 安装到特定模块（如 -F new-tab）

# 模块管理
pnpm module-manager   # 启用/禁用扩展模块
```

## 项目架构

### Monorepo 结构（Turborepo + pnpm workspace）

```
├── chrome-extension/     # 扩展核心
│   ├── manifest.ts       # 清单配置（自动生成 manifest.json）
│   └── src/background/   # Service Worker 后台脚本
├── pages/                # 扩展页面（独立 Vite 项目）
│   ├── new-tab/          # 新标签页覆盖
│   ├── popup/            # 点击图标弹窗
│   ├── options/          # 设置页面
│   ├── devtools/         # DevTools 入口
│   └── devtools-panel/   # DevTools 面板
└── packages/             # 共享包
    ├── storage/          # Chrome Storage 封装（createStorage）
    ├── shared/           # 共享组件、hooks、utils
    ├── i18n/             # 国际化（t 函数）
    ├── ui/               # UI 组件（Tailwind 集成）
    ├── env/              # 环境变量管理
    └── hmr/              # 热更新插件
```

### 关键机制

1. **Storage 系统**：`packages/storage` 提供响应式存储封装
   - `createStorage()` 创建存储实例
   - `useStorage()` 在 React 中订阅存储变化
   - 示例：`exampleThemeStorage` 在 `packages/storage/lib/impl/`

2. **国际化**：`packages/i18n`
   - 语言文件：`packages/i18n/locales/{locale}/messages.json`
   - 使用：`import { t } from '@extension/i18n'`

3. **Manifest 生成**：`chrome-extension/manifest.ts` 导出配置对象，构建时自动转换为 JSON

4. **包引用**：所有共享包使用 `@extension/` 前缀（如 `@extension/storage`）

## 开发流程

1. 运行 `pnpm dev` 启动开发服务器
2. 在 `chrome://extensions` 加载 `dist` 目录
3. 修改代码后扩展会自动热更新（某些情况需手动刷新）

## 环境变量

- `.env` 文件存放在项目根目录
- 环境变量需以 `CEB_` 前缀命名
- 详见 `packages/env/README.md`
