<div align="center">

# 🌐 Tavily Hub

**1 个 Hub Key，背后 N 个 Tavily 账号。用得越快，回得越快。**

为国内 AI 开发者打造的 **Tavily 统一代理网关 + Credit 池 + MCP 一键接入**

👉 **官网：[https://tavily.sharyuke.com](https://tavily.sharyuke.com/)**

</div>

---

## 🎯 Tavily Hub 是什么？

[Tavily](https://tavily.com/) 是当下 AI / RAG / Agent 最常用的搜索 API，但直接用官方账号会遇到一堆坑：

| 痛点 | Tavily Hub 的解法 |
|---|---|
| 单账号配额很快就触顶 | 多 Tavily 账号聚合到"号池"，自动负载均衡（轮询 / 权重 / 最少使用）|
| 想给团队分发 Key 又怕滥用 | 每个用户独立 `thb-` Key + IP 白名单 + 用量配额 + 全量请求日志 |
| Claude Desktop / Cursor 想直接调 Tavily | 一键暴露 **MCP 端点**（`/mcp/sse`），自动注册 `tavily_search` 与 `tavily_extract` 工具 |
| 凭据裸放在数据库怕泄露 | **AES-256-GCM** 加密存储 + **SHA-256** 查重 |
| 踩到限流 (`429`) | 自动切下一个号池账号，业务完全无感 |
| 没"计费 / 套餐 / 支付"基础设施 | 5 档套餐 · Credit 池 · 支付宝 PC 网站支付 · 订单管理 |

**一句话**：把 Tavily 当水电煤用 — 你只需要一个 `thb-` 开头的 Key，背后发生什么我们来处理。

---

## 🔑 两大招牌功能

### 1. Credit 池 —— "用得越快，回得越快"
区别于官方"每月固定配额"的死板模型，Hub 用 **Credit 池 + 定时回充**：
- 套餐决定 **池容量** + **回充速率**（例如 Pro：池容量 100，每分钟回 +2）
- 每次调用按 Tavily 返回的 `usage.credits` 原子扣减
- 前端 **SSE 实时推送 + 老虎机数字动画**，肉眼可见地在跳动

### 2. 自带 Tavily Key · 自费通道
你已经有自己的 Tavily Key 了？把它们全部加进 Tavily Hub：
- ✅ **平台零扣费** — Credit 池一动不动
- ✅ **不限频** — 平台限流对你完全不生效
- ✅ **自动负载均衡** — 系统在你**自己的多把 Key 之间**自动按 `usedCount` 最少优先调度
- ✅ **超额自动同步** — 接近 quota 时主动调 Tavily `/usage` 校准
- ✅ **MCP 同样生效** — 通过 `/mcp/sse` 调 `tavily_search` / `tavily_extract` 时也走自费通道

| 场景 | 走哪条路径 | 扣 Credit | 限频 |
|---|---|:-:|:-:|
| 你**没有**自带 Key | 系统号池（自动调度 N 个平台账号）| ✅ 按 Tavily 上游 `usage.credits` | ✅ 受套餐池容量约束 |
| 你**有**自带 Key | **你的 Key 之间自动负载均衡** | ❌ 自费 | ❌ **完全不限** |

---

## 🚀 5 分钟接入

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

### 一键接入 MCP（Claude Desktop / Cursor）
在你的 MCP 配置文件（如 `claude_desktop_config.json` 或 Cursor 的 MCP 设置）里加上：

```json
{
  "mcpServers": {
    "tavily": {
      "url": "https://tavily.sharyuke.com/api/mcp/sse?token=thb-你的key"
    }
  }
}
```

重启 Claude / Cursor，即可看到两个工具：`tavily_search` 与 `tavily_extract`。

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

## 🛠 技术栈速览

- **后端**：Spring Boot 3.3 + Java 17 + MyBatis Plus 3.5 + Sa-Token 1.39（JWT 模式）
- **前端**：Vue 3.5 + TypeScript 5.7 + Naive UI 2.41 + Vite 6
- **数据库**：MySQL 8.0 + Redis 7
- **API 文档**：Knife4j 4.5（OpenAPI 3）
- **特色**：SSE 实时推送 + 老虎机数字动画 / 玻璃拟态 UI

完整 14 个后端模块、12 张数据表、3 套负载均衡策略、套餐 + 邀请 + 支付 + 审计 + 告警全栈自包含。

---

## 📚 链接

| 入口 | 链接 |
|---|---|
| 🌐 官网 | https://tavily.sharyuke.com |
| 📖 在线 API 文档 | https://tavily.sharyuke.com/api/doc.html |
| 👤 用户端控制台 | https://tavily.sharyuke.com/dashboard |

---

## 📬 反馈与联系

- 官网右下角在线客服
- 邮件：admin@tavily-hub.com（占位，请通过官网联系）
- 提交 Issue：https://github.com/sharyuke/tavily-hub/issues

---

<div align="center">

**国内访问速度优化 · 中文 UI · 中文文档 · 支付宝支付 · 邀请制注册**

[立即注册 https://tavily.sharyuke.com/register](https://tavily.sharyuke.com/register) · [查看 API 文档](https://tavily.sharyuke.com/api/doc.html)

</div>