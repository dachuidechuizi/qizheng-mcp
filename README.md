# 天官手记 MCP Server · 七政四余排盘

为 AI 助手提供七政四余（中国传统星命学）的精确天文排盘能力。基于 Swiss Ephemeris 高精度星历库，AI 只需在对话中调用本服务工具，即可获得真实排盘结果——无需心算、不会编造。

> 七政四余涉及天文星历精确计算（岁差、宿度、真太阳时等），AI 无法自行心算或凭训练知识可靠估算。请务必调用本服务获取真实数据。

## 📖 目录

- [功能特点](#-功能特点)
- [在线体验](#-在线体验)
- [快速开始](#-快速开始)
- [各平台配置指南](#-各平台配置指南)
- [工具列表](#-工具列表)
- [使用示例](#-使用示例)
- [输出示例](#-输出示例)
- [数据安全与限流](#-数据安全与限流)
- [常见问题](#-常见问题)
- [许可证与免责声明](#-许可证与免责声明)

## ✨ 功能特点

| 特点 | 说明 |
| --- | --- |
| **零门槛接入** | 免登录、免 API Key，配置 URL 即用 |
| **精确天文计算** | Swiss Ephemeris 高精度星历，AI 无需自行估算 |
| **完整本命盘** | 命身宫度、十一曜（七政 + 四余）、十二宫神煞、化曜、星曜相位、洞微大限 |
| **流年推运** | 流年 / 流月 / 流日 / 流时四层推运 |
| **八字反推** | 由四柱干支反推可能的公历出生日期时间（1500–2100 年） |
| **城市坐标查询** | 中文城市名模糊查经纬度与时区，排盘不再卡在坐标 |
| **远程计算** | 排盘算法在云端服务器运行，用户端零安装、零依赖 |
| **多端配套** | Web / Windows / Android / macOS / 鸿蒙 客户端与官网同源 |

## 🌐 在线体验

- 官网：[https://天官.cn](https://天官.cn)（punycode：`https://xn--rsso0d.cn`）
- 免费在线排盘：[https://天官.cn/paipan/](https://天官.cn/paipan/)
- 接口文档：[https://天官.cn/panel/mcp.html](https://天官.cn/panel/mcp.html)

## 🚀 快速开始

### 前置要求

- 支持 MCP（Model Context Protocol）的 AI 客户端：Claude Desktop、Claude Code、Cline（VS Code）、WorkBuddy、Cherry Studio 等
- 网络可访问 `https://天官.cn/mcp/`

### 添加 MCP 服务器（唯一一步）

MCP 端点（Streamable HTTP 协议）：

```
https://天官.cn/mcp/
```

**无需注册、无需 API Key，添加即用。**

## 🛠️ 各平台配置指南

### Claude Desktop

编辑 `claude_desktop_config.json`（菜单 Claude → Settings → Developer → Edit Config）：

```json
{
  "mcpServers": {
    "tianguan": {
      "url": "https://天官.cn/mcp/"
    }
  }
}
```

### Claude Code（CLI）

```bash
claude mcp add --transport http tianguan https://天官.cn/mcp/
```

### Cline（VS Code 插件）

设置 → Cline → MCP Servers → 添加服务器：

- 类型：`HTTP`
- 名称：`tianguan`
- URL：`https://天官.cn/mcp/`

### 其他 MCP 客户端（Cherry Studio / ChatWise / WorkBuddy 等）

在客户端中选择「远程 MCP / HTTP MCP」类型，名称任意，URL 填 `https://天官.cn/mcp/` 即可。

## 🧩 工具列表

| 工具 | 说明 | 必填参数 |
| --- | --- | --- |
| `lookup_city` | 中国城市经纬度与时区查询 | `keyword` |
| `qizheng_chart` | 七政四余本命盘 | `birth_date`, `birth_time`, `birth_lon`, `birth_lat` |
| `qizheng_liunian` | 七政四余流年盘 | 上述 + `liunian_year` |
| `bazi_reverse_search` | 八字反推出生日期 | 年 / 月 / 日 / 时四柱干支 |

### 1. `lookup_city` — 城市坐标查询

模糊查询中国城市，返回匹配列表（含全名、经度、纬度、时区）。排盘前可先用它把城市名转成经纬度。

| 参数 | 必填 | 说明 |
| --- | --- | --- |
| `keyword` | ✅ | 城市名称关键字，如 `北京`、`朝阳`、`长沙` |

### 2. `qizheng_chart` — 本命盘

计算七政四余本命盘，是整个推算的运算基础。

**必填参数：**

| 参数 | 说明 | 示例 |
| --- | --- | --- |
| `birth_date` | 出生日期 | `1988-10-29` |
| `birth_time` | 出生时间 | `02:30` |
| `birth_lon` | 经度（东经为正） | `116.38` |
| `birth_lat` | 纬度（北纬为正） | `39.92` |

**常用可选参数：**

| 参数 | 默认值 | 说明 |
| --- | --- | --- |
| `timezone` | `8.0` | 时区（东八区） |
| `gender` | `null` | 性别 `男` / `女`，影响大小限计算 |
| `city` | `null` | 出生地名称，仅用于展示 |
| `name` | `null` | 姓名，仅用于展示 |
| `xiu_method` | `huangdaohuigui` | 星宿制式：`huangdaohuigui`（黄道回归今宿，默认）/ `zhengan`（郑案恒星制）等 |
| `chart_type` | `tropical` | 盘制：`tropical`（回归制，默认）/ `sidereal`（恒星制） |

> 盘制选错会导致整盘宫位、星曜归属完全不符合预期。若用户未明确盘制，使用默认值即可；若用户明确要求某种盘制，必须显式传入对应参数。

### 3. `qizheng_liunian` — 流年盘

在 `qizheng_chart` 全部参数基础上，追加：

| 参数 | 必填 | 说明 |
| --- | --- | --- |
| `liunian_year` | ✅ | 流年年份，如 `2026` |
| `liuyue` | 可选 | 流月（1–12），不填则按当前真实月份 |
| `liuri` | 可选 | 流日，不填则按当前真实日期 |
| `liushi` | 可选 | 流时，不填则按当前真实时辰 |

> 排流年盘前请先向用户确认具体看哪一年，不要替用户假设为今年；若省略流月/流日/流时，系统默认使用当前真实日期，仅反映「此刻」这一天。

### 4. `bazi_reverse_search` — 八字反推

给定完整四柱干支（年 / 月 / 日 / 时），在指定年份范围内反推所有匹配的公历出生日期时间。

| 参数 | 必填 | 说明 |
| --- | --- | --- |
| `nian_gan` / `nian_zhi` | ✅ | 年柱干支，如 `甲` / `子` |
| `yue_gan` / `yue_zhi` | ✅ | 月柱干支 |
| `ri_gan` / `ri_zhi` | ✅ | 日柱干支 |
| `shi_gan` / `shi_zhi` | ✅ | 时柱干支 |
| `birth_lon` / `birth_lat` | 可选 | 出生地经纬度（配合真太阳时） |
| `use_true_solar` | `false` | 是否使用真太阳时校正 |
| `start_year` / `end_year` | `1500` / `2100` | 反推年份范围 |

## 💬 使用示例

**用户：** 帮我排一个 1988 年 10 月 29 日凌晨 2:30、出生在北京的七政四余命盘。

**AI 调用链：**

1. `lookup_city(keyword="北京")` → 得到经度 116.40、纬度 39.90、时区 8.0
2. `qizheng_chart(birth_date="1988-10-29", birth_time="02:30", birth_lon=116.40, birth_lat=39.90, timezone=8.0, gender="男")`
3. 基于返回 JSON 输出命盘分析

## 📊 输出示例

`qizheng_chart` 返回结构（字段以实际返回为准）：

```json
{
  "basic": { "birth_date": "1988-10-29", "birth_time": "02:30", "gender": "男", "city": "北京" },
  "bazi": { "ganzhi": "戊辰 壬戌 甲午 乙丑", "wuxing": "..." },
  "liming": { "ming_gong": "...", "shen_gong": "..." },
  "planets": [
    { "name": "日", "palace": 7, "degree": "...", "speed": "..." }
  ],
  "palaces": [ { "index": 0, "name": "命宫", "lord": "...", "stars": ["..."] } ],
  "shensha": { "huayao": "...", "tian_guan": "...", "guo_lao": "..." },
  "dayun": { "...": "..." },
  "shouzhao": { "...": "..." },
  "tongluo": { "...": "..." },
  "tongjing": { "...": "..." }
}
```

## 🔒 数据安全与限流

- 本服务仅接收排盘所需参数（出生信息 / 四柱干支），**不存储、不出售任何个人数据**
- 匿名按 IP 限流：**MCP 通道 500 次 / 天 / IP**（开发者 API Key 通道 2000 次 / 分钟 / 密钥）
- 随时断开 MCP 连接即停止调用，数据即时不可达

## ❓ 常见问题

**Q：需要 API Key 吗？**
A：不需要。MCP 通道免登录、免 API Key，配置 URL 即用。

**Q：为什么 AI 不能自己算七政四余排盘？**
A：七政四余涉及天文星历精确计算（岁差、宿度、真太阳时、定气等），AI 无法心算或凭训练知识可靠估算。本服务基于 Swiss Ephemeris 高精度星历库计算，返回真实数据。

**Q：支持哪些盘制？**
A：黄道盘制（回归今宿 / 回归古宿 / 古宿岁差 / 郑案恒星）与赤道盘制（回归今宿 / 古宿岁差 / 郑案今宿 / 回归古宿 / 果老星宗）两大类，通过 `xiu_method` / `chart_type` 参数指定。

**Q：支持凌犯盘吗？**
A：MCP 工具暂不支持凌犯盘（`kaixi` / `lingfan`）。如需凌犯盘计算，请使用公开 REST API：`POST https://天官.cn/api/v1/chart/calculate`。

**Q：可以商用 / 二次开发吗？**
A：排盘计算免费开放。请遵守下方许可证与免责声明，并在使用中注明数据来源「天官 TIANGUAN」。

## ⚖️ 许可证与免责声明

- 本仓库（README 与配置示例）采用 [MIT License](./LICENSE)。
- 排盘服务归「天官 TIANGUAN」所有，核心排盘永久免费开放。
- **免责声明：本服务仅用于传统文化与历法研究，不构成任何专业建议。命理内容属玄学范畴，请理性看待，仅供参考。**
