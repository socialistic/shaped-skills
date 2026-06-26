---
name: Afterglow 续温
description: |
  从QQ/微信聊天记录中提取对方语气和表达习惯，通过RAG+Persona让AI以ta的风格继续陪你说话。
  不微调模型，不需要GPU，导入真实历史对话即可重现熟悉的语气。
---

# Afterglow 续温 — AI 聊天记录陪伴复刻

> 来源: https://github.com/kldhsh123/Afterglow
> 原始发现: https://github.com/kldhsh123/Afterglow

## 这个技能做什么

Afterglow（续温）是一个本地运行的「AI 朋友」系统。用户导入与某人的真实聊天记录（QQ / 微信），系统通过 RAG 混合检索 + Persona 卡片 + OpenAI 兼容 API，让 AI 以接近原始对话对象的语气继续对话。

核心能力:
- **聊天记录导入与清洗**: 支持 QQ（QQChatExporter v5 JSON）和微信（WeFlow JSON），自动 PII 脱敏
- **三索引混合检索**: 向量检索 + 关键词检索 + 时间衰减，可选 cross-encoder 精排（Qwen3-Reranker）
- **Persona 自动生成**: 从聊天语料分析对方的称呼习惯、语气词、表情偏好、话题倾向，生成 Persona 卡片
- **生活状态时间线**: 动态人设，AI 分身的"当前状态"随时间变化
- **关系记忆蒸馏**: 提炼你们之间的关系特征，让回复更贴合真实互动模式
- **可选长期记忆**: `AI_GENERATED_LONG_TERM_ENABLED` 开关控制 AI 自己的回复是否跨会话累积

## 用户需要提供什么

1. **聊天记录导出文件**: QQ 使用 QQChatExporter 导出 JSON，微信使用 WeFlow 导出 JSON
2. **对方的基本信息**: 昵称、你们的关系类型（朋友/恋人/家人/同事）
3. **OpenAI 兼容 API 配置**: 主聊天模型、Embedding 模型的 API 地址和密钥

## 工作流程

### 第一步: 环境准备
- 安装 Python 3.10+ 和 uv
- 克隆仓库: `git clone https://github.com/kldhsh123/Afterglow.git`
- 复制 `backend/.env.example` 为 `backend/.env`，填写模型 API 配置

### 第二步: 导入聊天记录
- 将导出的 JSON 文件放入指定目录
- 运行导入脚本: `python -m xuwen.ingestion.cli import --source <json路径>`
- 系统自动完成清洗、分块、向量化、PII 脱敏

### 第三步: 生成 Persona
- 导入完成后自动分析语料生成 Persona 卡片
- 可通过 `python backend/scripts/analyze_persona.py` 查看和调整 Persona
- Persona 模板支持: 朋友(friend)、恋人(lover)、家人(family)、同事(colleague)

### 第四步: 开始对话
- 启动后端服务
- 通过前端界面或 API 开始对话
- 系统会根据你的消息检索相关历史语料，以 ta 的语气回复

## 技术架构

- **后端**: Python (FastAPI)，核心模块在 `backend/xuwen/`
- **前端**: Vue + Tailwind，用于本地调试
- **向量库**: LanceDB（本地，无需外部数据库）
- **模型接入**: OpenAI 兼容 API（支持任何兼容接口）
- **Docker 部署**: 提供 `compose.yaml`

## 重要提醒

- 这是**文字复刻**，不是语音/影像还原
- 相似度取决于聊天记录数量和对方风格鲜明程度
- 不如模型微调(LoRA)精确，但远好于普通角色扮演 GPT
- 请遵守伦理边界: 不得用于骚扰、冒充、欺诈
- 数据隐私由用户自行负责，选择有"不用于训练"承诺的 API 服务商

## 相关链接

- 项目主页: https://github.com/kldhsh123/Afterglow
- QQBot 适配器: https://github.com/kldhsh123/Afterglow-QQBot
- 后端核心代码: https://github.com/kldhsh123/Afterglow/tree/main/backend/xuwen
- Persona 模板: https://github.com/kldhsh123/Afterglow/tree/main/backend/xuwen/persona/templates
- 导入插件: https://github.com/kldhsh123/Afterglow/tree/main/backend/xuwen/ingestion/plugins
