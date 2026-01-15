
# Gemini CLI 配置全攻略 

## 一句话核心思想

Gemini CLI 构建了**7 级优先级分层配置体系**，通过 `settings.json` 实现持久化运行规则管控、GEMINI.md 定义 AI 项目认知边界，搭配环境变量 / 命令行参数完成临时配置调整，同时依托沙箱隔离、敏感信息脱敏、数据收集开关三大功能，实现灵活、安全、可定制的使用体验。

## 思维导图结构（完整层级细节）

plaintext

```plaintext
Gemini CLI 配置体系
├─ 1. 配置层级与优先级规则（7级，低→高，高优先级覆盖低优先级）
│  ├─ 1级：默认值 - CLI 内置出厂设置（如 outputFormat=text、preview=false、timeout=15s）
│  ├─ 2级：系统默认文件 - 系统级基础配置
│  │  ├─ Windows路径：C:\ProgramData\gemini-cli\system-defaults.json
│  │  ├─ Mac/Linux路径：/etc/gemini-cli/system-defaults.json
│  │  └─ 作用：所有用户通用基础配置，优先级最低
│  ├─ 3级：用户设置文件 - 当前用户全局配置
│  │  ├─ 路径：~/.gemini/settings.json（~=用户主目录）
│  │  └─ 作用：覆盖系统默认，适用于该用户所有项目
│  ├─ 4级：项目设置文件 - 项目专属配置
│  │  ├─ 路径：当前项目根目录/.gemini/settings.json
│  │  └─ 作用：覆盖用户配置，仅对当前项目生效
│  ├─ 5级：系统设置文件 - 企业级管控配置
│  │  ├─ 路径：Windows(C:\ProgramData\gemini-cli\settings.json)、Mac/Linux(/etc/gemini-cli/settings.json)
│  │  └─ 作用：管理员统一管控，优先级高于用户/项目配置
│  ├─ 6级：环境变量 - 会话级临时配置
│  │  ├─ 加载顺序：当前目录.env → 父目录.env → ~/.env → 系统环境变量
│  │  ├─ 核心优势：存储敏感信息（如 API 密钥），不写入配置文件
│  │  └─ 生效范围：终端会话内有效，关闭终端失效
│  ├─ 7级：命令行参数 - 单次最高优先级配置
│  │  ├─ 示例：gemini -m gemini-1.5-pro --output-format json
│  │  └─ 核心优势：无需修改配置文件，覆盖所有其他层级配置
│  └─ 优先级示例：用户文件设output=json → 命令行传--output-format text → 最终生效text
├─ 2. settings.json 核心配置文件（持久化配置载体）
│  ├─ 配置文件层级分类
│  │  ├─ 用户级：~/.gemini/settings.json（全局通用，如默认模型、全局超时）
│  │  └─ 项目级：项目根/.gemini/settings.json（项目专属，如上下文文件列表、沙箱默认开启）
│  ├─ 标准文件结构（JSON 键值对，严格格式要求）
│  │  {
│  │    "general": {
│  │      "previewFeatures": false,
│  │      "preferredEditor": "code",
│  │      "vimMode": true
│  │    },
│  │    "output": { "format": "markdown" },
│  │    "model": { "name": "gemini-1.5-pro" },
│  │    "context": { "fileName": ["GEMINI.md", "CODING_RULES.md"] },
│  │    "tools": { "sandbox": true },
│  │    "privacy": { "usageStatisticsEnabled": false }
│  │  }
│  ├─ 高频核心配置项分类
│  │  ├─ 通用功能：general.previewFeatures（预览功能）、general.vimMode（Vim 快捷键）
│  │  ├─ 输出配置：output.format（text/json/markdown）
│  │  ├─ 模型配置：model.name（模型名称）、model.maxSessionTurns（会话最大轮次）
│  │  ├─ 上下文配置：context.fileName（加载的上下文文件列表）
│  │  ├─ 工具配置：tools.sandbox（沙箱开关）、tools.autoAccept（自动批准工具调用）
│  │  └─ 隐私配置：privacy.usageStatisticsEnabled（使用统计开关）
│  └─ 格式注意事项：JSON 需用双引号、键值对后无尾逗号、层级嵌套正确，格式错误会导致配置失效
├─ 3. 辅助配置方式（临时/敏感场景首选）
│  ├─ 环境变量配置
│  │  ├─ 常用变量及设置命令
│  │  │  ├─ GEMINI_MODEL（指定模型）
│  │  │  │  ├─ Mac/Linux：export GEMINI_MODEL=gemini-1.5-flash
│  │  │  │  └─ Windows：set GEMINI_MODEL=gemini-1.5-flash
│  │  │  ├─ GEMINI_API_KEY（API 密钥）
│  │  │  │  └─ 示例：export GEMINI_API_KEY=your-secret-key-xxx
│  │  │  ├─ GEMINI_SANDBOX（开启沙箱）
│  │  │  │  └─ 示例：export GEMINI_SANDBOX=true
│  │  │  └─ BUILD_SANDBOX（构建自定义沙箱）
│  │  │     └─ 示例：export BUILD_SANDBOX=1
│  │  └─ .env 文件配置示例
│  │     GEMINI_MODEL=gemini-1.5-pro
│  │     GEMINI_API_KEY=your-secret-key-xxx
│  │     GEMINI_SANDBOX=true
│  └─ 命令行参数配置
│     ├─ 高频参数及示例
│     │  ├─ --model/-m：指定模型 → gemini -m gemini-1.5-pro
│     │  ├─ --output-format：指定输出格式 → gemini --output-format json
│     │  ├─ --sandbox/-s：开启沙箱 → gemini -s
│     │  ├─ --yolo：自动批准所有工具调用 → gemini --yolo
│     │  ├─ --help/-h：查看参数列表 → gemini -h
│     │  └─ --resume：恢复历史会话 → gemini --resume latest
│     └─ 核心特点：单次生效、优先级最高、无需修改配置文件
├─ 4. 项目上下文管理（GEMINI.md 系列文件）
│  ├─ 核心定位：与 settings.json 功能分离，仅管控 AI 认知范围，不影响 CLI 运行
│  ├─ 文件配置方式
│  │  ├─ 默认文件：GEMINI.md（CLI 自动扫描加载）
│  │  └─ 自定义多文件：settings.json 中配置 context.fileName: ["CONTEXT.md", "RULES.md"]
│  ├─ 层级加载逻辑（从通用到具体，细则覆盖总则）
│  │  ├─ 1级：全局上下文 - ~/.gemini/GEMINI.md（所有项目通用规则）
│  │  ├─ 2级：项目上下文 - 项目根目录及父目录 GEMINI.md（项目级规则）
│  │  └─ 3级：子目录上下文 - 当前目录子文件夹 GEMINI.md（模块级细则）
│  ├─ 上下文文件内容示例（Markdown 格式）
│  │  # 项目：用户管理系统（TypeScript 版）
│  │  ## 技术栈要求
│  │  - 必须使用 TypeScript 5.0+，禁止使用 any 类型
│  │  - 接口请求统一使用 utils/request.ts，添加 Token 头
│  │  - 数据库操作使用 Prisma ORM，禁止直接执行 SQL
│  │  ## 禁止操作
│  │  - 不修改 node_modules、src/assets 目录文件
│  │  - 不新增未在 package.json 声明的依赖包
│  └─ 核心管理命令
│     ├─ /memory refresh：强制重新扫描加载所有上下文文件（修改后生效）
│     └─ /memory show：查看合并后的完整上下文内容（验证配置是否加载）
└─ 5. 安全与隐私配置（风险防护核心）
   ├─ 沙箱模式（操作隔离防护）
   │  ├─ 核心作用：限制 CLI 操作范围，避免误删文件、乱装依赖，保护真实系统
   │  ├─ 三种开启方式
   │  │  ├─ 命令行（单次）：gemini -s / gemini --sandbox
   │  │  ├─ 环境变量（会话级）：export GEMINI_SANDBOX=true
   │  │  └─ 配置文件（持久化）：settings.json 中设 "tools": { "sandbox": true }
   │  └─ 自定义沙箱环境（Docker 版）
   │     ├─ 步骤1：项目根/.gemini/ 下创建 sandbox.Dockerfile
   │     ├─ 步骤2：编写自定义镜像内容（基于官方镜像扩展）
   │     └─ 步骤3：运行命令 BUILD_SANDBOX=1 gemini -s 自动构建启动
   ├─ 环境变量脱敏（敏感信息防泄露）
   │  ├─ 默认脱敏规则（自动识别，无需手动配置）
   │  │  ├─ 按名称脱敏：变量名含 TOKEN、SECRET、PASSWORD、KEY、AUTH 等关键词
   │  │  └─ 按内容脱敏：匹配 API 密钥、RSA 私钥、带密码的 URL 等格式
   │  └─ 自定义脱敏规则（settings.json 配置）
   │     {
   │       "security": {
   │         "environmentVariableRedaction": {
   │           "allowed": ["MY_PUBLIC_KEY"],  // 永不脱敏的变量
   │           "blocked": ["INTERNAL_DB_PASS"] // 强制脱敏的变量
   │         }
   │       }
   │     }
   └─ 使用统计配置（隐私自主管控）
      ├─ 默认行为：匿名收集工具调用次数、模型请求成功率等数据，不收集代码/提示词/文件内容
      └─ 关闭方式：settings.json 中设 "privacy": { "usageStatisticsEnabled": false }
```

