# Quantumult X 主配置 v2.0.0

[![Version](https://img.shields.io/badge/version-v2.0.0-blue)]() [![Platform](https://img.shields.io/badge/platform-Quantumult%20X%20(iOS)-red)]() [![Made by](https://img.shields.io/badge/made%20by-AI%20(WorkBuddy)-9cf)]()

> **本项目由 AI（WorkBuddy 智能体）完成，未经完整人工审核。** 配置涉及流量分流与 HTTPS 解密，请理解原理并校验后再导入。

## 一键导入

已装 Quantumult X 的 iPhone，点下方按钮，App 内确认后即整体覆盖当前配置：

**[📲 一键导入配置](https://quantumult.app/x/open-app/update-configuration?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2Fchengaiying99-cmd%2Fqx-personal-config%2Fmain%2Fquantumultx.conf)**

raw 被墙时用镜像源：

**[📲 一键导入（镜像）](https://quantumult.app/x/open-app/update-configuration?remote-resource=https%3A%2F%2Fcdn.jsdelivr.net%2Fgh%2Fchengaiying99-cmd%2Fqx-personal-config%40main%2Fquantumultx.conf)**

手动方式：复制下方链接，QX → 设置 → 配置文件 → 编辑，粘贴保存。

```
https://raw.githubusercontent.com/chengaiying99-cmd/qx-personal-config/main/quantumultx.conf
```

> 覆盖导入不保留旧配置。导入后还需两步：替换 `[server_remote]` 中的订阅占位符；在「设置 → GeoLite2」填入 `https://github.com/Hackl0us/GeoIP2-CN/raw/release/Country.mmdb`。

## 配置概览

- **三层十组**：引擎（⚡自动优选 / 🧠固定出口）+ 应用（AI / 流媒体 / Telegram / 国外 / 苹果 / 国内直连）+ 控制（🛑广告拦截 / 🐟兜底）
- **14 条远端规则链**：全部 `force-policy` 收敛到本地策略组，远端只当域名清单
- **DNS**：不使用系统 DNS，阿里 + 腾讯双 DoH 并发，污染结果自动丢弃
- **UDP**：丢弃 443/STUN（封 HTTP/3 广告绕行与 WebRTC 泄露）
- **信任面最小化**：MITM、远端重写、脚本、HTTP 后端默认全部关闭
- **已脱敏**：敏感值均为 `YOUR_*` 占位符

## 使用方法

### 占位符

| 占位符 | 位置 | 说明 |
|---|---|---|
| `YOUR_SUBSCRIPTION_URL` | `[server_remote]` | **必填**，机场 HTTPS 订阅地址 |
| `YOUR_MIRROR_URL` | `[server_remote]` | 可选，镜像订阅行（默认关闭） |
| `YOUR_BASE64_KEY` / `YOUR_PASSWORD` | `[server_local]` | 使用本地手填节点时替换 |
| `YOUR_CA_PASSPHRASE` / `YOUR_P12_BASE64` | `[mitm]` | 启用 MITM 时填入 |

### App 内设置

- **GeoLite2**：填入上方 Country.mmdb 地址（`geoip, cn` 依赖此库）
- 建议：关闭「分流匹配优化」与「兼容性增强」；通知栏仅开「策略检测通知」
- 验证：首页出现 10 个策略组，远程规则全部拉取成功、无 `Invalid line` 报错

## 红果专项模块（可选，未包含在主配置）

仓库内的 `hongguo-adblock.list`（199 条 DNS 清单）与 `hongguo-adblock.conf`（gecko 运行时重写）针对红果短剧/番茄小说 8 秒插屏广告。核心事实来自 [biu1biu/qx-rules](https://github.com/biu1biu/qx-rules) v2.2 的实抓分析（原仓库已清空，规则自 Git 历史取回）：红果广告走字节 gecko `nextad_runtime` 资源包 + `media-show.wanzi.com` 上报 + QUIC 视频流，不走穿山甲域名。

启用方式：`hongguo-adblock.list` 加入 `[filter_remote]`，`hongguo-adblock.conf` 加入 `[rewrite_remote]` 并在 `[mitm]` 声明 `*.bytegecko.com` 等域。**硬约束**：绝不 MITM `*.snssdk.com` / `*.byted.org`（正片接口证书绑定，MITM 会断流）；不拦截正片流、支付与风控域。

## 文件结构

```
.
├── quantumultx.conf        # 主配置 v2.0.0（已脱敏）
├── hongguo-adblock.list    # 红果 DNS 清单（199 条，可选模块）
├── hongguo-adblock.conf    # 红果 gecko 重写 + MITM 域声明（可选模块）
├── index.html              # 一键导入落地页（开启 GitHub Pages 后生效）
└── .gitattributes          # 锁定 LF，防 CRLF 导致 QX 报 Invalid line
```

## 安全注意事项

- `passphrase` 与 `p12` 是私钥，绝不提交任何仓库；不经聊天/IM 传输（会破坏 ASN.1 结构）
- 订阅链接内含节点凭证，必须 HTTPS
- 规则源跟随上游默认分支自动更新，信任链敏感源建议定期 diff
- 丢弃 UDP 443 会使国内 QUIC 回落 TCP，属有意取舍，不接受可删除该项

## 规则来源与致谢

主配置引用：[ddgksf2013](https://github.com/ddgksf2013/Filter)（Unbreak 修正）、[TG-Twilight/AWAvenue-Ads-Rule](https://github.com/TG-Twilight/AWAvenue-Ads-Rule)（GPL-3.0，广告）、[blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script)（GPL-2.0，分流）、[Orz-3/mini](https://github.com/Orz-3/mini)（图标）、[KOP-XIAO/QuantumultX](https://github.com/KOP-XIAO/QuantumultX)（resource-parser）、[Hackl0us/GeoIP2-CN](https://github.com/Hackl0us/GeoIP2-CN)（GPL-3.0，GeoLite2）、[app2smile/rules](https://github.com/app2smile/rules)（MIT，重写示例）。

红果模块出处：[biu1biu/qx-rules](https://github.com/biu1biu/qx-rules)（v2.2，广告栈分析）、[abclq/home-dns-adblock](https://github.com/abclq/home-dns-adblock)（MIT，DNS 清单）。

> 未声明许可证的仓库默认保留所有权利；本项目仅以链接引用其规则。配置编排部分为 AI 生成产物，供个人使用。

---

**本项目为全 AI 制作，未经完整人工审核，使用前请自行校验。**
