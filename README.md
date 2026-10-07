# Quantumult X 主配置

个人 Quantumult X 配置：三层十组策略、18 条分流规则链（全量 `force-policy` 收敛）、双 DoH 并发、去广告重写仅保留红果模块（MITM 最小解密面）。

## 一键导入

手机已装 Quantumult X 的话，点下方链接，在 QX 内确认后即下载并整体覆盖当前配置：

https://quantumult.app/x/open-app/update-configuration?remote-resource=https%3A%2F%2Fraw.githubusercontent.com%2Fchengaiying99-cmd%2Fqx-personal-config%2Fmain%2Fquantumultx.conf

## 手动导入

复制下面链接，QX → 设置 → 配置文件 → 编辑，粘贴保存：

```
https://raw.githubusercontent.com/chengaiying99-cmd/qx-personal-config/main/quantumultx.conf
```

## 导入后必做

1. 替换 `[server_remote]` 中的 `YOUR_SUBSCRIPTION_URL` 为你的 HTTPS 机场订阅
2. 「设置 → GeoLite2」填入 `https://github.com/Hackl0us/GeoIP2-CN/raw/release/Country.mmdb`
3. 红果去广告重写需启用 MITM：「设置 → 证书」生成并安装信任，然后在 `[mitm]` 填入 `passphrase` / `p12`

## 文件说明

| 文件 | 说明 |
|---|---|
| `quantumultx.conf` | 主配置 v2.3.0（已脱敏，订阅与证书为占位符） |
| `hongguo-adblock.list` | 红果广告域名清单（199 条，已被主配置引用） |
| `hongguo-adblock.conf` | 红果 gecko 广告运行时重写（已被主配置引用） |

> 本项目由 AI 生成，未经完整人工审核，导入前请自行校验。