## 关键概念深度解释

1. **配置优先级层级**
    
    7 级层级的设计核心是 **“兼顾管控与灵活”**：企业管理员可通过系统设置文件统一规范基础配置，用户可通过用户 / 项目配置文件定制个人习惯，临时需求则通过环境变量 / 命令行参数快速调整，实现 “全局规则不被破坏，局部需求灵活满足”。
    
2. **settings.json 与上下文文件的功能边界**
    
    - `settings.json`：**管 CLI 本身**，决定 CLI 的运行方式（用什么模型、是否开沙箱、输出什么格式），是 “工具的操作手册”；
    - GEMINI.md 等上下文文件：**管 AI 认知**，告诉 AI 项目的技术栈、编码规范、禁止操作，是 “AI 的项目字典”；
        
        两者完全独立，需同时配置才能让 Gemini CLI 既稳定运行，又输出贴合项目需求的结果。
3. **沙箱模式的核心价值**
    
    沙箱是 CLI 的 “安全隔离舱”，尤其适合**新手使用**和**测试场景**：在沙箱内执行的 `npm install`、文件修改等操作，仅在隔离环境生效，不会影响本地真实系统文件，彻底避免 “误操作删库”“安装恶意依赖” 等风险。
    
4. **环境变量脱敏机制**
    
    脱敏是 CLI 内置的 “敏感信息保镖”，无需手动配置即可自动识别并隐藏密钥、密码等信息。其原理是通过**关键词匹配 + 格式识别**双重规则，确保日志、输出中敏感信息以 `[REDACTED]` 显示，防止泄露。
    

