# Quantumult X 个人配置模板

[![Version](https://img.shields.io/badge/version-v1.0.0-blue)]() [![Platform](https://img.shields.io/badge/platform-Quantumult%20X%20(iOS)-red)]() [![AI Generated](https://img.shields.io/badge/made%20by-AI%20(WorkBuddy)-9cf)]()

> ## ⚠️ 全 AI 制作声明
>
> **本项目从架构设计、配置编写、规则整合到文档撰写，全程由 AI（WorkBuddy 智能体）独立完成，未经完整人工审核。**
> 配置涉及网络流量分流与 HTTPS 解密（MITM），请务必理解其工作原理并逐段校验后再导入使用。

---

## 一键导入

点击下方链接（手机需已安装 Quantumult X），QX 将自动拉起并导入本配置（去广告规则开箱即用，仅需再填订阅与证书）：

**[📲 一键导入 Quantumult X 配置](quantumult-x:///update-configuration?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2Fchengaiying99-cmd%2Fqx-personal-config%2Fmain%2Fquantumultx.conf)**

或手动复制以下链接到 QX → 设置 → 配置文件 → 引用：

```
https://raw.githubusercontent.com/chengaiying99-cmd/qx-personal-config/main/quantumultx.conf
```

## 项目简介

一套面向 Quantumult X（iOS）的完整去广告配置模板：

- **四层十一段架构**：general / dns / policy / 节点 / 分流 / 重写 / 任务 / MITM 共 12 段完整
- **三层五组策略体系**：3 个节点引擎组（⚡ 自动优选 / 🧠 AI 固定 / 🌐 IPv6 通道）+ 国外 7 组 + 🐼 国内直连 + 🍎 苹果服务 + 3 个控制组，共 15 组，全量 Orz-3/mini 彩色图标
- **广告四道防线**：
  1. DNS 域名拦截（199 条字节系广告 API 精准清单，commit SHA 锁版）
  2. HTTPDNS 拦截（@commit 锁版，防绕过本地 DNS）
  3. SDK 备用域兜底（@commit 锁版）
  4. gecko 广告运行时重写 + 全局丢 QUIC（封死广告视频本体）
- **专项去广告**：红果短剧 / 番茄小说 8 秒插屏广告专项方案（基于上游作者实抓 HAR 的真实广告栈分析）
- **已脱敏**：所有敏感值均为 `YOUR_*` 占位符，可安全 fork / 分发

## 去广告覆盖范围

配置集中整合了以下 App 的去广告订阅规则（共 34 条分流 + 21 条重写）：

| 类别 | 覆盖 |
|---|---|
| 视频平台 | YouTube（字幕 + 去广告）、B 站、Spotify、腾讯新闻 |
| 短剧/小说 | **红果短剧 / 番茄小说**（8 秒插屏专项，见下方出处） |
| 电商/生活 | 闲鱼、菜鸟、小红书、高德地图 |
| 资讯社区 | 知乎净化、贴吧净化 |
| 工具/其他 | Keep、墨迹天气、小程序广告、中国联通、通用开屏广告、信息流 SDK |
| 系统级 | iOS 更新屏蔽、Google 重定向修正、HTTPDNS 拦截 |

## 红果短剧去广告 · 来源与出处

本配置中的红果短剧去广告规则基于以下上游项目整合，出处如下：

| 文件 | 来源 | 说明 |
|---|---|---|
| `hongguo-adblock.list`（199 条 DNS 拦截清单） | 主要来自 [abclq/home-dns-adblock](https://github.com/abclq/home-dns-adblock)（MIT），原作者对字节系广告 API 逐域枚举并剔除白名单；另含 1 条 [biu1biu/qx-rules](https://github.com/biu1biu/qx-rules) v2.2 的 `media-show.wanzi.com` 上报域 | 连接级拦截，无需 MITM |
| `hongguo-adblock.conf`（gecko 运行时重写 + MITM 声明） | 核心规则来自 [biu1biu/qx-rules](https://github.com/biu1biu/qx-rules) **v2.2（commit `b18a8b8`，原作者 biu1biu，规则自其 Git 历史取回，仓库现已被作者清空）**。作者基于 2026-09-19 实抓 HAR（红果 v7.3.2 / app_id 8662）确认真实广告栈 | 原作者结论：红果不走穿山甲域名，实际链路为 gecko `nextad_runtime` 资源包 + `media-show.wanzi.com`/`ad.toutiao.com` 曝光上报 + QUIC 视频流 |

**红果广告原理（引自原作者分析）**：红果内置 8 秒插屏广告不走穿山甲（pangolin）域名——早期按 pangolin 写的规则全部无效。真实链路是：① 字节 gecko 下发 `nextad_runtime_series` 广告运行时资源包（`*.bytegecko.com`）；② `media-show.wanzi.com`（巨量玩子）+ `ad.toutiao.com` 曝光归因上报；③ 广告视频本体走 QUIC/HTTP3（UDP 443），QX 无法解密——本配置用全局丢 QUIC 从传输层封死。

**原 v2.2 的关键安全约束（已遵守）**：绝不 MITM `*.snssdk.com` / `*.byted.org`——红果正片播放接口 `vas-lf-x.snssdk.com` 带证书绑定（pinning），MITM 会导致握手失败、视频无法播放。

原作者明确的**不拦截**边界（均已遵守）：正片流 `*.douyincdn.com` / `vas-lf-x.snssdk.com`、gecko 主域非广告资源、支付 `tp-pay.snssdk.com`、设备风控 `security.snssdk.com`。

## 文件结构

```
.
├── quantumultx.conf            # 主配置文件（正式版 v1.0.0，已脱敏）
├── hongguo-adblock.list        # 红果广告域名清单（199 条，LF）
├── hongguo-adblock.conf        # 红果 gecko 运行时重写 + MITM 域声明
├── _upstream-README-v2.2.md    # biu1biu/qx-rules v2.2 原始 README（来证归档）
└── .gitattributes              # 锁定 *.conf/*.list 为 LF（防 CRLF 导致 QX 报错）
```

## 使用方法

### 1. 替换占位符（共 3 处）

| 占位符 | 位置 | 替换为 |
|---|---|---|
| `YOUR_AIRPORT_SUBSCRIPTION_URL` | `[server_remote]`（注释状态） | 你的机场 **HTTPS** 订阅地址，替换后取消该行注释 |
| `YOUR_CERTIFICATE_PASSWORD` | `[mitm]`（注释状态） | MITM 证书口令 |
| `YOUR_P12_CERTIFICATE_CONTENT` | `[mitm]`（注释状态） | p12 证书 Base64 内容 |

> 说明：红果去广告等订阅规则地址已填入真实值（指向本账号仓库，SHA 锁版），导入后即可正常拉取，无需修改。
>
> ⚠️ p12 证书与口令**不要经聊天/IM 传输**（会破坏 ASN.1 结构导致证书失效）。推荐在手机 QX 现用配置的 `[mitm]` 段直接复制这两行。

### 2. App 内一次性设置（必做）

- **其他设置 → GeoLite2**：填 `https://github.com/Hackl0us/GeoIP2-CN/raw/release/Country.mmdb` 并开启自动更新
- **关闭**「分流匹配优化」与「兼容性增强」（减少去广告失效）
- **通知栏**：仅开「策略检测通知」「脚本通知」
- **信任并安装 MITM 证书**（设置 → 证书）

### 3. 验证

- 主页策略组图标全部正常显示（Orz-3/mini 彩色图标）
- 远程分流/重写全部拉取成功，无 Invalid line 报错
- 红果短剧播放无 8 秒插屏广告即为生效

## 安全注意事项

- **MITM 证书**：`passphrase` 与 `p12` 是你的私钥，**绝不提交到任何仓库**
- **订阅链接**：内含节点凭证，务必使用 HTTPS 端点
- **供应链**：第三方规则均已 @commit 锁版或指向稳定分支
- **QUIC 全局丢弃**：`udp_drop_list = 443, STUN, QUIC` 是广告封锁的一部分，若部分 QUIC 应用异常请自行取舍

## 规则来源与致谢

本配置引用的规则文件版权归原作者所有：

| 项目 | 许可证 | 用途 |
|---|---|---|
| [biu1biu/qx-rules](https://github.com/biu1biu/qx-rules)（v2.2，自 Git 历史取回） | 未声明 | 红果短剧 gecko 重写核心规则、真实广告栈分析 |
| [abclq/home-dns-adblock](https://github.com/abclq/home-dns-adblock) | MIT | 红果/番茄 198 条 DNS 清单基础 |
| [ddgksf2013/Rewrite](https://github.com/ddgksf2013/Rewrite) · [Filter](https://github.com/ddgksf2013/Filter) | 未声明 | 去开屏、闲鱼/高德/菜鸟/Keep/墨迹/联通/小程序去广告、Unbreak 修正 |
| [app2smile/rules](https://github.com/app2smile/rules) | MIT | B 站/知乎/贴吧/腾讯新闻/Spotify 净化 |
| [hwind2021/QuantumultX-AdBlock-CN](https://github.com/hwind2021/QuantumultX-AdBlock-CN) | MIT + 上游声明 | HTTPDNS 拦截、SDK 兜底、开屏/信息流 SDK 重写 |
| [ZenmoFeiShi/Qx](https://github.com/ZenmoFeiShi/Qx) | 未声明 | YouTube 字幕/去广告 |
| [blackmatrix7/ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) | GPL-2.0 | 30+ 条分流规则、Google 重定向、更新屏蔽 |
| [TG-Twilight/AWAvenue-Ads-Rule](https://github.com/TG-Twilight/AWAvenue-Ads-Rule) | GPL-3.0 | 社区广告拦截双源之一 |
| [NobyDa/Script](https://github.com/NobyDa/Script) | GPL-3.0 | AdRule 广告规则 |
| [Orz-3/mini](https://github.com/Orz-3/mini) | 未声明 | 全套策略组图标 |
| [KOP-XIAO/QuantumultX](https://github.com/KOP-XIAO/QuantumultX) | 未声明 | resource-parser 资源解析脚本 |
| [Hackl0us/GeoIP2-CN](https://github.com/Hackl0us/GeoIP2-CN) | GPL-3.0 | GeoLite2 中国 IP 库 |

> 未声明许可证的仓库默认保留所有权利；本项目仅以链接引用其规则，未复制其代码。本项目自身的配置编排部分为 AI 生成产物，供个人使用。

---

**再次提醒：本项目为全 AI 制作，未经完整人工审核，使用前请自行校验。**
