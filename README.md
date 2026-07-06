<div align="center">

# 🌐 Tavily Hub

**1 个 Hub Key，背后 N 个 Tavily 账号。用得越快，回得越快。**

为国内 AI 开发者打造的 **Tavily 统一代理网关 + Credit 池 + MCP 一键接入**

**🪪 无需国际信用卡　·　🧠 国内直连　·　⚡ MCP streamableHTTP + SSE 双协议　·　💰 支付宝按量付费**

👉 **官网：[https://tavily.sharyuke.com](https://tavily.sharyuke.com/)**

</div>

---

## 💡 为什么是 Tavily Hub？

[Tavily](https://tavily.com/) 是当下 AI / RAG / Agent 最常用的搜索 API。但对国内开发者来说，原生使用有一道绕不过去的坎：

> **注册 Tavily 需要海外信用卡，门槛高、续费难、经常被风控。**

Tavily Hub 就是为了把这道坎彻底拆掉 —— **支付宝扫码就能用，没有国际信用卡也能稳定、大量地调用 Tavily 搜索。**

| 你遇到的坎 | Tavily Hub 的解法 |
|---|---|
| ❌ 注册 Tavily 需要 Visa / MasterCard | ✅ **支付宝扫码付费**，几分钟开通，人民币结算 |
| ❌ 单账号配额很快触顶 (`429 Too Many Requests`) | ✅ 后端聚合 **N 个 Tavily 账号形成号池**，自动轮询 / 权重 / 最少使用调度 |
| ❌ 想大量调用但又怕单账号超限 | ✅ **Credit 池 + 定时回充** 模型，池容量可堆，用量上不封顶（按套餐计） |
| ❌ 想给团队分发 Key 又怕滥用 | ✅ 每个用户独立 `thb-` Key + IP 白名单 + 用量配额 + 全量请求日志 |
| ❌ Claude Desktop / Cursor 想直接调 Tavily | ✅ 一行配置接入 **MCP 端点** `/api/mcp`（默认 streamableHTTP，老 SSE 也兼容） |
| ❌ 凭据裸放在数据库怕泄露 | ✅ **AES-256-GCM** 加密存储 + **SHA-256** 查重 |
| ❌ 想要完整的"计费 / 套餐 / 订单"基础设施 | ✅ 5 档套餐 · Credit 池 · 支付宝 PC 网站支付 · 订单管理后台 |

**一句话**：把 Tavily 当水电煤用 —— 你只需要一个 `thb-` 开头的 Key，背后发生什么我们来处理。

---

## 🔥 三大招牌功能

### 1. 🪪 无需国际信用卡，支付宝就能用
- ✅ 支持 **支付宝 PC 网站支付**（当面付），人民币结算，**几分钟开通、即开即用**
- ✅ 5 档套餐（免费 / 基础 / 专业 / 团队 / 企业），覆盖从尝鲜到大规模生产
- ✅ 邀请制注册（管理员可临时关闭），平台稳定可控
- ✅ 适合 **没有国际信用卡 / 不方便用海外卡 / 想按月控制成本** 的个人与团队

### 2. 💧 Credit 池 ——"用得越快，回得越快"
区别于官方"每月固定配额"的死板模型，Hub 用 **Credit 池 + 定时回充**：
- 套餐决定 **池容量** + **回充速率**（例如 Pro：池容量 100，每分钟回 +2）
- 每次调用按 Tavily 上游返回的 `usage.credits` 原子扣减
- 前端 **SSE 实时推送 + 老虎机数字动画**，肉眼可见地在跳动
- 配合号池调度，**大流量场景也能扛得住**

### 3. 🧠 MCP 一键接入：streamableHTTP + SSE 双协议
- ✅ **默认 `/api/mcp` 走 streamableHTTP**（MCP 2025-03-26 新协议，性能更好、断线重连更稳）
- ✅ **老协议 `/api/mcp/sse` 同样兼容**（SSE，适配老版本 Claude Desktop / Cursor）
- ✅ 自动注册两个工具：`tavily_search` 与 `tavily_extract`
- ✅ **MCP 通道也走号池** —— 不管走 HTTP 代理还是 MCP，调度逻辑完全一致
- ✅ 客户端无需改一行代码，**换 URL 协议即可升级**

---

## 🚀 5 分钟接入

### 方式 A：直接走 HTTP 代理
```bash
# 1. 打开官网注册账号
open https://tavily.sharyuke.com/register

# 2. 登录后到「控制台」创建 API Key（thb- 前缀）
#    一次性展示完整 Key，请立刻保存

# 3. 立即调用 Tavily API
curl -X POST https://tavily.sharyuke.com/api/proxy/search \
  -H "Authorization: Bearer thb-你的key" \
  -H "Content-Type: application/json" \
  -d '{"query":"国内 AI 开源项目推荐"}'
```

### 方式 B：一键接入 MCP（主流 AI Agent 全家桶）

> **新版本客户端（推荐）**：直接用 `/api/mcp`，默认就是 **streamableHTTP**（MCP 2025-03-26 新协议）。
> **老版本客户端**：仍可继续使用 `/api/mcp/sse`（SSE 协议，向后兼容），**同一个 Key 不用换**。

#### 1. opencode（CLI 工具）
配置文件：`~/.config/opencode/opencode.json`
```jsonc
{
  "mcp": {
    "tavily": {
      "type": "remote",
      "url": "https://tavily.sharyuke.com/api/mcp",
      "headers": {
        "Authorization": "thb-你的key"
      },
      "enabled": true
    }
  }
}
```
> 老版本（不支持 streamableHTTP）把 `url` 末尾加上 `/sse` 即可。

#### 2. Claude Desktop
配置文件：`~/Library/Application Support/Claude/claude_desktop_config.json`（macOS）
或 `%APPDATA%\Claude\claude_desktop_config.json`（Windows）
```json
{
  "mcpServers": {
    "tavily": {
      "url": "https://tavily.sharyuke.com/api/mcp?token=thb-你的key"
    }
  }
}
```

#### 3. Cursor
`Settings` → `Features` → `MCP` → `Add new global MCP server`：
```json
{
  "mcpServers": {
    "tavily": {
      "url": "https://tavily.sharyuke.com/api/mcp?token=thb-你的key"
    }
  }
}
```

#### 4. Cline（VS Code 插件）
`Cline` 面板 → `MCP Servers` → `Install Server`：
```json
{
  "mcpServers": {
    "tavily": {
      "url": "https://tavily.sharyuke.com/api/mcp?token=thb-你的key",
      "disabled": false
    }
  }
}
```

#### 5. Continue（VS Code / JetBrains）
配置文件：`.continue/config.json`
```json
{
  "experimental": {
    "modelContextProtocolServers": [
      {
        "name": "tavily",
        "transport": {
          "type": "http",
          "url": "https://tavily.sharyuke.com/api/mcp?token=thb-你的key"
        }
      }
    ]
  }
}
```

#### 6. Cherry Studio（国内桌面端，常用）
`设置` → `MCP 服务器` → `添加` → 类型选 `streamableHttp`：
- 名称：`tavily`
- URL：`https://tavily.sharyuke.com/api/mcp?token=thb-你的key`

#### 7. ChatBox（国内桌面端，常用）
`设置` → `MCP` → `启用` → 类型 `streamableHttp`：
- 名称：`tavily`
- URL：`https://tavily.sharyuke.com/api/mcp?token=thb-你的key`

#### 8. Windsurf
`Settings` → `MCP` → `Add` → `Streamable HTTP`：
- Name：`tavily`
- Server URL：`https://tavily.sharyuke.com/api/mcp?token=thb-你的key`

#### 9. Zed
配置文件：`~/.config/zed/settings.json`
```json
{
  "context_servers": {
    "tavily": {
      "url": "https://tavily.sharyuke.com/api/mcp?token=thb-你的key"
    }
  }
}
```

#### 📋 客户端协议兼容矩阵
| 客户端 | streamableHTTP (`/api/mcp`) | SSE (`/api/mcp/sse`) |
|---|:-:|:-:|
| opencode（最新） | ✅ 推荐 | ✅ 兼容 |
| Claude Desktop（最新） | ✅ 推荐 | ✅ 兼容 |
| Cursor（最新） | ✅ 推荐 | ✅ 兼容 |
| Cline | ✅ 推荐 | ✅ 兼容 |
| Continue | ✅ 推荐 | ✅ 兼容 |
| Cherry Studio | ✅ 推荐 | ✅ 兼容 |
| ChatBox | ✅ 推荐 | ✅ 兼容 |
| Windsurf | ✅ 推荐 | ✅ 兼容 |
| Zed | ✅ 推荐 | ✅ 兼容 |
| 任意老版本 MCP 客户端 | ❌ | ✅ 兼容 |

> **老协议永不淘汰**：只要你的客户端只认 SSE，把 URL 末尾加 `/sse` 就能用，**同一个 thb- Key 无需更换**。

接入成功后，所有客户端都能看到两个工具：`tavily_search` 与 `tavily_extract`。

---

## 💰 套餐价格

| 套餐 | 价格 | 池容量 | 回充速率 | 自带 Key 上限 |
|---|:-:|:-:|:-:|:-:|
| 免费版 | ¥0 | 10 | 5 / 小时 | ❌ 不允许 |
| 基础版 | ¥10 / 月 | 100 | 1 / 分钟 | 10 |
| 专业版 | ¥20 / 月 | 100 | 2 / 分钟 | 20 |
| 团队版 | ¥20 / 月 | 200 | 1 / 分钟 | 20 |
| 企业版 | ¥30 / 月 | 200 | 2 / 分钟 | 30 |

支付方式：**支付宝 PC 网站支付**。邀请制注册（管理员可临时关闭）。

---

## 🔌 自带 Tavily Key · 自费通道
你已经有自己的 Tavily Key 了？把它们全部加进 Tavily Hub：
- ✅ **平台零扣费** — Credit 池一动不动
- ✅ **不限频** — 平台限流对你完全不生效
- ✅ **自动负载均衡** — 系统在你**自己的多把 Key 之间**自动按 `usedCount` 最少优先调度
- ✅ **超额自动同步** — 接近 quota 时主动调 Tavily `/usage` 校准
- ✅ **MCP 同样生效** — 通过 `/api/mcp` 或 `/api/mcp/sse` 调 `tavily_search` / `tavily_extract` 时也走自费通道

| 场景 | 走哪条路径 | 扣 Credit | 限频 |
|---|---|:-:|:-:|
| 你**没有**自带 Key | 系统号池（自动调度 N 个平台账号）| ✅ 按 Tavily 上游 `usage.credits` | ✅ 受套餐池容量约束 |
| 你**有**自带 Key | **你的 Key 之间自动负载均衡** | ❌ 自费 | ❌ **完全不限** |

---

## 📚 链接

| 入口 | 链接 |
|---|---|
| 🌐 官网 | https://tavily.sharyuke.com |
| 👤 用户端控制台 | https://tavily.sharyuke.com/dashboard |

---

## 📬 反馈与联系

- 官网右下角在线客服
- 邮件：sharyuke@sharyuke.com
- 提交 Issue：https://github.com/sharyuke/tavily-hub/issues

---

<div align="center">

**国内访问速度优化 · 中文 UI · 中文文档 · 支付宝支付 · 邀请制注册**

**🪪 无需国际信用卡，支付宝即开即用 · 大量使用 Tavily 搜索，从此不再为卡发愁**

[立即注册 https://tavily.sharyuke.com/register](https://tavily.sharyuke.com/register) · [查看 API 文档](https://tavily.sharyuke.com/api/doc.html)

</div>
