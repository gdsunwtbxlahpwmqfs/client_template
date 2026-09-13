# client_template

| Client      | Update date |
| ----------- | ----------- |
| clash       | 2025-11-18  |
| clash2      | 2026-02-23  |
| sing-box    | 2026-09-06  |
| sing-box-hello | 2026-09-13 |

# singbox的config
https://sing-box.sagernet.org/configuration/

# 节点外网映射路径（配置节点时对照复查）

各模板接入「外网节点/订阅」的映射位置如下，后续改节点只需替换对应位置。

## clash
- 外网订阅链接：`proxy-providers.NODE_NAME.url`（占位符 `PROXY_PROVIDERS_URL`）
- 提供者类型：`proxy-providers.NODE_NAME.type: http`
- 节点分发：锚点 `u: &u {use: ['NODE_NAME']}`，各策略组 `<<: *u` 自动引入
- 本地文件导入（可选）：`proxy-providers.local` 已注释，file 模式 `path: ./profiles/nodes.yaml`

## clash2
- 节点列表：直接写入顶层 `proxies:`（键 = 节点名）
- 策略组引用：`proxy-groups.*.proxies:` 中列出可用节点名（如 🚀 节点选择 / ♻️ 自动选择）
- 无 proxy-provider，节点需手动填充

## sing-box
- 节点出站对象：替换 `outbounds[0]` 的 `<OUTBOUND_REPLACE>`（节点对象数组/聚合）
- 节点 tag 列表：替换 `<NODE_REPLACE>`（出现在 `♻️ 自动选择` urltest 及 `🤖 AI`/`▶️ YouTube`/`📱 Telegram`/`✈️ Proxy` 各 selector 的 outbounds 中）

## sing-box-hello
- 节点出站对象：替换 `outbounds[0]` 的 `<INBOUND_REPLACE>`（节点对象数组/聚合）
- 节点 tag 列表：替换 `<NODE_REPLACE>`（`✈️ Proxy`、`♻️ 自动选择`、`📱 Telegram`、`▶️ YouTube`、`🤖 OpenAI` 共 5 处 outbounds 中）
- 说明：本模板为注释版，`http_clients`/`store_dns` 需要 sing-box >= 1.14

# URL 清单（复查用）

统一测速地址（所有模板 urltest / health-check）: http://www.gstatic.com/generate_204

## 官方文档
- sing-box 配置文档: https://sing-box.sagernet.org/configuration/
- sing-box JSON Schema: https://sing-box.sagernet.org/schema.json
- sing-geosite 规则集仓库: https://github.com/SagerNet/sing-geosite
- sing-geoip 规则集仓库: https://github.com/SagerNet/sing-geoip
- clash rules 规则集仓库（Loyalsoldier）: https://github.com/Loyalsoldier/clash-rules

## clash 规则集（rule-providers，interval 86400）
基础地址: `https://cdn.jsdelivr.net/gh/Loyalsoldier/clash-rules@release/<name>.txt`
- behavior: domain → `reject` / `icloud` / `apple` / `google` / `proxy` / `direct` / `private` / `gfw` / `greatfire` / `tld-not-cn`
- behavior: ipcidr → `telegramcidr` / `cncidr` / `lancidr`
- behavior: classical → `applications`
- 节点订阅（待填真实链接）: `PROXY_PROVIDERS_URL`

## sing-box 规则集（rule_sets，更新间隔 1d，下载走 http-client-direct 直连）
- `https://testingcf.jsdelivr.net/gh/SagerNet/sing-geosite@rule-set/<name>.srs`
  `geosite-category-ads-all` / `geosite-cn` / `geosite-openai` / `geosite-anthropic` / `geosite-google-gemini` / `geosite-youtube` / `geosite-netflix` / `geosite-telegram` / `geosite-geolocation-!cn`
- `https://testingcf.jsdelivr.net/gh/SagerNet/sing-geoip@rule-set/geoip-cn.srs`

## sing-box-hello 规则集（rule_sets，更新间隔 1d，下载走 http-client-proxy → ✈️ Proxy）
- `https://raw.githubusercontent.com/SagerNet/sing-geosite/rule-set/<name>.srs`
  `geosite-category-ads-all` / `geosite-telegram` / `geosite-youtube` / `geosite-netflix` / `geosite-openai@ads` / `geosite-openai` / `geosite-anthropic` / `geosite-google-gemini` / `geosite-apple` / `geosite-google` / `geosite-microsoft` / `geosite-geolocation-!cn` / `geosite-private` / `geosite-cn`
- `https://raw.githubusercontent.com/SagerNet/sing-geoip/rule-set/geoip-cn.srs`
- `https://raw.githubusercontent.com/MetaCubeX/meta-rules-dat/sing/geo/geoip/<name>.srs`
  `geoip-telegram` / `geoip-netflix` / `geoip-google` / `geoip-private`

## 公共服务地址（sing-box-hello）
- DNS: `1.1.1.1:853` DoT（cloudflare-dns.com） / `dns.alidns.com:443/dns-query` DoH3 / `223.5.5.5:53` UDP
- NTP: `time.apple.com:123`（detour 走 direct-out）