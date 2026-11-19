# Clash Rules — 极简网络分流规则集

三份分平台配置 + 42 个自托管规则集，覆盖 **Clash Meta / Verge / OpenClash**、**Shadowrocket (iOS)**、**Stash (iOS / macOS / tvOS)**。三端策略组、规则顺序、地区池逐项同构。

## 三平台对比

| 项 | Clash Meta | Shadowrocket | Stash |
|---|:---:|:---:|:---:|
| 语法 | YAML | INI | YAML |
| 节点来源 | `proxy-providers` | App 内置订阅 | 同 Clash |
| 节点过滤 | `filter:` 正则 | `policy-regex-filter` | 同 Clash |
| 系统接管 | TUN + `mixed-port` | `bypass-system` + `ipv6` | Stash App 管理 |
| DNS | 完整 Fake-IP 块 | — | 精简 DNS |
| 域名嗅探 | Sniffer | — | — |
| YAML 锚点 | 支持 | — | 不支持，已展开 |

## 仓库结构

```
.
├── config/
│   ├── clash.yaml          # Clash Meta / Verge / OpenClash
│   ├── shadowrocket.conf   # Shadowrocket (iOS)
│   └── stash.yaml          # Stash (iOS / macOS)
├── rules/                  # 42 个规则集，每集合一对 .yaml / .list
│   ├── HSBC/HSBC.yaml      # Clash / Stash 读 .yaml
│   ├── HSBC/HSBC.list      # Shadowrocket 读 .list
│   └── ...
└── icons/                  # 73 个分组图标
```

每份配置都是 **62 个策略组**（34 个服务组，含兜底 `Default` ＋ 28 个地区池）、**41 个规则集**、**51 条规则**。

## 命中顺序

自上而下首次命中生效，与 `config/clash.yaml` 的 `rules:` 逐行一致。

```
 1  局域网              ls_lan                  → DIRECT
 2  临时验证            202.90.108.13/32        → DIRECT
 3  广告拦截            bm_ad + 7 条域名         → REJECT
 4  AI 服务            ChatGPT / Gemini        → 专属组
 5  社媒               Telegram → Line → …     → 专属组
 6  银行与券商          9 个服务组                → 专属组
 7  钱包支付            PayPal / Wise           → 专属组
 8  图表与加密          TradingView → … → Crypto → 专项在前、泛表兜底
 9  预测市场            Kalshi                  → 专属组
10  开发者              GitHub                  → 专属组
11  流媒体              NETFLIX                 → 专属组
12  其他               Docker                  → DeepWep
13  巴哈姆特             gamer.com.tw            → DeepWep
14  Apple              ls_apple                → DIRECT
15  国内域名             ls_direct               → DIRECT
16  中国 IP             ls_cncidr               → DIRECT
17  GFW / 代理列表       ls_gfw + ls_proxy       → DeepWep
18  兜底               MATCH                   → Default
```

两处容易踩的次序：`ys_crypto` 排在六个专项交易所**之后**，专项优先、泛表兜底；`MATCH` 落 `Default`，不是 `DeepWep`。

## 规则来源

| 来源 | 数量 | 说明 |
|---|:---:|---|
| 自托管 `ys_*` | 34 | 本仓库 `rules/`，逐个域名做 DoH 解析 + TLS 证书主体核验 |
| Loyalsoldier | 6 | `ls_lan` / `ls_direct` / `ls_apple` / `ls_proxy` / `ls_gfw` / `ls_cncidr` |
| blackmatrix7 | 1 | 广告 `bm_ad` |

远程资源统一走 `cdn.jsdelivr.net`；Clash 与 Stash 用 YAML 锚点合并模板，Stash 侧已展开为完整字段。

## 快速开始

**Clash Meta / Verge / OpenClash**：复制 `config/clash.yaml` 全文粘进配置编辑器，把 `proxy-providers > cocoduck > url` 换成自己的订阅地址，重载。

**Stash (iOS / macOS)**：同上，用 `config/stash.yaml`，改同一个键的 `url`。改订阅键名时，62 个策略组的 `use: [cocoduck]` 要同步改，否则 Stash 拒绝加载。

**Shadowrocket (iOS)**：复制 `config/shadowrocket.conf`，App 内新建配置粘贴，再到 `Settings → Subscriptions` 导入节点订阅；`policy-regex-filter` 按节点名把节点分进对应策略组。

## 隐私拦截

`bm_ad` 规则集之外另有 7 条精确拒止：

| 规则 | 用途 |
|---|---|
| `ut.taobao.com` | 阿里 UT 用户行为埋点 |
| `bugly.qq.com` | 腾讯 Bugly 崩溃上报 |
| `*pcdn*.biliapi.net` | B 站 PCDN 探测 |
| `mcdn.bilivideo.cn` / `mcdn.bilivideo.com` | B 站 P2P 分发 |
| `szbdyd.com` | 跟踪域名 |
| `mountaintoys.cn` | 跟踪域名 |

## 引用地址

```yaml
图标: "https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@main/icons/<Name>.png"
规则: "https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@main/rules/<Name>/<Name>.yaml"
列表: "https://cdn.jsdelivr.net/gh/yubisyuu/clash-rules@main/rules/<Name>/<Name>.list"
```

`@main` 有 CDN 缓存，改完想立刻生效就把 `@main` 换成提交哈希 `@<sha>`。

---

> 混沌的流量终被驯服于精密的缩进与冰冷的过滤模型之下。
