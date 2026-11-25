# g1-acq


Google One（iOS）订阅入口的商品 ID 改写与采集工具，适用于 **Shadowrocket / 小火箭**。

**只需一个 G1 ACQ：支持 Ultra 1个月免费试用、2TB 12个月优惠测试、被动采集原始 SKU，并提供最近 10 次响应、本次新增、重复诊断与 JSON 导出。** 原始 iOS 2TB → `g1.2tb.ai.12months_ip_90p` 用户截图指定候选的 12个月优惠测试，20TB / 30TB 默认使用 Ultra 1个月免费试用 SKU `g1.20tb.1month_eft.acq` / `g1.ai.ultra.1month_eft.acq`，并各自独立回退到对应的三个月 90p 备用 SKU。当前安装版本为 `v7.6.0`，响应脚本为 `g1-acq-v7.6`，无需另装扫描器或反复切换模块。采集只读取手机实际收到的匹配响应，**不会主动查询全量 SKU**；旧 ID 再次出现时累加次数，清单保持原样也能代表正在收到重复数据。完整安装与实机核对步骤见 [Pro 12个月优惠测试与 SKU 扫描](docs/pro-annual-test.md)。

此次版本整合 [GitHub PR #3](https://github.com/crhywun/g1-acq/pull/3)，加入 20TB / 30TB 两档 Ultra 1个月免费试用支持；试用是否向当前账号展示、实际订阅周期与续费金额按 App Store 收银台记录，权益按 Google One 账户实际状态记录。

> 响应中出现商品 ID，只能证明页面携带该字符串，不等于 App Store 商品目录校验。改写命中也不等于商品可购买；商品名称、周期、初次与续费金额须在 App Store 收银台核对，权益以 Google One 账户实际状态为准。

## 主模块安装地址

**G1 ACQ：Ultra 1个月免费试用、2TB 12个月优惠测试、被动采集与响应活动诊断**

```text
https://cdn.jsdelivr.net/gh/crhywun/g1-acq@v7.6.0/g1-acq-v7.sgmodule
```

首次安装时在小火箭模块管理中添加上面的地址并启用；从旧版本升级时，将旧 **G1 ACQ** 替换为此地址一次。模块安装入口与内部响应、报告脚本均固定引用 `@v7.6.0`，无需单独添加脚本；安装或升级后重新连接，再重新进入 Google App 的订阅页。发布新版本需同时更新脚本内容和固定版本 pin；合并 PR 不会让旧固定 CDN 地址自动更新或失效，旧地址仍引用其原版本。升级保留本地历史记录；响应诊断不保证产生新 ID，重复安装也不会促使服务器下发新 SKU。

只开启这一个 **G1 ACQ** 即可。若手机曾安装独立 **G1 SKU Scan**、**G1 Pro Annual Test** 或其它改写副本，停用这些旧副本，避免重复计数或改写；只装了一个主模块时没有额外关闭步骤。历史 `v7.0.0` / `v7.1.0` / `v7.2.0` / `v7.3.0` / `v7.4.0` / `v7.5.0`、`pro-annual-test-v1` / `pro-annual-test-v2` 标签保持不动。

最近 10 次响应诊断由主脚本 `g1-acq-v7.6` 提供；**G1 Pro Annual Test** 兼容入口也使用该脚本，但仍仅匹配 `/ai?`。独立 **G1 SKU Scan** 兼容入口只更新下载 pin，脚本继续保存历史版本 `pro-annual-test-v2`，没有新增活动诊断；新版报告读取它时显示 **旧采集记录**，响应历史为空。

## 三档映射

脚本先从 `one.google.com/` 的原始文字响应中采集 SKU，再仅对匹配 `one.google.com/ai?` 的响应按完整源 SKU 执行以下映射：

| 正文中的完整源 SKU | 默认目标 | 备用目标 |
|---|---|---|
| `g1.ios.2tb.` 前缀的完整 SKU | `g1.2tb.ai.12months_ip_90p`（用户指定候选） | 无 |
| `g1.ios.20tb.m.v2` | `g1.20tb.1month_eft.acq`（Ultra 1个月免费试用） | `g1.20tb.3months_ip_90p.acq`（三个月 90p 优惠） |
| `g1.ios.30tb.ai.pro.m` | `g1.ai.ultra.1month_eft.acq`（Ultra 1个月免费试用） | `g1.ai.ultra.3months_ip_90p.acq`（三个月 90p 优惠） |

2TB 12个月优惠测试的源商品是 **原始 iOS 2TB**，目标固定且没有备用档。20TB / 30TB 各档独立按原始响应正文选择：该档 `1month_eft` 默认目标出现时优先使用默认目标，即使对应 90p 备用也出现；默认目标未出现、对应 90p 备用目标出现时才回退到 90p；两个目标都未出现时仍使用 `1month_eft` 默认目标。75p 不再是本版回退目标；旧版本历史记录中的 `g1.20tb.3months_ip_75p.acq` 与 `g1.ai.ultra.3months_ip_75p.acq` 保留原值。这是正文字符串判断，不是商店商品查询。三档都未命中时原样返回响应。

**2TB 候选固定为 `g1.2tb.ai.12months_ip_90p`，没有 `.acq` 后缀。** 用户截图首项提供该候选与价格标签 `$1.99 × 12 months = $23.88/year`，图示表达连续 12 个月的月度优惠；`90p` 表示 **90% off，约一折**，不是实付 90% 的九折。`$23.88/year` 是截图中的 12 个月合计标签，不是一次年付扣款的证据。

截图标题为 **Pro 5TB**，本轮源商品仍是 `g1.ios.2tb.` 前缀的原始 iOS 2TB 完整 SKU，没有新增 5TB 源前缀。截图是候选与价格标签的来源，不是已验证的 iOS 商店商品资料，也不证明实际 iOS 价格、扣款周期或 5TB 权益。实际名称、周期、初次与续费金额以 App Store 返回商品核对，权益以 Google One 账户显示为准。离线测试验证字符串匹配与报告行为。

## 安装（Shadowrocket / 小火箭）

进入 **配置页 → 当前使用配置右侧的 (i) 图标 → HTTPS 解密**。

![配置页入口：点击 shadowrocket.conf 右侧的 (i) 图标进入配置编辑，才能看到 HTTPS 解密](docs-config-entry.png)

1. 在小火箭模块管理中启用上面 `v7.6.0` 地址安装的 **G1 ACQ**；旧版用户按前文升级一次，首页保持 VPN 连接。
2. 从上述当前配置的 **(i)** 入口进入 **HTTPS 解密**，打开解密开关。已有可用且已受信任的 Shadowrocket 证书时直接沿用；尚未配置证书时，在该页生成并安装证书，再完成下两步。
3. 首次配置证书时，在 iOS **设置 → 通用 → VPN 与设备管理** 中完成 Shadowrocket 描述文件安装。
4. 首次配置证书时，在 iOS **设置 → 通用 → 关于本机 → 证书信任设置** 中开启对应 Shadowrocket 根证书的信任。
5. 在当前配置的 **HTTPS 解密 hostname 列表**中核对 `one.google.com`；模块通过 `%APPEND%` 追加该域名。这里是 HTTPS 解密列表，不是 DNS Hosts。
6. 安装模块、更改模块开关或解密配置后，重新连接小火箭，再重新进入 Google App 的订阅页面。

## 手机端使用流程

1. 保持新版 **G1 ACQ** 开启与小火箭连接；收到匹配响应后，采集和改写会自动依次执行。
2. 打开 **Google App（彩色 G 图标）→ Google One 管理**，进入 **2TB / Pro** 方案进行12个月优惠测试；核对 Ultra 1个月免费试用映射时，再分别打开实际提供的 **20TB / 30TB** 方案。
3. 进入订阅流程后，核对 App Store 收银台显示的商品名称、周期、初次与续费金额；在 Safari 地址栏粘贴完整[本地报告地址](https://one.google.com/g1-sku-report)，查看原始候选 SKU 与改写记录，再点操作区的 **导出 JSON** 按钮。

![Google One Ultra 方案选择面板：旧模块的 20TB 方案入口示例](docs-ultra-plans.png)

截图展示 20TB 入口；2TB 12个月优惠测试应打开 **2TB / Pro** 方案。账号、地区和版本会影响页面展示，以手机实际提供的方案为准。原生 StoreKit 界面自身不属于网页响应改写范围。

浏览方案且收到匹配响应时，主模块会先读取原始正文中的 `g1.*` 与 `com.google.one.*`，按 ID 去重累计次数，最多保留 **300 个 ID**，然后才执行符合 `/ai?` 范围的三档改写。重复 ID 只更新次数和时间；到达容量后继续更新已保存 ID，并记录被容量丢弃的新 ID 数。采集保存的是改写前的原始编号，跳过本地报告页；无需另装扫描器。手机保存候选 ID、次数、时间、扫描状态和最多 10 条经过路径脱敏的响应事件，不保存完整响应正文，不查询 App Store，不发网络上报。

查找页面未展示的 SKU 时，依次浏览 Google App 实际提供的月付、年付和优惠入口：原始响应中携带的编号即使未显示在界面上，也会被采集。服务器未下发的编号不会进入原始清单；指定候选由模块替换后，在 App Store 原生订阅界面验证。完整 iOS 操作见 [SKU 采集与候选验证](docs/pro-annual-test.md#6-在-ios-小火箭中采集与验证隐藏-sku)。

## 本地报告与排错

保持 **G1 ACQ** 开启与小火箭连接，在 Safari 地址栏粘贴完整[本地报告地址](https://one.google.com/g1-sku-report)。页面上方应显示 **本机脚本已接管** 和 **v7.6.0** 标识，操作区有 **导出 JSON** 按钮；也可直接打开[JSON 导出地址](https://one.google.com/g1-sku-report?format=json)。

地址使用 `one.google.com`，页面由 Shadowrocket 的 `http-request` 脚本在手机本机生成，**不是 Google 提供的报告页面**。拦截范围为 `/g1-sku-report`、末尾带 `/` 的同一路径及其 query；本机接管后直接返回 HTML 或 JSON，不把这次请求交给 Google。

若看到 Google 普通页面、404，或没有上述本机标识与导出按钮，当前展示的就不是这版本机报告。核对新版模块开启、小火箭连接、**当前配置 (i) → HTTPS 解密**、`one.google.com` hostname 与受信任证书，再把完整 URL 粘贴到 Safari 地址栏打开。

`reportVersion=v7.6.0` 表示报告脚本版本，**不证明新版采集器已经收到响应**。报告单独展示实际保存的 `scan.version`、最近响应时间与历史；本版主采集器为 `g1-acq-v7.6`。旧脚本记录显示 **旧采集记录；尚未保存本版采集响应记录。** 版本未知或尚无记录时标为 **尚未保存本版采集响应记录**；旧记录未保存 `recentResponses` 时不补造活动历史，也不据此判断手机故障。

| 采集字段 | 含义 |
|---|---|
| `scan.scannedResponses` | 累计处理的文字响应数，保持原有计数含义 |
| `scan.matchedResponses` | 文字响应中至少识别到一个候选 ID 的累计响应数，含重复 ID；保持原有含义 |
| `scan.nonTextResponses` | 累计非文本响应数，不计入 `scannedResponses` |
| `scan.recentResponses` | 最多 10 条响应事件；JSON 数组按接收顺序旧 → 新保存，HTML 表格按新 → 旧展示；每条含 `{at, path, bodyType, status, candidateCount, addedCount, omittedCount}` |

单次事件中，`candidateCount` 是该响应内去重后的候选数，`addedCount` 是本次新加入并实际保留的 ID 数，`omittedCount` 是本次因容量丢弃的不同 ID 数。状态包括 `new-skus`（新增）、`known-skus`（重复）、`no-skus`（无 ID）、`limit-reached`（无新增保留且有容量丢弃）、`non-text`（非文本）。`addedCount>0` 时优先标为 `new-skus`，即使同时 `omittedCount>0`；只有 `addedCount=0` 且 `omittedCount>0` 时才标为 `limit-reached`。路径只保留 `/`、`/ai`、`/plans`、`/_/…/data/batchexecute` 或 `[其他 one.google.com 路径]`，不保存 query、完整 URL、正文、请求头或响应头。

顶层 `configuredTarget=g1.2tb.ai.12months_ip_90p` 表示当前配置；`test.version`、`test.target` 则来自最近一次保存的执行记录，升级后仍会保留旧版本与实际保存的旧目标，直到产生新记录。当前响应脚本为 `g1-acq-v7.6`，新候选记录的 `test.targetOrigin=user-specified`。旧 v7.5 记录的 2TB 目标同样是 `g1.2tb.ai.12months_ip_90p`、来源为 `user-specified`，但 `test.version` 仍是 `g1-acq-v7.5`；相同目标不代表旧记录已升级。旧 v7.3 / v7.4 记录保留 `g1.2tb.ai.annual_ip_90p.acq` 与 `user-specified`；旧 v7.2 记录保留 `g1.2tb.ai.annual.3months_ip_90p.acq` 与 `pattern-derived`；旧 v1 / v7.1 普通年付记录保留 `g1.2tb.ai.annual` 与 `legacy`；无记录时来源为空。升级不会把历史 ID 或目标来源重标为当前候选。`test.target` 仅表示该次执行配置的 2TB 目标；20TB / 30TB 的实际目标看各行 `to`。

`test.hitCount` 是三档总改写数；`test.annualHitCount` 沿用兼容字段名，仅统计 2TB / Pro 优惠改写，日志 `annualHits` 含义相同，报告显示“其中 2TB”。字段名与计数不证明年付扣款。`test.matchedFrom` 每行含 `{id, to, count, fallback}`：`to` 是该源本次实际目标，`fallback=true` 表示使用了该次执行对应版本的备用目标。本版 20TB / 30TB 的备用目标分别是 `g1.20tb.3months_ip_90p.acq` / `g1.ai.ultra.3months_ip_90p.acq`；旧版本历史记录仍可保留 75p 备用目标。HTML 结合 `fallback` 与实际 `to` 显示 90p 备用、历史 75p 备用或“否”；已标为备用但目标信息不足时只显示“是”，旧行原值继续保留。`fallback=false` 时也应结合 `test.version` 与 `to` 核对默认目标。

`status=no-match` 仅表示三档都没命中，此时保留响应原样；若 2TB 没命中但 20TB / 30TB 命中，状态仍为 `rewritten`，须看 `annualHitCount` 判断 2TB 优惠改写。报告保留最近一次执行记录，按时间核对；根据采集结果确认完整源 ID，不推测新的 iOS SKU。报告兼容旧 v1 的仅 2TB 记录：旧行缺少 `to` 时补为原普通年付目标 `g1.2tb.ai.annual`，缺少的 `annualHitCount` 从旧行次数计算。旧 v1 / v7.1 执行记录继续显示 `g1.2tb.ai.annual`；旧 v7.2 记录继续显示实际保存的 `g1.2tb.ai.annual.3months_ip_90p.acq`；旧 v7.3 / v7.4 记录继续显示 `g1.2tb.ai.annual_ip_90p.acq`；旧 v7.5 记录继续显示 `g1.2tb.ai.12months_ip_90p` 和旧版本 `g1-acq-v7.5`。这些旧记录的实际目标、来源与版本均保留。

当前脚本日志前缀为 `[g1-acq]`，按以下字段核对执行结果：

| 输出标记 | 含义 |
|---|---|
| `g1-acq-v7.6 status=<状态> addedCount=N candidates=N omittedCount=N occurrences=N saved=yes/no` | 采集状态、本次新保留数、单响应去重候选数、容量丢弃数、字符串出现总次数与存储结果；`saved=yes` 才表示本次记录已写入 |
| `saved=no` / `SKU collection unavailable` | 本次记录写入未成功，或采集流程未完成；报告仍能显示旧计数与旧时间，须核对这类日志 |
| `g1-acq-v7.6 hits=N annualHits=N` | 当前脚本版本、三档总改写数与其中 2TB / Pro 优惠改写数（兼容日志名，不证明年付扣款） |
| `源 SKU → 实际目标 xN` | 逐行记录完整源 ID、本次实际目标与该源改写次数 |
| `fallback=实际完整目标编号` | 该行 20TB / 30TB 使用备用档；本版输出 `fallback=g1.20tb.3months_ip_90p.acq` 或 `fallback=g1.ai.ultra.3months_ip_90p.acq`，与该行实际 `to` 一致；旧版本日志中的 `fallback=75p` 仅为历史格式 |

清单仍是旧 SKU 时，按下面顺序比较一次实际活动：

1. 在报告记录 `reportVersion`、实际 `scan.version`、三个响应计数与最新事件时间，保留当前清单。
2. 从 Google App 重新进入实际提供的 2TB / Pro 方案，再浏览其它实际方案页，等待加载完成。
3. 返回 Safari 刷新报告。文字计数增长且最新事件为 `known-skus`，说明收到的是已保存 ID，次数会增加而清单不增加；`no-skus` 表示该响应未携带可识别 ID。若只增加 `nonTextResponses`，查看 `non-text` 事件。
4. 三个计数与最新事件时间均未变化，只能确认这轮未保存新的采集记录；存储写入失败也会保留旧计数。先查看脚本日志是否出现 `saved=no` 或 `SKU collection unavailable`，再逐项核对页面缓存、`one.google.com/` 匹配范围、VPN 连接、模块开关、当前配置的 HTTPS 解密、证书信任与 hostname。报告版本升级本身不代表采集脚本已执行。

这组诊断不保证新 SKU 出现；重装模块或反复清空记录不会使服务器返回新编号。`?clear=1` 保持手动两键清理，仅在确实要重置历史时使用。完整字段、采集与实机记录模板见 [Pro 12个月优惠测试与 SKU 扫描](docs/pro-annual-test.md)。

## 工作范围与本地存储

| 工具 | 脚本匹配范围 | 行为 |
|---|---|---|
| G1 ACQ 被动采集 | `^https://one\.google\.com/` 的响应，跳过本地报告页 | 从原始文字正文采集候选 ID；非文本单独计数；保存最近 10 次响应诊断 |
| G1 ACQ 三档改写 | `^https://one\.google\.com/ai\?` 的响应 | 采集后执行完整 SKU 改写、独立回退并保存最近执行记录 |
| 本地结果页 | `/g1-sku-report`、末尾带 `/` 的同一路径及其 query | 本机显示采集结果、最近改写记录或导出 JSON |

- **脚本处理范围**：G1 ACQ 被动读取匹配页面的响应体，先保存候选 ID、次数、时间、扫描状态与最多 10 条脱敏响应事件，再检查改写范围；不读取请求头或请求体，不保存完整响应正文。响应事件只保存时间、固定类别路径、正文类型、诊断状态与计数，不保存 query 或完整 URL。改写只修改商品 ID，不改价格字段，页面文字价格不替代 App Store 收银台金额。
- **实际外发行为**：当前脚本没有网络上报调用；G1 ACQ 写脚本日志，采集、改写和报告共用手机本地 `$persistentStore`。Shadowrocket 脚本宿主提供网络与存储 API，当前代码未调用网络上报不等于宿主缺少外发能力。模块与脚本下载仍使用 GitHub / jsDelivr 地址。
- **MITM 范围**：模块只追加 `one.google.com`，其全站流量进入解密范围；页面改写受脚本路径限制。证书在设备生成、安装与信任，HTTPS 解密设置属于当前配置。
- **系统权限**：脚本没有申请定位、相册、通知的调用；使用过程包含小火箭 VPN 连接和 iOS 描述文件、根证书信任配置。
- **全局规则影响**：模块的 `[Rule]` 拦截全局 UDP 443，促使支持回退的 QUIC 请求改走 TCP，也会影响其它 App 的 HTTP/3 使用、速度或连通性。
- **版本与存储**：本次模块安装入口与全部内部脚本统一固定引用 `@v7.6.0`；历史版本与 v1 / v2 标签保留且不移动。本地键仍为 `g1.sku.scan.v1`、`g1.pro.annual.test.v1`，`schemaVersion` 保持 `1`；采集、响应事件与执行记录时间使用 UTC；结果页的 `?clear=1` 只清除这两个键。

## 文件

| 文件 | 用途 |
|---|---|
| [g1-acq-v7.sgmodule](g1-acq-v7.sgmodule) | 唯一主模块：被动采集、三档改写与响应活动诊断 |
| [g1-acq.js](g1-acq.js) | `g1-acq-v7.6` 响应脚本，先采集原始 SKU 与活动诊断，再改写并保存记录 |
| [g1-pro-annual-test.sgmodule](g1-pro-annual-test.sgmodule) | 旧版兼容文件，保留 `/ai?` 匹配范围，主流程无需安装或启用 |
| [g1-sku-scan.sgmodule](g1-sku-scan.sgmodule) | 旧版独立扫描兼容文件，主流程无需安装或启用 |
| [g1-sku-scan.js](g1-sku-scan.js) | 历史独立扫描脚本，保留候选 ID 去重、计数与时间记录；未增加响应活动诊断 |
| [g1-sku-report.js](g1-sku-report.js) | 本机 HTML 报告、实际采集版本与响应历史、导出 JSON 和手动两键清理 |
| [docs/pro-annual-test.md](docs/pro-annual-test.md) | 单模块使用、被动采集诊断、12个月优惠实机核对与报告字段 |

## 离线验证

使用 Node.js 18 或更新版本，在仓库根目录执行：

```sh
node --test tests/sku-tools.test.cjs
```

离线检查以主脚本 [g1-acq.js](g1-acq.js) 为核心，覆盖三档完整编号匹配、2TB 固定 12个月优惠候选、20TB / 30TB 的 `1month_eft` 默认优先和 90p 独立回退的 16 种组合、默认与备用均缺失时仍使用 `1month_eft`、75p 不参与本版回退、只有 20TB / 30TB 的页面、URL 范围、三档零命中、总改写数与 2TB 改写数、按实际 `to` 显示 90p 备用和历史 75p 备用、当前配置与最近执行目标区分、旧 v1 / v7.1 / v7.2 / v7.3 / v7.4 / v7.5 报告兼容、存储异常、采集去重与上限、本地报告、导出字段和模块脚本版本。以实际执行输出核对结果；测试中的源编号是合成样本，不构成商店上架或实机购买证据。

## 贡献致谢

感谢 [@cehomecambodia-lang](https://github.com/cehomecambodia-lang) 通过 [PR #3](https://github.com/crhywun/g1-acq/pull/3) 提供 20TB / 30TB Ultra 1个月免费试用 SKU 映射和三个月 90p 备用方案。
