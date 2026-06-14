# Clash Rules — 极简网络分流规则集

三套分平台配置文件，覆盖 **Clash Meta / Clash Verge / OpenClash**、**Shadowrocket (iOS)**、**Stash (iOS/macOS/tvOS)**。

## 洋葱过滤模型

```
第 1 层  局域网            → DIRECT
第 2 层  广告              → REJECT
第 3 层  银行与券商         → 香港/美国节点
第 4 层  加密货币交易所     → 专项组
第 5 层  券商内联域         → 香港节点
第 6 层  AI 服务            → 综合代理组
第 7 层  泛加密货币         → 综合代理组
第 8 层  社媒/流媒体        → 专项组
第 9 层  开发者/游戏        → 专项组
         Apple             → DIRECT
         国内域名            → DIRECT
         GFW/代理列表       → Default
         中国 IP            → DIRECT
         MATCH             → Default
```

## 三平台对比

| 特性 | Clash Meta | Shadowrocket | Stash |
|------|:----------:|:------------:|:-----:|
| **语法** | YAML | INI | YAML |
| **代理订阅** | `proxy-providers` | iOS App 内置管理 | 同 Clash |
| **节点过滤** | `filter:` 正则 | `policy-regex-filter` | 同 Clash |
| **TUN / 系统代理** | TUN + mixed-port | bypass-system + ipv6 | Stash App 管理 |
| **DNS Fake-IP** | 完整 DNS 块 | — | 精简 DNS |
| **域名嗅探** | Sniffer 完整配置 | — | — |
| **YAML 锚点** | 支持 | — | 不支持（已展开） |

## 仓库结构

```
.
├── README.md
├── config/
│   ├── clash.yaml        # Clash Meta / Verge / OpenClash
│   ├── shadowrocket.conf # Shadowrocket (iOS)
│   └── stash.yaml        # Stash (iOS / macOS)
├── rules/                # 自托管规则集（20+ 分类）
│   ├── HSBC/HSBC.yaml
│   ├── IBKR/IBKR.yaml
│   ├── Gemini/Gemini.yaml
│   ├── ChatGPT/ChatGPT.yaml
│   └── ...
├── icons/                # 分组图标（自托管）
│   ├── HSBC_HK.png
│   ├── DeepWep.png
│   ├── Gemini.png
│   └── ...
```

### 规则命名约定

| 前缀 | 来源 |
|------|------|
| `ys_` | 自托管（本仓库 `rules/`） |
| `ls_` | Loyalsoldier/clash-rules |
| `bm_` | blackmatrix7/ios_rule_script |

## 策略组

### 入口
- **Default** — 兜底出口（DIRECT + 全部地理组 + DeepWep）

### AI 服务
- ChatGPT → Gemini

### 社交媒体与流媒体
- Instagram → YouTube → Google → X → TikTok → NETFLIX → Telegram

### 银行与券商
- HSBC Hong Kong → Interactive Brokers → Longbridge → uSmart → Zinvest → Phillip → Zircon

### 加密货币
- Crypto → Kraken → Coinbase → OSL

### 开发与游戏
- GitHub → Steam → XBOX → Microsoft

### 地理节点池
- Hong Kong → Taiwan → Japan → Korea → Singapore → US → DeepWep

## 快速开始

### Clash Meta / Verge / OpenClash

1. 复制 `config/clash.yaml` 内容粘贴到配置编辑器
2. 将 `YOUR_SUBSCRIPTION_URL` 替换为你的节点订阅链接（`proxy-providers > sushi_cloud > url`）
3. 重载配置

### Shadowrocket (iOS)

1. 复制 `config/shadowrocket.conf` 内容
2. 在 Shadowrocket 中创建新配置并粘贴
3. 在 App 内 `Settings → Subscriptions` 导入节点订阅
4. `policy-regex-filter` 自动根据节点名正则匹配到对应策略组

### Stash (iOS / macOS)

1. 复制 `config/stash.yaml` 内容粘贴到配置编辑器
2. 替换 `YOUR_SUBSCRIPTION_URL` 为你的订阅链接
3. 重载配置

## 隐私拦截

| 域名 | 处置 | 用途 |
|------|:----:|------|
| `ut.taobao.com` | REJECT | 阿里 UT 用户行为埋点 SDK |
| `bugly.qq.com` | REJECT | 腾讯 Bugly 崩溃上报 SDK |

## 图标引用

```yaml
图标: "https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@main/icons/<Name>.png"
规则: "https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@main/rules/<Name>/<Name>.yaml"
```

---

> 混沌的流量终被驯服于精密的缩进与冰冷的过滤模型之下。
