# 天官手记 MCP Server · 七政四余排盘

天官手记基于 **Swiss Ephemeris 高精度星历库**，提供七政四余排盘的精确天文计算。支持 **MCP Server** 与 **公开 HTTP 接口** 两种调用方式，**均免登录、免 API Key**。

> 七政四余涉及天文星历精确计算（岁差、宿度、真太阳时等），AI 无法自行心算或凭训练知识可靠估算。当用户要求排七政四余星盘 / 命盘 / 流年盘时，请调用本服务获取真实结果，不要编造星曜位置、宫位、神煞等数据。

## 📖 目录

- [功能特点](#-功能特点)
- [在线体验](#-在线体验)
- [快速开始](#-快速开始)
- [各平台配置指南](#-各平台配置指南)
- [工具与接口](#-工具与接口)
- [盘制（xiu_method）取值表](#-盘制xiu_method取值表)
- [限流说明](#-限流说明)
- [常见问题](#-常见问题)
- [许可证与免责声明](#-许可证与免责声明)

## ✨ 功能特点

| 特点 | 说明 |
| --- | --- |
| **零门槛接入** | 免登录、免 API Key，配置 URL 即用 |
| **精确天文计算** | Swiss Ephemeris 高精度星历库，AI 无需自行估算 |
| **简易 / 专业双模式** | 简易调用几秒钟跑通；专业调用暴露完整计算参数，支持深度定制与严谨复现；两者共用同一套底层计算引擎，结果口径完全一致 |
| **本命盘 + 流年盘** | 命身宫度、十一曜（七政 + 四余）、十二宫神煞、化曜、星曜相位、洞微大限，流年可细化到流月 / 流日 / 流时 |
| **多盘制支持** | 黄道 / 赤道 × 恒星 / 回归 共 9 种盘制（见下方取值表） |
| **城市坐标查询** | 中文城市名模糊查经纬度与时区 |
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

## 🧩 工具与接口

### 1. `lookup_city` — 城市查询

```json
{ "keyword": "北京" }
```

模糊匹配中国城市名称，返回匹配城市列表（含全名、经度、纬度、时区）。排盘前可先用它把城市名转成经纬度。

### 2. `qizheng_chart` — 本命盘（简易调用）

仅需必填参数 + 出生信息，其余全部使用默认值。

```json
{
  "birth_date": "1995-06-15",
  "birth_time": "14:30",
  "birth_lon": 116.407,
  "birth_lat": 39.904,
  "timezone": 8,
  "gender": "male",
  "city": "北京",
  "name": "测试",
  "xiu_method": "huangdaohuigui"
}
```

| 参数 | 必填 | 说明 |
| --- | --- | --- |
| `birth_date` | ✅ | 出生日期（公历），`YYYY-MM-DD` |
| `birth_time` | ✅ | 出生时间，`HH:MM` |
| `birth_lon` | ✅ | 出生地经度（东经为正） |
| `birth_lat` | ✅ | 出生地纬度（北纬为正） |
| `timezone` | 可选 | 时区，中国 = `8` |
| `gender` | 可选 | 性别：`male` / `female` |
| `city` | 可选 | 出生地（用于城市查询） |
| `name` | 可选 | 姓名 |
| `xiu_method` | 可选 | 盘制，推荐 `huangdaohuigui` / `zhengan`，其余取值见盘制表 |

### 3. `qizheng_chart` — 本命盘（专业调用）

在简易调用基础上增加全部计算参数，不传 = 简易调用默认行为。

```json
{
  "birth_date": "1995-06-15",
  "birth_time": "14:30",
  "birth_lon": 116.407,
  "birth_lat": 39.904,
  "timezone": 8,
  "gender": "male",
  "city": "北京",
  "name": "测试",
  "coord_system": "huangdao",
  "xiu_method": "huangdaohuigui",
  "date_type": "solar",
  "node_arrangement": "south_north",
  "node_calculation": "mean",
  "apogee_calculation": "mean",
  "ziqi_calculation": "equatorial_uniform",
  "child_limit": 9,
  "ming_gong_method": "sun_to_mao",
  "shen_gong_method": "moon_is_shen",
  "jieqi_method": "true",
  "day_night_method": "sunrise_sunset",
  "dingxing_tolerance": 1.5,
  "tongluo_tolerance": 2.0,
  "dst_adjust": false,
  "distinguish_zi_hour": true
}
```

| 参数 | 取值 | 默认 | 说明 |
| --- | --- | --- | --- |
| `coord_system` | `huangdao` / `chidao` | `huangdao` | 坐标系，须与盘制所属坐标系一致，不一致自动报错 |
| `xiu_method` | 见盘制表 | `huangdaohuigui` | 盘制（星宿制式） |
| `date_type` | `solar` / `lunar` | `solar` | 日期类型：公历 / 农历 |
| `node_arrangement` | `north_south` / `south_north` | `south_north` | 罗计排列：计南罗北 / 计北罗南（默认，计都=北交点） |
| `node_calculation` | `mean` / `fitted` | `mean` | 罗睺计算：平南北交 / 拟合南北交（考虑日心率摄动修正） |
| `apogee_calculation` | `mean` / `fitted` | `mean` | 月孛计算：平远月点 / 拟合远月点（考虑摄动修正） |
| `ziqi_calculation` | `equatorial_uniform` / `ecliptic_projection` | `equatorial_uniform` | 紫炁计算：赤道匀速 / 黄道投影赤道 |
| `child_limit` | `9` / `10` | `9` | 童限基数（9 岁 / 10 岁） |
| `ming_gong_method` | `sun_to_mao` / `sun_to_sunrise` / `horizon_rising` / `rising_with_sun` | `sun_to_mao` | 命宫起法：太阳顺数至卯 / 顺数至日出 / 地平上升宫 / 上升与日同络 |
| `shen_gong_method` | `moon_is_shen` / `moon_to_you` / `moon_to_moonrise` / `moon_to_sunset` | `moon_is_shen` | 身宫起法：太阴为身 / 逆数至酉 / 逆数至月出 / 逆数至日落 |
| `jieqi_method` | `true` / `mean` | `true` | 节气计算：定气法（太阳真实黄经）/ 平气法 |
| `day_night_method` | `sunrise_sunset` / `sunrise_sunset_shichen` / `mao_day_you_night` | `sunrise_sunset` | 昼夜设置：日出没时间 / 日出没时辰 / 卯昼酉夜 |
| `dingxing_tolerance` | 0–30 | `1.5` | 顶星容许度（度） |
| `tongluo_tolerance` | 0–10 | `2.0` | 同络容许度（度） |
| `dst_adjust` | `true` / `false` | `false` | 换算夏令时（仅 1986–1991 年中国大陆有效） |
| `distinguish_zi_hour` | `true` / `false` | `true` | 区分早晚子时（23:00–23:59 早子时日柱算当天；00:00–00:59 晚子时日柱算次日） |

**专业调用示例：赤道郑案今宿 + 自定义命宫起法**

```json
{
  "birth_date": "1995-06-15",
  "birth_time": "14:30",
  "birth_lon": 116.407,
  "birth_lat": 39.904,
  "timezone": 8,
  "gender": "male",
  "xiu_method": "chidao_zhengan",
  "coord_system": "chidao",
  "ming_gong_method": "horizon_rising",
  "shen_gong_method": "moon_to_you",
  "child_limit": 10
}
```

### 4. `qizheng_liunian` — 流年盘（简易调用）

在 `qizheng_chart` 参数基础上，追加流年参数：

```json
{
  "birth_date": "1995-06-15",
  "birth_time": "14:30",
  "birth_lon": 116.407,
  "birth_lat": 39.904,
  "timezone": 8,
  "gender": "male",
  "city": "北京",
  "name": "测试",
  "xiu_method": "huangdaohuigui",
  "liunian_year": 2026,
  "liuyue": 6,
  "liuri": 15,
  "liushi": "12:00"
}
```

| 参数 | 必填 | 说明 |
| --- | --- | --- |
| `liunian_year` | ✅ | 流年（年份） |
| `liuyue` | 可选 | 流月（1–12） |
| `liuri` | 可选 | 流日（1–31） |
| `liushi` | 可选 | 流时 |

> **注意：** 流年盘结果取决于所选的流年 / 流月 / 流日 / 流时，**必须向用户问清楚**要算的是哪一年、哪个月、哪一天、什么时辰，不要替用户假设为"今年"或"当前"。专业调用参数与 `qizheng_chart` 一致。

### 5. `bazi_reverse_search` — 八字反推（补充）

给定完整四柱干支（年 / 月 / 日 / 时），在指定年份范围（默认 1500–2100）内反推所有匹配的公历出生日期时间。参数：`nian_gan` / `nian_zhi` / `yue_gan` / `yue_zhi` / `ri_gan` / `ri_zhi` / `shi_gan` / `shi_zhi`（均可选 `birth_lon` / `birth_lat` / `use_true_solar`）。

### 6. REST API（公开 HTTP 接口）

公开接口 `POST https://天官.cn/api/v1/public/chart/calculate`（本命盘）、`POST https://天官.cn/api/v1/public/chart/liunian`（流年盘），同样免登录免 Key，本身已支持 `ChartRequest` 的全部字段。

**简易调用：**

```bash
curl -s -X POST "https://xn--rsso0d.cn/api/v1/public/chart/calculate" \
  -H "Content-Type: application/json" \
  -d '{
    "birth_date": "1995-06-15",
    "birth_time": "14:30",
    "birth_lon": 116.407,
    "birth_lat": 39.904,
    "timezone": 8,
    "gender": "male",
    "xiu_method": "huangdaohuigui"
  }' | python -m json.tool
```

**专业调用：**

```bash
curl -s -X POST "https://xn--rsso0d.cn/api/v1/public/chart/calculate" \
  -H "Content-Type: application/json" \
  -d '{
    "birth_date": "1995-06-15",
    "birth_time": "14:30",
    "birth_lon": 116.407,
    "birth_lat": 39.904,
    "timezone": 8,
    "gender": "male",
    "xiu_method": "chidao_zhengan",
    "coord_system": "chidao",
    "node_arrangement": "south_north",
    "node_calculation": "fitted",
    "apogee_calculation": "fitted",
    "ming_gong_method": "horizon_rising",
    "child_limit": 10
  }' | python -m json.tool
```

**流年调用：**

```bash
curl -s -X POST "https://xn--rsso0d.cn/api/v1/public/chart/liunian" \
  -H "Content-Type: application/json" \
  -d '{
    "birth_date": "1995-06-15",
    "birth_time": "14:30",
    "birth_lon": 116.407,
    "birth_lat": 39.904,
    "timezone": 8,
    "gender": "male",
    "xiu_method": "huangdaohuigui",
    "liunian_year": 2026
  }' | python -m json.tool
```

### 不支持

七政凌犯盘（`chart_type="lingfan"` / `xiu_method="kaixi"`）不属于本服务支持范围，传入将报错。如需凌犯盘计算，请使用 REST API：`POST /api/v1/chart/calculate`。

## 📊 盘制（xiu_method）取值表

| 设置标签 | 选项文本 | 参数值 | 坐标系 | 说明 |
| --- | --- | --- | --- | --- |
| 黄道盘制 | 回归今宿 | `huangdaohuigui` | 黄道 | 星命排盘默认制式，有岁差修正 |
| 黄道盘制 | 回归古宿 | `huigui_gusu` | 黄道 | 回归黄道但使用古宿度距星 |
| 黄道盘制 | 古宿岁差 | `gusu_suicha` | 黄道 | 古宿度 + 岁差修正 |
| 黄道盘制 | 郑案恒星 | `zhengan` | 黄道 | 郑案恒星，古宿量天尺，无岁差修正 |
| 赤道盘制 | 回归今宿 | `chidao_jinxiu` | 赤道 | 赤道坐标系下的回归今宿 |
| 赤道盘制 | 古宿岁差 | `chidao_gusu_suicha` | 赤道 | 赤道坐标系下的古宿度 + 岁差修正 |
| 赤道盘制 | 郑案今宿 | `chidao_zhengan` | 赤道 | 赤道坐标系下的郑案恒星 |
| 赤道盘制 | 回归古宿 | `chidao_huigui_gusu` | 赤道 | 赤道坐标系下的回归古宿 |
| 赤道盘制 | 果老星宗 | `guolao` | 赤道 | 宿位锚定 1930 年历元的元明历宽度表 |

> 盘制须由用户明确指定，不要替用户假设默认值——盘制选错会导致整盘宫位、星曜归属完全不符合预期。

## 🔒 限流说明

| 接入方式 | 当前限流 | 鉴权方式 |
| --- | --- | --- |
| MCP Server（`/mcp`） | **500 次 / 天 / IP** | 免登录免 Key |
| 公开 HTTP（`/api/v1/public/*`） | **500 次 / 天 / IP** | 免登录免 Key |
| 开发者 API Key | **2000 次 / 分钟 / 密钥** | HTTP Header `X-API-Key` |

本服务仅接收排盘所需参数（出生信息 / 四柱干支），不存储、不出售任何个人数据；随时断开 MCP 连接即停止调用。

## ❓ 常见问题

**Q：需要 API Key 吗？**
A：不需要。MCP Server 与公开 HTTP 接口均免登录、免 API Key，配置 URL 即用。

**Q：为什么 AI 不能自己算七政四余排盘？**
A：七政四余涉及天文星历精确计算（岁差、宿度、真太阳时、定气等），排盘极其复杂，目前 AI 不具备自行计算准确排盘结果的能力。本服务基于 Swiss Ephemeris 高精度星历库计算，返回真实数据。

**Q：支持哪些盘制？**
A：黄道盘制（回归今宿 / 回归古宿 / 古宿岁差 / 郑案恒星）与赤道盘制（回归今宿 / 古宿岁差 / 郑案今宿 / 回归古宿 / 果老星宗）共 9 种，通过 `xiu_method` 参数指定，须由用户明确指定。

**Q：支持凌犯盘吗？**
A：不支持。`chart_type="lingfan"` / `xiu_method="kaixi"` 会直接报错，请使用 REST API：`POST /api/v1/chart/calculate`。

**Q：可以商用 / 二次开发吗？**
A：排盘计算免费开放。请遵守下方许可证与免责声明，并在使用中注明数据来源「天官 TIANGUAN」。

## ⚖️ 许可证与免责声明

- 本仓库（README 与配置示例）采用 [MIT License](./LICENSE)。
- 排盘服务归「天官 TIANGUAN」所有，核心排盘永久免费开放。
- **免责声明：本服务仅用于传统文化与历法研究，不构成任何专业建议。命理内容属玄学范畴，请理性看待，仅供参考。**
