# Shadowrocket 配置（paoluzx 分流逻辑）

按用途分组的 Shadowrocket 代理配置。分流逻辑继承自 `paoluzx.yaml`（Clash/Mihomo 配置），用 lazy_group 的 Shadowrocket 语法重写。

## 快速使用

1. Shadowrocket → 右上角 `+` → **从 URL 添加配置**，粘贴：

   ```
   https://raw.githubusercontent.com/kaiklife/shadowrocket/main/paoluzx.conf
   ```

2. 客户端「订阅」标签页，添加 **Paoluz 订阅链接**（节点不写死在配置里，靠订阅拉，地区节点组会按名称自动归类）。

> 配置里的 `update-url` 已指向上面这个链接。以后改配置 push 到 GitHub，客户端右上角下拉即可自动更新，不用重新导入。

## 配置结构

### 地区节点组（url-test 自动测速）

靠 `policy-regex-filter` 按节点名关键词自动归类。机场节点命名变了，就动这些正则：

| 组名 | 匹配关键词 |
|------|-----------|
| 香港节点 | 🇭🇰 / HK / Hong / 香港 / 深港 / 沪港 / 京港 / 港 |
| 日本节点 | 🇯🇵 / JP / Japan / Tokyo / 日本 / 东京 / 大阪 / 川日 / 埼玉 / 日 |
| 台湾节点 | 🇹🇼 / TW / TWN / Taiwan / 台湾 / 台北 / 台中 / 新北 |
| 新加坡节点 | 🇸🇬 / SG / Sing / 新加坡 / 狮城 / 沪新 / 京新 / 深新 |
| 韩国节点 | 🇰🇷 / KR / Korea / 韩国 / 首尔 / 釜山 |
| 马来西亚节点 | 🇲🇾 / MY / Malaysia / 马来西亚 / 吉隆坡 |
| 越南节点 | 🇻🇳 / VN / Vietnam / 越南 / 河内 / 胡志明 |
| 美国节点 | 🇺🇸 / US / USA / America / 美国 / 洛杉矶 / 纽约 / 芝加哥 |
| 土耳其节点 | 🇹🇷 / TR / Turkey / 土耳其 / 伊斯坦布尔 |
| 俄罗斯节点 | 🇷🇺 / RU / Russia / 俄罗斯 / 莫斯科 |
| 其他节点 | 排除以上所有地区 + link/官网/官/套餐/公告 |
| 自动选择 | 排除 link/官网/官/套餐/公告（屏蔽官方节点） |

### 用途分组（select 手动切换）

| 组名 | 分流内容 | 默认策略 |
|------|---------|---------|
| AI | OpenAI / Claude / Gemini / x.ai / grok | 日本节点 |
| YouTube | YouTube | 香港节点 |
| Netflix | Netflix | 香港节点 |
| Disney+ | Disney+ | 香港节点 |
| TikTok | TikTok | 日本节点 |
| bilibili | B 站 | 直连 |
| Spotify | Spotify | 香港节点 |
| ChatApps | Telegram / Twitter / FB / IG / Whatsapp / Threads | 香港节点 |
| 开发 | GitHub + Docker Hub | 直连 |
| Google | Google | 香港节点 |
| Microsoft | Microsoft | 直连 |
| 游戏平台 | Steam / Xbox / PlayStation / Nintendo | 直连 |
| 国外网站 | 其他国外站（Global 兜底） | 香港节点 |
| 漏网之鱼 | FINAL 最终兜底 | 直连 |

## 规则分流顺序（[Rule] 从上到下）

1. **去广告** → `Advertising.list` REJECT
2. **AI / 流媒体 / 社交** → 各自用途组
3. **国内直连** → Lan / China / `GEOIP,CN` / Apple / SteamCN → DIRECT
4. **其他特定** → Google / GitHub / Docker / Microsoft / Steam / Sony / Xbox / Nintendo
5. **兜底** → `Global.list` → 国外网站
6. **漏网之鱼** → `FINAL` → 漏网之鱼

## 怎么改

本地工作副本：`~/Aiagent/code/shadowrocket/`

```bash
cd ~/Aiagent/code/shadowrocket
# 改 paoluzx.conf（分组在 [Proxy Group]，规则在 [Rule]）
git add paoluzx.conf
git commit -m "改了什么"
git push
```

推完客户端右上角下拉更新即可（update-url 已配好）。

## 关键决策（改之前先看）

- **MITM 关闭**（`enable = false`）—— 本配置用不着解密 HTTPS，也顺带避开了 lazy_group 模板公共 CA 证书的安全风险。要开的话自己生成私有 CA，别用公共模板证书。
- **Microsoft 默认直连** —— 按「微软直接 DIRECT」的旧约定设的，组里可切代理。
- **去广告 REJECT** —— iOS 上偶发误伤，不想要删掉 `Advertising.list` 那一行。
- **订阅 token 不写进配置** —— 订阅在客户端加，所以这仓库能放心公开。
- **规则集全走 blackmatrix7 的 `Shadowrocket/*.list`** —— Shadowrocket 不认 Clash/Mihomo 的 `.mrs` 格式，新增规则集只能选 blackmatrix7 的 Shadowrocket 目录（`rule/Shadowrocket/xxx/xxx.list`）。
