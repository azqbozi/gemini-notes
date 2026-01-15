
# Gemini CLI 认证配置结构化笔记
## 一、核心目标
- 完成 Gemini CLI 与 Google 服务的身份验证，确保 CLI 正常调用相关功能
- 针对不同用户类型/使用场景，提供最优认证方案选型

## 二、认证方案选型（按用户/场景划分）
| 用户类型/场景                       | 推荐认证方式                     | 是否需要 Google Cloud 项目    |
| ----------------------------- | -------------------------- | ----------------------- |
| 个人 Google 账号（免费/Pro/Ultra 订阅） | 登录 Google 账号               | 否（特殊情况除外）               |
| 组织账号（企业/学校/Google Workspace）  | 登录 Google 账号               | 是                       |
| AI Studio 用户                  | 使用 Gemini API Key          | 否                       |
| Vertex AI 用户                  | Vertex AI 认证               | 是                       |
| 无头模式（Headless Mode）           | Gemini API Key / Vertex AI | API Key 无需；Vertex AI 需要 |

### 账号类型说明
- 个人账号：含免费层级、Google AI Pro/Ultra 付费订阅
- 组织账号：通过企业/学校授权的付费账号，含 Google AI Ultra for Business

## 三、主流认证方法操作步骤
### 1. 推荐方案：Google 账号登录（本地机器适用）
- **前置条件**：本地终端与浏览器可互通
- **操作步骤**
  1. 终端执行命令启动 CLI：`gemini`
  2. 选择「Login with Google」，浏览器自动弹出登录页面，按提示完成授权
  3. 凭证会本地缓存，后续会话无需重复登录
- **特殊要求**
  - Pro/Ultra 订阅用户需使用订阅关联的 Google 账号
  - 组织账号/开发者计划授权账号需额外配置 Google Cloud 项目

### 2. 备选方案：Gemini API Key 认证
- **适用场景**：不愿使用 Google 账号直接登录的用户
- **操作步骤**
  1. 从 Google AI Studio 获取 API Key
  2. 终端设置环境变量：`export GEMINI_API_KEY="YOUR_GEMINI_API_KEY"`
  3. 启动 CLI：`gemini`，选择「Use Gemini API key」完成认证
- **安全提示**：API Key 属于敏感凭证，需严格保护，避免泄露

### 3. 专业方案：Vertex AI 认证（适用于 Google Cloud 平台用户）
- **前置条件**：启用 Vertex AI API 的 Google Cloud 项目；设置两个环境变量
```bash
  export GOOGLE_CLOUD_PROJECT="YOUR_PROJECT_ID"
  export GOOGLE_CLOUD_LOCATION="YOUR_PROJECT_LOCATION" # 如 us-central1
```


