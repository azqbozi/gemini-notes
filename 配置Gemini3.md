
# Gemini 3 Pro/Flash 于 Gemini CLI 相关笔记
## 一、核心前提
- 适用模型：Gemini 3 Pro、Gemini 3 Flash（对所有 Gemini CLI 用户开放）
- 版本要求：Gemini CLI 0.21.1 及以上

## 二、Gemini 3 在 Gemini CLI 上手步骤
1. 升级 CLI 至最新版：npm install -g @google/gemini-cli@latest
2. 确认版本（需 ≥0.21.1）
3. 开启预览功能：输入 /settings 命令，将 Preview Features 设为 true
4. 选择模型：输入 /model 命令，选择 Auto (Gemini 3)（参考“Gemini CLI 模型选择”文档）

## 三、使用限制与 Fallback 机制
1. Gemini 3 Pro 每日上限：提示切换至 Gemini 2.5 Pro、升级提升上限或停止使用，告知重置时间
2. Gemini 2.5 Pro 每日上限：fallback 至 Gemini 2.5 Flash

## 四、容量错误处理（Gemini 3 Pro 过载时）
- 选项1：继续尝试（采用“指数退避”机制，需等待几分钟）
- 选项2：Fallback 至 Gemini 2.5 Pro

## 五、模型选择与路由类型
| 路由类型   | 逻辑说明                                                                 |
|------------|--------------------------------------------------------------------------|
| Auto 路由（默认） | 简单任务→Gemini 2.5 Flash；复杂任务→已启用 3 Pro 则用 3 Pro，否则用 2.5 Pro |
| Pro 路由       | 需通过 /model 选择 Pro，优先使用当前可用“最具能力的模型”（启用 3 Pro 则优先），适用于高需求任务 |

