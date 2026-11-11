# AI 中转站推荐 2026：Claude、ChatGPT、Grok、Gemini 一个 Key 全接通

Token173 聚合中转平台（https://token173.net）把 OpenAI、Anthropic、Google、xAI 和国内几家大模型的接口收在同一个 Base URL 后面。你在后台拿一个 Key，客户端里只改一处地址，就能在 Claude、GPT、Gemini、Grok 之间随意换模型，不用为每家单独准备海外信用卡、也不用挂着代理跑。

文档站：https://docs.token173.net

## 适合什么人用

- 手上同时跑 Claude Code、Cursor、Cherry Studio、LobeChat 的开发者，懒得给每个工具配一套 Key
- 做批量出图、批量文案、短视频脚本的工作室，需要按量付费、随时看消耗明细
- 小团队做灰度对比：同一个 prompt 丢给 4 家模型，看谁答得好再决定往哪个方向扩

如果你只需要单一模型、调用量也很小，直接用某一家官方渠道更省事；聚合平台的价值在于"换模型不改代码"。

## 一个 Key 能调到的模型

| 系列 | 模型 ID | 说明 |
|---|---|---|
| Claude | `claude-opus-5-5`、`claude-sonnet-5-5`、`claude-haiku-5-5` | 长文推理、代码重构主力 |
| Claude | `claude-fable-5-2`、`claude-fable-5-1`、`claude-fable-5`、`claude-opus-4-8` | 前沿任务线，5.2 最强，往下一档更便宜 |
| OpenAI | `gpt-6-astra`、`gpt-6.1-sol`、`gpt-6.1-terra`、`gpt-6-sol` | 当前主推，复杂推理+场景规划 |
| OpenAI | `gpt-5.6-sol`、`gpt-5.6-terra`、`gpt-5.5`、`gpt-5.4` | 老项目不换模型也能继续跑 |
| Gemini | `gemini-4-carbon`、`gemini-4-argon`、`gemini-3.8-flash`、`gemini-3-pro-preview` | Carbon 最强最贵，Argon 均衡，Flash 走速度 |
| Grok | `grok-4.7`、`grok-4.5` | 实时信息类问答 |
| 图像 | `gpt-image-2.6-flare`、`gpt-image-2.6-sunburst`、`gpt-image-2.5-sunburst`、`gpt-image-2` | 2.6 分 Flare（快速迭代）/ Sunburst（细节与编辑精度），直出 GIF/SVG |
| 视频 | `doubao-seedance-2-0-fast-260128`、`veo_3_1` | 短视频素材批量生成 |
| 国产 | `glm-5.3-flash`、`deepseek-v4-pro`、`deepseek-v4-pro-0813`、`deepseek-v4-flash-0731`、`kimi-k3`、`MiniMax-H3` | 中文长文、便宜跑量 |

模型名就是模型 ID，客户端里填什么就调什么，不用查映射表。

## 接入：改两个值

以 OpenAI 兼容格式为例：

```bash
export OPENAI_BASE_URL="https://token173.net/v1"
export OPENAI_API_KEY="sk-你的令牌"
```

Python：

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-你的令牌",
    base_url="https://token173.net/v1",
)

resp = client.chat.completions.create(
    model="gpt-6.1-sol",
    messages=[{"role": "user", "content": "用三句话说明什么是向量检索"}],
)
print(resp.choices[0].message.content)
```

Claude Code 走的是 Anthropic 协议，改这两个环境变量：

```bash
export ANTHROPIC_BASE_URL="https://token173.net"
export ANTHROPIC_API_KEY="sk-你的令牌"
```

## 各客户端填哪个地址

| 客户端 | Base URL | 备注 |
|---|---|---|
| Cherry Studio / LobeChat / ChatBox | `https://token173.net/v1` | 选 OpenAI 兼容类型 |
| Claude Code / Cline | `https://token173.net` | Anthropic 协议，不要带 `/v1` |
| Cursor | `https://token173.net/v1` | 设置里选 Override OpenAI Base URL |
| OneAPI / NewAPI 反代 | `https://token173.net` | 作为上游渠道填 |
| n8n / Dify | `https://token173.net/v1` | HTTP Request 节点加 Bearer 头 |

填错最常见的情况就是 Anthropic 协议多加了 `/v1`，报 404 就是这个问题。

## 计费

- 充值比例：2 元 = 1 美元额度，一个账号通用全部模型
- 注册送 0.2 美元额度，不限时，用完再充
- 按 token 扣费，后台日志能看到每一笔的输入、输出、扣了多少
- 请求失败不扣额度

换算成人民币大约是官方价格的 2.8 折到 3 折区间，具体每个模型的倍率在后台模型列表里标着，不用自己算。

## 常见问题

**Q：一个 Key 能在多个工具里同时用吗？**
能。同一个令牌可以在任意多个客户端同时调用，只要总额度够。要单独核算某个项目的消耗，就在后台多建几个令牌分开用。

**Q：跑着跑着报 401 或 403 怎么办？**
先确认 Key 没被删、余额没见底，其次检查 Base URL 有没有多余的空格或路径。Claude Code 里如果用了 `/v1` 结尾会直接 404。

**Q：模型会不会被换成便宜的旧版本？**
回包里 `model` 字段是实际调用的模型名，接进去先发一条测试请求对一下。对不上就换令牌或找客服。

**Q：能开票吗？**
后台有充值记录，需要开票联系客服，说明金额和抬头。

**Q：数据安全怎么算？**
请求会经过中转服务器。涉及内部代码、客户数据这类内容，先在测试环境验证再决定是否放到生产流程里。

## 相关仓库

- Claude 专题：[claude-api](https://github.com/Token173/claude-api)
- GPT 专题：[gpt-api](https://github.com/Token173/gpt-api)
- Gemini 专题：[gemini-api](https://github.com/Token173/gemini-api)
- Grok / 综合对比：[2026-Token173-Claude-ChatGPT-Grok-Gemini-AI-API](https://github.com/Token173/2026-Token173-Claude-ChatGPT-Grok-Gemini-AI-API)
- 完整接入文档：[doc-token173-api](https://github.com/Token173/doc-token173-api)
