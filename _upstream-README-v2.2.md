# 红果短剧 去广告 · Quantumult X 规则

屏蔽红果短剧（番茄系，app_id 8662 / v7.3.2）的 8 秒插屏广告。

## 真实广告栈（基于 2026-09-19 实抓 HAR）

红果**不走穿山甲(pangolin)域名**（所以早期按 pangolin 写的规则全打空）。实际是：

- **广告运行时资源包**：字节 gecko 下发的 `nextad_runtime_series`，托管在 `*.bytegecko.com`
- **广告曝光/归因上报**：`media-show.wanzi.com`（巨量玩子广告平台）+ `ad.toutiao.com`
- **广告视频本体**：走 **QUIC / HTTP3（UDP 443）**，QX 无法解密，未抓到

## 用法（两个远程资源）

QX → 资源 →

1. **分流(Filter)** 添加：
   ```
   https://raw.githubusercontent.com/biu1biu/qx-rules/main/hongguo-adblock.list
   ```
2. **重写(Rewrite)** 添加：
   ```
   https://raw.githubusercontent.com/biu1biu/qx-rules/main/hongguo-adblock.conf
   ```

重写文件自带 `[mitm]` hostname，需在 QX 里信任并启用 MITM 证书。

## 安全边界

不拦截：正片流 `*.douyincdn.com` / `vas-lf-x.snssdk.com`、gecko 主域其它资源、支付 `tp-pay.snssdk.com`、设备风控 `security.snssdk.com`。

## 已知限制

8 秒广告的**视频本体走 QUIC**，QX 抓不到也拦不了。若装上规则后广告仍播放，需要先关掉 QUIC 让广告填充接口暴露出来，再抓一份 HAR 精确定位。详见仓库 issue / 联系维护者。