## 金句摘录

1. 配置优先级口诀：**默认打底，文件定常，环境管会话，命令行说了算**。
2. 双文件定位：**settings.json 是 CLI 的操作手册，GEMINI.md 是 AI 的项目字典**。
3. 安全防护铁三角：**沙箱隔离操作风险，脱敏隐藏敏感信息，统计开关守护隐私**。
4. 上下文使用原则：**全局定通用规则，项目定专属规范，子目录定模块细则**。

## 行动建议（实操步骤）

1. **全局配置初始化（10 分钟搞定）**
    
    - 步骤 1：创建用户配置目录：`mkdir -p ~/.gemini`（Mac/Linux）或 `md ~/.gemini`（Windows）；
    - 步骤 2：新建 `~/.gemini/settings.json`，复制思维导图中的标准文件结构，修改 `model.name`、`output.format` 为常用值；
    - 步骤 3：新建 `~/.gemini/GEMINI.md`，写入所有项目通用规则（如 “代码必须加注释”“禁止使用 any 类型”）。
2. **项目专属配置（每个项目 3 分钟）**
    
    - 步骤 1：在项目根目录执行 `mkdir .gemini`；
    - 步骤 2：新建 `.gemini/settings.json`，配置 `context.fileName` 和 `tools.sandbox: true`；
    - 步骤 3：新建项目级 `GEMINI.md`，写入技术栈、依赖要求、禁止操作等规则；
    - 步骤 4：运行 `gemini /memory show` 验证上下文是否加载成功。
3. **敏感信息安全管理（必做）**
    
    - 所有 API 密钥、数据库密码一律用环境变量存储，不写入任何配置文件；
    - 项目根目录新建 `.env` 文件，写入环境变量，同时在 `.gitignore` 中添加 `.env`，避免提交到代码仓库；
    - 运行 CLI 后，查看输出日志，确认敏感信息已被脱敏显示为 `[REDACTED]`。
4. **配置失效排查技巧**
    
    - 先运行 `gemini --help` 检查命令行参数是否生效；
    - 用在线 JSON 校验工具检查 `settings.json` 格式是否正确；
    - 确认配置文件路径是否符合要求（用户级在 `~/.gemini`，项目级在 `.gemini`）；
    - 上下文加载失败时，运行 `gemini /memory show` 查看加载结果，检查 `context.fileName` 配置是否正确。