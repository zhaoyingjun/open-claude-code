# Open Claude Code（Claude Code开源的代码）
> Claude Code基于 TypeScript 开发的 Agent 化 AI 编程助手 | 本地代码自动化开发 CLI 工具，本代码库是其开源代码的Fork版本，目的是一起研究学习Cluade Code优秀的Harness设计、工具调用、Memroy的管理等，打造让大家打造自己的AgentOS。
**仓库地址**：https://github.com/zhaoyingjun/open-claude-code

---

## 目录
- [项目简介](#项目简介)
- [核心特性](#核心特性)
- [系统环境要求](#系统环境要求)
- [快速安装部署](#快速安装部署)
- [环境变量配置](#环境变量配置)
- [使用指南](#使用指南)
- [命令参考](#命令参考)
- [开发者扩展](#开发者扩展)
  - [自定义 AI 模型](#自定义-ai-模型)
  - [二次开发流程](#二次开发流程)
- [项目目录结构](#项目目录结构)
- [故障排查](#故障排查)
- [安全与隐私说明](#安全与隐私说明)
- [许可证](#许可证)
- [贡献指南](#贡献指南)

---

## 项目简介
**Open Claude Code** 是一款面向开发者的轻量级 Agent 化编程 CLI 工具，基于 TypeScript + Node.js 构建，代码源自 Anthropic 开源贡献。

工具具备**全局代码仓库上下文理解能力**，可通过**自然语言指令**全自动完成：代码编辑、文件操作、Git 管理、Shell 命令执行等工程化开发任务，实现了从「代码补全工具」到「工程执行系统」的升级。

本项目开箱即用、无冗余依赖、支持自定义大模型接入，专注于本地开发自动化场景，适用于所有主流编程语言项目。

## 核心特性
- 🌍 **全局代码感知**：自动扫描项目结构、解析文件依赖，支持跨文件代码理解
- 🗣️ **自然语言驱动**：无需编写命令，直接用自然语言描述需求即可完成开发
- ⚙️ **工程自动化**：自动修改代码、执行脚本、Git 版本管理、文件增删改查
- 🔌 **模型可扩展**：默认支持 Claude 系列，可快速替换为任意大语言模型
- 💻 **纯 CLI 交互**：无图形界面，轻量化运行，全平台兼容
- 📦 **开箱即用**：TypeScript 编写，编译简单，一键启动无需复杂配置

## 系统环境要求
- **Node.js**：v18.0.0 及以上 LTS 版本（必须）
- **包管理器**：npm / pnpm / yarn
- **Git**：v2.0+（可选，用于 Git 相关功能）
- **操作系统**：macOS / Linux / Windows 全平台支持
- **内存**：最低 2GB，推荐 4GB+

## 快速安装部署
### 1. 克隆仓库
```bash
git clone https://github.com/zhaoyingjun/open-claude-code.git
cd open-claude-code
```

### 2. 安装依赖
```bash
npm install
# 或 pnpm install / yarn install
```

### 3. 编译 TypeScript 源码
```bash
# 生产环境编译
npm run build

# 开发环境热重载（文件修改自动编译）
npm run watch
```

### 4. 注册全局命令（推荐）
注册后可在**任意项目目录**直接调用工具
```bash
npm link
```

### 5. 验证安装
```bash
open-claude-code --help
open-claude-code --version
```

## 环境变量配置
### 1. 生成配置文件
```bash
cp .env.example .env
```

### 2. 核心配置项
编辑 `.env` 文件，填入你的配置：
```env
# ==================== AI 模型配置 ====================
# Anthropic API Key（使用 Claude 模型必填）
ANTHROPIC_API_KEY=your_anthropic_api_key

# 默认模型名称
DEFAULT_MODEL=claude-3-sonnet-20240229

# API 版本（固定）
ANTHROPIC_VERSION=2024-02-29

# ==================== 系统配置 ====================
# 调试模式
DEBUG=false

# 最大响应令牌数
MAX_TOKENS=4096
```

## 使用指南
### 启动工具
```bash
# 方式 1：全局命令（推荐，任意目录执行）
open-claude-code

# 方式 2：项目内启动
npm run start
```

### 交互式使用（核心）
启动后直接输入**自然语言指令**，AI 自动执行开发任务：
```text
> 分析当前项目结构并生成 README.md
> 修复 src 目录下的 TypeScript 类型错误
> 格式化所有代码文件
> 将修改提交到 Git 并生成提交信息
> 创建用户登录认证模块
> 运行测试并修复报错
```

### 单指令快速执行
```bash
# 直接执行单条任务
open-claude-code "重构项目入口文件，提升代码可读性"
```

## 命令参考
### CLI 基础命令
```bash
open-claude-code          # 启动交互式 AI 编程助手
open-claude-code "指令"   # 执行单条自然语言指令
open-claude-code -h       # 查看帮助
open-claude-code --help   # 查看帮助
open-claude-code -v       # 查看版本
```

### 交互式内置命令
```text
/help      查看帮助信息
/exit      退出程序
/clear     清空控制台
/bug       提交问题反馈
```

## 开发者扩展
### 自定义 AI 模型
本项目默认使用 Claude 模型，所有模型调用逻辑模块化封装，**支持快速替换为任意大语言模型**。

#### 核心文件路径
```typescript
src/llm/client.ts       # 模型客户端（核心调用逻辑）
src/llm/types.ts       # 模型类型定义
src/utils/config.ts    # 配置加载
```

#### 替换步骤
1. 修改 `src/llm/client.ts` 中的请求逻辑
```typescript
async generateCompletion(prompt: string): Promise<string> {
  const response = await fetch("https://api.your-llm.com/chat", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${this.apiKey}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      model: this.model,
      messages: [{ role: "user", content: prompt }],
      max_tokens: this.maxTokens,
    }),
  });

  const data = await response.json();
  // 根据你的模型返回格式修改解析逻辑
  return data.choices[0].message.content;
}
```

2. 更新 `.env` 配置
```env
API_KEY=your_custom_llm_key
DEFAULT_MODEL=your-model-name
```

3. 重新编译生效
```bash
npm run build
```

### 二次开发流程
```bash
# 启动开发监听模式
npm run watch

# 代码语法检查
npm run lint

# 本地测试运行
npm run start
```

## 项目目录结构
```
open-claude-code/
├── src/                    # TypeScript 源码目录
│   ├── cli.ts              # CLI 交互入口
│   ├── index.ts            # 项目主入口
│   ├── core/               # 核心功能模块
│   │   ├── agent.ts        # Agent 调度核心
│   │   ├── code-editor.ts  # 代码/文件操作
│   │   ├── git.ts          # Git 功能封装
│   │   └── prompt.ts       # 提示词构建
│   ├── llm/                # 大模型调用模块
│   │   ├── client.ts       # 模型客户端
│   │   └── types.ts        # 类型定义
│   └── utils/              # 工具函数
│       ├── config.ts       # 配置加载
│       ├── fs.ts           # 文件系统工具
│       └── logger.ts       # 日志工具
├── dist/                   # 编译输出目录
├── .env.example            # 环境变量模板
├── package.json            # 依赖与脚本
└── tsconfig.json           # TypeScript 配置
```

## 故障排查
### 1. 依赖安装失败
```bash
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

### 2. TypeScript 编译报错
```bash
npm run lint
```

### 3. 模型 API 调用失败
1. 检查 `.env` 中的 API Key 是否正确
2. 确认网络可访问模型接口
3. 开启 `DEBUG=true` 查看详细请求日志
4. 核对模型名称与接口版本

### 4. 全局命令不生效
```bash
npm unlink && npm link
```

### 5. 项目扫描缓慢
- 忽略 `node_modules`/`dist`/`.git` 等无关目录
- 关闭调试模式提升运行速度

## 安全与隐私说明
1. **数据流向**：项目代码上下文仅发送至你配置的 AI 模型接口，无第三方数据收集
2. **本地运行**：所有代码执行、文件操作均在本地完成，无远程代码上传
3. **Git 保护**：建议在 Git 仓库中使用，可随时回滚 AI 自动修改的代码
4. **敏感项目**：可接入本地部署大模型，完全隔离外网传输

## 许可证
本项目基于 **MIT License** 开源，自由使用、修改与分发。

## 贡献指南
欢迎提交 Issue 反馈问题，或通过 Pull Request 贡献代码：
1. Fork 本仓库
2. 创建功能分支 (`git checkout -b feature/xxx`)
3. 提交修改 (`git commit -m 'Add some feature'`)
4. 推送分支 (`git push origin feature/xxx`)
5. 提交 Pull Request
