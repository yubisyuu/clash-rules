# Clash Rules — 极简网络分流规则集

三套分平台配置文件，覆盖 **Clash Meta / Clash Verge / OpenClash**、**Shadowrocket (iOS)**、**Stash (iOS/macOS/tvOS)**。

## 规则来源

| 来源 | 内容 | 格式 |
|------|------|------|
| [Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules) | 国内直连、GFW 黑名单、Apple、中国 IP | TXT 域名/IPCIDR |
| [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) | 广告、AI、社媒、流媒体、游戏 | YAML Classical |
| [bgpeer/rules](https://github.com/bgpeer/rules) | 券商（HSBC/IB/Longbridge）、Kraken | YAML Classical |

所有规则集通过 **fastly.jsdelivr.net CDN 镜像**拉取，避免 GitHub 阻断。

## 洋葱过滤模型

```
第 1 层  局域网          → DIRECT
第 2 层  广告            → REJECT
第 3 层  券商（bgpeer）  → 香港/美国节点
第 4 层  加密货币交易所   → 专项组
第 5 层  券商户内联域     → 香港节点
第 6 层  AI 服务         → 综合代理组
第 7 层  泛加密货币       → 综合代理组
第 8 层  社媒/流媒体     → 专项组
第 9 层  开发者/游戏     → 专项组
         Apple          → DIRECT
         国内域名         → DIRECT
         GFW/代理列表    → Default
         中国 IP         → DIRECT
         MATCH          → Catch-All
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

## 配置结构

```
.
├── README.md
├── config/
│   ├── clash.yaml        # Clash Meta / Verge / OpenClash
│   ├── shadowrocket.conf # Shadowrocket (iOS)
│   └── stash.yaml        # Stash (iOS / macOS)
└── icons/
    ├── hsbc.png          # HSBC 分组图标
    └── longbridge.png    # Longbridge 分组图标
```

## 快速开始

### Clash Meta / Verge / OpenClash

1. 复制 `config/clash.yaml` 内容粘贴到配置文件编辑器
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

## 策略组结构

### 入口与 AI
- **Default** — 兜底出口（DIRECT + 全部地理组 + All Nodes）
- **AI** — Gemini / OpenAI 走独立隧道

### 社交媒体与流媒体
- Instagram → YouTube → Google → Twitter → TikTok → NETFLIX → Telegram

### 银行与券商
- HSBC Hong Kong → Interactive Brokers → Longbridge → uSmart → Zinvest → Phillip → Zircon

### 加密货币
- Crypto → Kraken → Coinbase → OSL

### 开发与游戏
- GitHub → Steam → Xbox → Microsoft

### 地理节点池
- Hong Kong → Taiwan → Japan → Korea → Singapore → US → All Nodes

### 终局兜底
- Catch-All（全地区 + DIRECT）

## 隐私拦截

| 域名 | 处置 | 用途 |
|------|:----:|------|
| `ut.taobao.com` | REJECT | 阿里 UT 用户行为埋点 SDK |
| `bugly.qq.com` | REJECT | 腾讯 Bugly 崩溃上报 SDK |

## 图标

仓库自带 `icons/` 目录下的分组图标可通过 jsDelivr 引用：

```
https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@master/icons/hsbc.png
https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@master/icons/longbridge.png
```

---

> 混沌的流量终被驯服于精密的缩进与冰冷的过滤模型之下。
