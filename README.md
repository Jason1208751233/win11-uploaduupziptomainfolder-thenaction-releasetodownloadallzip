下面这段文字**直接粘到 `README.md` 里**即可。推荐放在文档**最前面**（目录之前），作为"零、快速开始（交接速查）"；如果你想保持现有目录结构，也可以放在**最末尾**作为"附录 G：使用与交接说明"。两种位置二选一，内容一样。

> **它替代什么、不替代什么（先看清）：**
> - ✅ 这段文字是加进 **`README.md`** 的，作为"速查卡"，和后面的详细章节**并存**，不删除原有内容；
> - ✅ 它替代的是你以前**口头/聊天里反复解释**"我怎么用、朋友怎么用、东西在哪"的那部分说明，以后直接让对方看 README 开头；
> - ❌ **不要用它替代 `main.yml`**（工作流代码一字不动）；
> - ❌ **不要用它替代 `uup.zip`**（构建原材料，永远保留在仓库根目录）；
> - ❌ 不要把真实用户名/仓库名写进去，继续用 `***` 或"你的用户名/你的仓库名"占位。

---

以下为可直接粘贴的内容（复制虚线之间的部分）：

---

## 零、快速开始（交接速查）

> 这一节是给"下次的我"和"第一次来的朋友"看的速查卡。三步走：**上传 `uup.zip` → 等绿勾 → Releases 下载拼接校验安装**。详细原理见后面各章。

### 0.1 我自己下次怎么用（维护者速查）

**前提**：仓库里 `main.yml` 是最终跑通版（genisoimage 转发脚本方案），仓库根目录有 `uup.zip`。这两个东西**不要删、不要乱改**。

**每次装新版本，只做这三步：**

1. **换原料**：去 [uupdump.net](https://uupdump.net) 按原来的选项（x64 / 中文简体 / 最新版本 / Professional）下载 **Download the conversion script (Linux)** 的 zip → 改名为 `uup.zip`（全小写，一字不差）；
2. **覆盖上传**：仓库首页 → **Add file → Upload files** → 把新的 `uup.zip` 拖进去 → **Commit changes**（是**覆盖**旧的，不是先删再传）；
3. **等结果**：上传瞬间自动触发，Actions 页面出现 🟡 → 等 **40~55 分钟** → ✅ 绿勾 → 去 **Releases** 找最新那个 `vYYYYMMDD-HHMM-runN` 版本。

**拿到 Release 之后：**

1. 下载该版本里**全部** `win11.iso.part-*` 和 `SHA256SUMS.txt`（推荐迅雷：右键 part → 复制链接 → 迅雷新建任务；**挂梯子更快，不挂也能下**；镜像前缀 `https://gh-proxy.com/` 是可选兜底，失效就退回迅雷）；
2. 全部 part 放进同一个文件夹，用 `copy /b win11.iso.part-00+win11.iso.part-01+win11.iso.part-02 win11.iso` 拼回完整 ISO（**有几个 part 就连几个，顺序 00→01→02，一个不能少**）；
3. `certutil -hashfile win11.iso SHA256` 算出的哈希，和 `SHA256SUMS.txt` 里那串 64 位值**逐位对比**，一致才安装；
4. 双击挂载跑 `setup.exe`，或用 Rufus / Ventoy 做 U 盘启动盘。

**三条铁律：**
- ⚠️ **永远不要删 `uup.zip`**。它是构建原材料，删了既不会自动触发、也没东西可构建。换版本 = 覆盖上传。
- ⚠️ **改了 `main.yml` 不会自动跑**（故意设计）。改完去 Actions → `build-iso` → **Run workflow** 手动点一次。
- ⚠️ **红叉先别慌**：90% 是 uupdump 官方源临时抽风，直接 **Re-run jobs** 重跑一次就好；还不行就把日志原文复制出来对照第十三节，或直接发给 AI。

### 0.2 别人怎么用（两种人，两套说法）

**情况 A：对方只想拿现成的 ISO，不想构建**

直接把你的 **Releases 页面链接**发给对方，让对方从"第八节 下载 Release 产物"开始看：
- 下载全部 part + `SHA256SUMS.txt`
- 拼接（第九节）
- 校验（第十节）
- 安装（第十一节）

对方**不需要 GitHub 账号，不需要 Fork，不需要懂任何代码**。

**情况 B：对方想自己构建（不依赖你的仓库）**

让对方按下面四步走（也是本文档第四、五节的浓缩）：

1. **拿仓库**：把你的仓库地址发给对方，对方点 **Fork**；或让对方用 GitHub 网页 **Import repository** 导入一份到自己账号下（**不需要装 Git、不需要命令行**）；
2. **确认权限**：对方仓库 Settings → Actions → General，确认允许 Actions 运行（工作流里已声明 `permissions: contents: write`，不用配 Token）；
3. **上传原料**：对方自己去 uupdump 下载 Linux 转换脚本 zip → 改名 `uup.zip` → 上传到**他自己**的仓库根目录 → Commit；
4. **等结果**：自动触发 → 40~55 分钟 → 绿勾 → 去**他自己**仓库的 Releases 下载。

对方构建出来的 Release 在**他自己的仓库**里，和你互不影响；以后他想换版本，同样只换 `uup.zip`，零代码修改。

**给对方的提醒：**
- Public 仓库 Actions 免费不限时长；Private 仓库有额度限制，建议 Public；
- 公开仓库的 Actions 日志人人可见，别在里面写敏感信息；
- 如果对方要公开自己的仓库，提醒他把 README 里的 `***` 继续保留为占位符，别填真实账号；
- 对方如果只是"想用一次"，走情况 A 最省事，不用 Fork。

### 0.3 这段说明替代什么、不替代什么

| 项目 | 说明 |
|---|---|
| 放在哪里 | 加进 `README.md`，建议放**最前面**（目录之前）或**最末尾**（作为附录 G），二选一 |
| 替代什么 | 替代你以前**口头/聊天里反复解释**"我怎么用、朋友怎么用、文件在哪"的那部分说明；以后直接让对方看 README 开头 |
| 不替代 `main.yml` | 工作流代码一字不动，照旧使用最终跑通版 |
| 不替代 `uup.zip` | 构建原材料，永远保留在仓库根目录，换版本用覆盖上传 |
| 不删除原章节 | 后面的详细章节（第三~十六节、附录）继续保留，作为深入参考；本节只是"速查卡" |
| 脱敏要求 | 本节继续用 `***` 或"你的用户名/你的仓库名"占位，不写真实账号、仓库名、Token |

### 0.4 一页纸流程图（贴墙版）

```text
我：uupdump 下新脚本 → 改名 uup.zip → 覆盖上传 → 等绿勾 → Releases 下载
                                                                  │
别人（只下载）：给 Releases 链接 → 下载 part → 拼接 → 校验 → 安装
                                                                  │
别人（自己构建）：Fork/Import 仓库 → 上传自己的 uup.zip → 等绿勾 → 自己 Releases 下载
                                                                  │
所有人：copy /b 拼接 → certutil 校验 → 挂载 setup.exe / Rufus / Ventoy
```

---

粘贴完成后：

1. **Commit** 一下 `README.md` 即可，**不需要动 `main.yml`**，也不会触发构建（工作流只监听 `uup.zip`）；
2. 想验证构建还正常：Actions → `build-iso` → **Run workflow** 手动点一次；
3. 以后你自己换版本、朋友来问怎么用，**直接让对方看 README 开头这一节**就行。

















# Win11 ISO 自动构建器 · 完整说明书（脱敏版 · 最终跑通版）

> **文档版本：** 基于最终跑通的 `main.yml`（genisoimage 转发脚本方案）编写
> **当前状态：** ✅ 已跑通 —— 云端成功构建约 5482 MB Windows 11 ISO，切成 `win11.iso.part-00 / 01 / 02` 分块并自动发布到 Releases
>
> **隐私说明：** 本文档已对个人账号、仓库路径、Release 链接做脱敏处理。文中所有 `***` 均为占位符（用户名/仓库名/个人信息）。公开仓库前请检查全文，不要写入真实账号、Token、私人路径。
>
> **免责声明：** Windows ISO 版权归 Microsoft 所有，请遵守微软许可协议，仅用于个人合法用途。本项目不绕过 Windows 11 硬件检查，不提供激活，不鼓励盗版或未授权分发。第三方工具（迅雷、镜像加速站、梯子）请自行评估风险。

---

## 目录

1. [项目背景与目标](#一项目背景与目标)
2. [系统架构总览](#二系统架构总览)
3. [仓库文件结构](#三仓库文件结构)
4. [准备与初始化](#四准备与初始化)
5. [构建操作步骤](#五构建操作步骤从-uupzip-到-release)
6. [完整工作流 YAML 逐段详解](#六完整工作流-yaml-逐段详解)
7. [核心修复原理：genisoimage 转发脚本](#七核心修复原理genisoimage-转发脚本)
8. [下载 Release 产物](#八下载-release-产物)
9. [把分块拼回完整 ISO](#九把分块拼回完整-iso)
10. [校验文件完整性](#十校验文件完整性)
11. [安装 Windows](#十一安装-windows)
12. [日常维护与版本更新](#十二日常维护与版本更新)
13. [故障排查与日志判读](#十三故障排查与日志判读)
14. [FAQ](#十四faq)
15. [维护者检查清单](#十五维护者检查清单)
16. [隐私、安全与合规](#十六隐私安全与合规)
17. [附录 A：成功日志样例（脱敏）](#附录-a成功日志样例脱敏)
18. [附录 B：失败日志样例与原因对照](#附录-b失败日志样例与原因对照脱敏)
19. [附录 C：命令速查表](#附录-c命令速查表)
20. [附录 D：术语表](#附录-d术语表)
21. [附录 E：变更记录模板](#附录-e变更记录模板)
22. [附录 F：脱敏规则](#附录-f脱敏规则)

---

## 一、项目背景与目标

### 1.1 这个项目是干嘛的

上传一个 `uup.zip`，GitHub 云端自动下载 Windows 散件并打包出 Windows 11 安装 ISO。**自己电脑什么都不用装**，只需要浏览器和一个 GitHub 账号。

完整链路：

1. 从 [uupdump.net](https://uupdump.net)（微软官方更新服务器的第三方下载器）选好 Windows 版本；
2. 把它的 Linux 转换脚本包（zip）改名为 `uup.zip` 传到本仓库；
3. GitHub 云端自动：下载散件 → 合成 `install.wim` → 打包 ISO（自动绕开 4GB 限制）→ 切成 <2GB 分块 → 发 Releases；
4. 你从 Releases 下载分块、拼回、校验、安装。

全程约 **40~55 分钟**，Public 仓库 GitHub Actions 免费不限时长。

### 1.2 为什么值得用这个项目而不是本地构建

| 维度 | 本地构建 | 本项目（云端） |
|---|---|---|
| 需要安装的软件 | wimlib、aria2、打包工具等一堆 | 零 |
| 需要的技术能力 | 命令行排错 | 会上传文件即可 |
| 磁盘占用 | 本地 10GB+ | 云端 runner，本地只存最终 ISO |
| 网络要求 | 直连微软更新服务器，国内常极慢 | 云端下载；你只下载成品（可分块多线程） |
| 环境一致性 | 各人机器不同，坑多 | 每次全新 ubuntu-latest，可复现 |

### 1.3 最终方案关键技术决策一览

| 决策点 | 最终选择 | 一句话理由 |
|---|---|---|
| 触发方式 | `push paths: ['uup.zip']` + `workflow_dispatch` | 上传即构建；改代码不触发 |
| 打包工具 | `genisoimage` + 转发脚本 | 支持 UDF；xorriso 不支持 `--udf` |
| >4GB 处理 | 转发脚本自动注入 `-allow-limited-size` | 绕开 ISO9660 4GiB-1 上限且保留 UDF |
| 分块 | `split -b 1900M -d -a 2` | 小于 GitHub Release 单文件 2GB 限制 |
| 校验 | `sha256sum` → `SHA256SUMS.txt` | 拼接后校验完整 ISO |
| Release 标签 | `v北京时间-run编号` | 永不撞名，历史可回溯 |
| 磁盘 | 构建前删 .NET/GHC/Android/CodeQL | runner 仅约 14GB，须为 6GB+ ISO 腾地 |
| 并发控制 | `concurrency` 同组单跑 | 防并发构建互相抢占磁盘和标签 |

### 1.4 适用范围与不适用范围

**适用：**
- GitHub Actions + uupdump Linux 转换脚本；
- Windows 11 x64 ISO 云端构建；
- Public 仓库免费构建；
- 分块发布、下载、拼接、校验全流程；
- 零代码修改换版本（只换 `uup.zip`）。

**不适用 / 未承诺：**
- 不绕过 Windows 11 硬件要求（TPM/Secure Boot 等）；
- 不提供激活、不鼓励盗版；
- 不保证 uupdump 所有版本/架构/语言一次成功（最常见失败是官方源临时抽风，重跑即可）；
- 不保证第三方镜像加速站长期可用；
- 不替代微软官方介质。

---

## 二、系统架构总览

### 2.1 端到端流程图

```text
用户浏览器
  │
  ├─ ① uupdump.net 选版本/语言/架构 → 下载 "Download the conversion script (Linux)"
  ├─ ② zip 改名为 uup.zip（全小写、一字不差）
  └─ ③ Add file → Upload files → Commit changes
        │（push 事件命中 paths: ['uup.zip'] → 自动触发）
        ▼
GitHub Actions · ubuntu-latest runner（全新临时机器，每次干净）
  │
  ├─ Checkout repository        拉取仓库，拿到 uup.zip
  ├─ Free disk space            删 /usr/share/dotnet、/opt/ghc、
  │                             /usr/local/lib/android、/opt/hostedtoolcache/CodeQL
  │                             （腾出约 10GB+，df -h 打印剩余空间）
  ├─ Install dependencies       apt 装 cabextract wimtools genisoimage
  │                             chntpw aria2 unzip xorriso
  ├─ Patch genisoimage          /usr/bin/genisoimage → 转发脚本：
  │   （核心修复）               exec /usr/bin/genisoimage.orig -allow-limited-size "$@"
  │                             mkisofs 同样处理（防脚本用别名调用）
  │                             cat 自检打印转发脚本内容
  ├─ Run UUP dump script        unzip uup.zip -d uup → chmod +x →
  │                             ./uup_download_linux.sh
  │                             ├─ aria2 多线程下载 UUP 散件
  │                             ├─ wimlib 合成 install.wim（可能 >4GB）
  │                             └─ 调 genisoimage 打包（自动带 -allow-limited-size，UDF 保留）
  ├─ Locate and split ISO       find 找最大 *.iso → mv 为 win11.iso
  │                             split -b 1900M -d -a 2 → win11.iso.part-00/01/02…
  │                             sha256sum win11.iso → SHA256SUMS.txt
  │                             rm win11.iso（腾空间）→ ls -lh 打印产物
  └─ Publish to Release         TAG="v北京时间-runN" → gh release create
        │                       上传全部 part-* + SHA256SUMS.txt
        ▼
GitHub Releases 页面
  │
  ├─ win11.iso.part-00（约 1.9GB）
  ├─ win11.iso.part-01（约 1.9GB）
  ├─ win11.iso.part-02（约 1.7GB，视版本而定）
  └─ SHA256SUMS.txt（完整 ISO 的 SHA256）
        │
        ▼
用户本机
  │
  ├─ 下载：迅雷（推荐，挂梯子更快）/ 浏览器 / 镜像前缀 gh-proxy.com
  ├─ 拼接：copy /b part-00+part-01+part-02 win11.iso
  ├─ 校验：certutil -hashfile win11.iso SHA256 对比 SHA256SUMS.txt
  └─ 安装：挂载 setup.exe / Rufus / Ventoy 做启动盘
```

### 2.2 数据流与文件变换

| 阶段 | 输入 | 输出 | 位置 |
|---|---|---|---|
| 触发 | uup.zip（你上传，几十 KB~几 MB） | push 事件 | 仓库 |
| 下载 | uup.zip 内脚本 | UUP 散件（数 GB） | runner `uup/` |
| 合成 | UUP 散件 | install.wim（>4GB） | runner `uup/` |
| 打包 | install.wim + 启动文件 | win11.iso（约 5.5GB） | runner `uup/` |
| 切块 | win11.iso | part-00/01/02（约 1.9GB 每个）+ SHA256SUMS.txt | runner `uup/` |
| 发布 | part-* + SHA256SUMS.txt | GitHub Release 资产 | Releases |
| 下载 | Release 资产 | 本地 part-* | 你的电脑 |
| 拼接 | part-* | win11.iso | 你的电脑 |
| 校验 | win11.iso vs SHA256SUMS.txt | ✅/❌ | 你的电脑 |

---

## 三、仓库文件结构

```text
.
├── .github/
│   └── workflows/
│       └── main.yml          # 工作流定义（本说明书的配套代码）
├── README.md                 # 本说明书
└── uup.zip                   # 构建原材料（上传/覆盖触发构建，切勿删除）
```

说明：

- `uup.zip` 是构建必需的输入，**不要删除**；换版本用"覆盖上传"；
- `main.yml` 改动后不会自动执行，需手动 Run workflow；
- `README.md` 纯文档，不参与构建。

---

## 四、准备与初始化

1. **注册/登录 GitHub**（免费）。Public 仓库 Actions 免费不限时长；Private 有额度限制，建议 Public。
2. **获取本仓库**：Fork，或 GitHub 网页 Import repository 导入一份。不需要本地装 Git。
3. **检查 Actions 权限**：Settings → Actions → General → 允许 Actions 运行。工作流内已声明 `permissions: contents: write`，无需额外配置 Token。
4. **隐私预处理**（若要公开）：
   - README 中保留 `***` 占位符；
   - 示例链接统一 `https://github.com/***/***/releases/download/...`；
   - 不写真实 Token、私人邮箱、聊天 ID、完整构建路径；
   - 公开仓库的 Actions 日志人人可见，工作流中不要打印敏感信息（当前 main.yml 无此问题）。

---

## 五、构建操作步骤（从 uup.zip 到 Release）

### 第 1 步：去 uupdump 选版本

1. 打开 [uupdump.net](https://uupdump.net)；
2. 架构选 **x64**；
3. Windows 版本选最新（如 24H2 / 25H2）；
4. 语言选 **Chinese (Simplified)**（或你要的语言）；
5. 版本勾选默认即可（**Professional 专业版必含**）；
6. 下载方式选 **Download the conversion script (Linux)** → 得到一个 zip（不是 ISO，只是转换脚本+散件清单）。

### 第 2 步：改名

把 zip 改名为 `uup.zip`。

⚠️ 必须一字不差：全小写、8 个字符 `u u p . z i p`。叫 `UUP.zip`、`uup (1).zip`、`uup.zip.zip` 都触发不了（`paths: ['uup.zip']` 精确匹配）。

### 第 3 步：上传

仓库首页 → **Add file → Upload files** → 拖入 `uup.zip` → **Commit changes**。

上传成功瞬间自动开跑（监听 push 的 uup.zip 路径变动）。

### 第 4 步：确认在跑

Actions 标签页：黄色圆点 🟡 = 进行中。

若没自动跑（个别情况）：Actions → 左侧 **build-iso** → 右侧 **Run workflow** → 绿色按钮手动触发。

### 第 5 步：等 40~55 分钟

- 🟡 进行中
- ✅ 绿勾 = 成功 → 去 Releases 领成品
- ❌ 红叉 = 失败 → 见第十三节

### 第 6 步：领取成品

仓库右侧 **Releases** → 找到 `vYYYYMMDD-HHMM-runN` 版本 → 里面有全部 `win11.iso.part-*` 和 `SHA256SUMS.txt`。

---

## 六、完整工作流 YAML 逐段详解

> 本节逐行解释最终跑通的 `main.yml`。建议对照代码阅读。

### 6.1 头部与触发

```yaml
name: build-iso

on:
  workflow_dispatch:
  push:
    paths:
      - 'uup.zip'

permissions:
  contents: write

concurrency:
  group: build-iso
  cancel-in-progress: false
```

| 行 | 解释 |
|---|---|
| `name: build-iso` | Actions 页面左侧显示的工作流名 |
| `workflow_dispatch` | 允许网页手动触发（首次测试、改代码后验证、失败后重试） |
| `push: paths: ['uup.zip']` | **只有 uup.zip 被上传/覆盖才自动构建**；改 main.yml、README 都不触发——故意设计，避免改代码白烧 50 分钟 |
| `permissions: contents: write` | 授予创建 Release、上传资产的权限；缺了这行 `gh release create` 会报权限错误 |
| `concurrency` | 构建编排：`group: build-iso` 同组任务排队互斥；`cancel-in-progress: false` 不打断正在跑的构建（ISO 构建一打断全废） |

### 6.2 Checkout repository

```yaml
      - name: Checkout repository
        uses: actions/checkout@v4
```

把仓库拉到 runner 工作目录，拿到 `uup.zip`。日志可能出现 `Node.js 20 deprecated` 警告——这是 checkout 动作自身的平台提示，**无害，不影响构建**。

### 6.3 Free disk space

```yaml
      - name: Free disk space
        run: |
          sudo rm -rf /usr/share/dotnet /opt/ghc /usr/local/lib/android /opt/hostedtoolcache/CodeQL
          df -h
```

- GitHub 托管 runner 可用空间仅约 14GB，而构建中间产物（散件+wim+ISO）需要 10GB+；
- 删除预装的大块头：.NET SDK（/usr/share/dotnet）、GHC 编译器（/opt/ghc）、Android SDK、CodeQL；
- `df -h` 打印剩余空间到日志，便于排查磁盘问题。

### 6.4 Install dependencies

```yaml
      - name: Install dependencies
        run: |
          sudo apt-get update
          sudo apt-get install -y cabextract wimtools genisoimage chntpw aria2 unzip xorriso
```

| 包 | 在构建中的作用 |
|---|---|
| `cabextract` | 解压 UUP 散件中的 CAB 包 |
| `wimtools` | wimlib，合成/处理 install.wim（>4GB 靠它支持） |
| `genisoimage` | **最终实际打包工具**，原生支持 UDF |
| `chntpw` | 处理注册表 hive（UUP 脚本依赖） |
| `aria2` | 多线程分段下载 UUP 散件（比单线程快很多） |
| `unzip` | 解压你上传的 uup.zip |
| `xorriso` | 保留安装但不参与最终打包（曾尝试用它替代 genisoimage，因不支持 `--udf` 失败；留装无副作用） |

### 6.5 Patch genisoimage（核心修复，详见第七节）

```yaml
      - name: Patch genisoimage to allow >4GB files
        run: |
          for name in genisoimage mkisofs; do
            if [ -e "/usr/bin/$name" ]; then
              sudo mv "/usr/bin/$name" "/usr/bin/$name.orig"
            fi
            printf '#!/bin/bash\nexec /usr/bin/%s.orig -allow-limited-size "$@"\n' "$name" | sudo tee "/usr/bin/$name" > /dev/null
            sudo chmod +x "/usr/bin/$name"
          done
          echo "== genisoimage 现在的内容（应为转发脚本） =="
          cat /usr/bin/genisoimage
```

做的事：

1. 循环处理 `genisoimage` 和 `mkisofs` 两个名字（uup 工具包可能用别名调用）；
2. 原版程序改名 `*.orig`；
3. 写入转发脚本：`#!/bin/bash` + `exec /usr/bin/<name>.orig -allow-limited-size "$@"`；
4. `chmod +x` 赋执行权限；
5. `cat` 打印自检——日志里应看到转发脚本内容，这是排查时第一检查点。

### 6.6 Run UUP dump script

```yaml
      - name: Run UUP dump script
        run: |
          unzip -o uup.zip -d uup
          cd uup
          chmod +x uup_download_linux.sh
          echo "== 引用 genisoimage 的位置（仅调试用） =="
          grep -rnI "genisoimage" . 2>/dev/null | head -20 || echo "(无直接引用，调用藏在工具包内——已由上一步套转发脚本解决)"
          ./uup_download_linux.sh
```

- `unzip -o uup.zip -d uup`：覆盖解压到 `uup/`；
- `chmod +x`：给下载脚本执行权限；
- `grep`：仅调试用，看脚本目录有没有直接引用 genisoimage。**找不到属正常**——新版 uup 脚本的打包命令藏在运行时解压的 `uup-converter-wimlib` 工具包内，所以上一节的"物理拦截"才可靠；
- `./uup_download_linux.sh`：真正干活的步骤，内部依次完成：aria2 下载散件 → wimlib 合成 wim → genisoimage 打包 ISO。耗时最长（30 分钟以上），失败绝大多数发生在这里。

### 6.7 Locate and split ISO

```yaml
      - name: Locate and split ISO
        run: |
          cd uup
          echo "== Searching for generated ISO files =="
          find . -type f -iname "*.iso" -printf '%s %p\n' | sort -rn | head -20

          ISO=$(find . -type f -iname "*.iso" -printf '%s %p\n' | sort -rn | head -n1 | cut -d' ' -f2-)

          if [ -z "$ISO" ]; then
            echo "Not found in ./ , searching parent directory..."
            ISO=$(find .. -maxdepth 2 -type f -iname "*.iso" -printf '%s %p\n' | sort -rn | head -n1 | cut -d' ' -f2-)
          fi

          if [ -z "$ISO" ]; then
            echo "Error: ISO file was not generated!"
            find . .. -maxdepth 3 -type f -printf '%p\n' | head -100
            exit 1
          fi

          echo "== Found ISO: $ISO =="
          mv "$ISO" win11.iso
          split -b 1900M -d -a 2 win11.iso win11.iso.part-
          sha256sum win11.iso > SHA256SUMS.txt
          rm -f win11.iso
          ls -lh win11.iso.part-* SHA256SUMS.txt
```

逐行逻辑：

1. `find ... -printf '%s %p\n' | sort -rn`：列出所有 ISO 并按**字节数倒序**——取最大的那个（uup 目录可能有多个中间产物，最大的才是成品）；
2. 当前目录没找到 → 去父目录（`-maxdepth 2`）再找；
3. 还没有 → 打印前 100 个文件路径帮助排查，`exit 1` 失败退出；
4. `mv "$ISO" win11.iso`：统一命名为 win11.iso；
5. `split -b 1900M -d -a 2`：
   - `1900M`：每块约 1.9GB，**小于 GitHub Release 单文件 2GB 限制**，留有余量；
   - `-d`：数字后缀（00、01、02 而非 aa、ab）；
   - `-a 2`：后缀定长 2 位；
6. `sha256sum win11.iso > SHA256SUMS.txt`：对**完整 ISO**算哈希（不是对 part！）；
7. `rm -f win11.iso`：删掉完整 ISO 腾磁盘（part 已含全部内容）；
8. `ls -lh`：打印产物清单到日志，成功判读依据。

### 6.8 Publish to Release

```yaml
      - name: Publish to Release
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          cd uup
          TAG="v$(TZ='Asia/Shanghai' date +%Y%m%d-%H%M)-run${{ github.run_number }}"
          gh release create "$TAG" --title "Win11 ISO ($TAG)" --notes "Auto build by GitHub Actions" win11.iso.part-* SHA256SUMS.txt
```

- `GH_TOKEN`：用 Actions 自动注入的 `GITHUB_TOKEN`，**不需要也不应该手动配置 Token**；
- `TZ='Asia/Shanghai'`：标签时间用北京时间，直观；
- `date +%Y%m%d-%H%M`：年月日-时分；
- `${{ github.run_number }}`：Actions 运行编号——两次同分钟构建也不会撞名；
- `gh release create`：一键建 Release 并上传全部 part + 校验文件。成功输出 Release 链接。

### 6.9 参数速查表

| 参数/步骤 | 值 | 作用 |
|---|---|---|
| 自动触发路径 | `uup.zip` | 上传即构建 |
| 手动触发 | `workflow_dispatch` | 测试/重试 |
| 权限 | `contents: write` | 创建 Release 必需 |
| 并发 | 同组单跑、不取消 | 防互相抢占 |
| 运行环境 | `ubuntu-latest` | GitHub 托管 runner |
| 磁盘清理 | .NET/GHC/Android/CodeQL | 腾约 10GB |
| 分块 | `1900M`、`-d`、`-a 2` | <2GB、part-00 式命名 |
| 校验 | SHA256（对完整 ISO） | 拼接后核对 |
| Release 标签 | `v北京时间-run编号` | 永不撞名 |

---

## 七、核心修复原理：genisoimage 转发脚本

### 7.1 要解决的矛盾

Win11 的 `install.wim` 常超过 4GB，而 `genisoimage` 默认遵守 ISO9660 规范——单文件上限 4GiB-1，超限直接报错：

```text
File ISODIR/sources/install.wim is larger than 4GiB-1.
-allow-limited-size was not specified. There is no way do represent this file size. Aborting.
Failed to create ISO image
```

同时，**UDF 不能去掉**：Windows 挂载大于 4GB 单文件的 ISO 时，需要 UDF 记录文件真实大小。删了 UDF 打包能"成功"，但挂载后 install.wim 显示不完整，装到一半报错。

所以需要：**>4GB 支持 + UDF 保留**，二者兼得。

### 7.2 为什么备选方案都不行

| 方案 | 失败原因 |
|---|---|
| 原版 genisoimage 默认跑 | 4GiB-1 上限，硬失败 |
| 用 xorriso 替代 | uup 脚本传 `--udf`，xorriso **硬性不支持**，报 `Unsupported option '--udf'`。这是 xorriso 的规格限制，不是写法问题 |
| sed 替换脚本内的 genisoimage | 新版 uup 脚本的打包命令藏在**运行时解压**的 `uup-converter-wimlib` 工具包内，sed 在解压前执行，根本够不着 |
| 在 uup 目录里伪造 genisoimage | 工具包调用的是 PATH 里的 `/usr/bin/genisoimage`，得改系统层 |

### 7.3 最终方案：系统层转发脚本

```bash
sudo mv /usr/bin/genisoimage /usr/bin/genisoimage.orig
printf '#!/bin/bash\nexec /usr/bin/genisoimage.orig -allow-limited-size "$@"\n' | sudo tee /usr/bin/genisoimage
sudo chmod +x /usr/bin/genisoimage
```

原理拆解：

1. **改名**：原版程序挪为 `genisoimage.orig`；
2. **替身**：原路径放一个两行脚本：

```bash
#!/bin/bash
exec /usr/bin/genisoimage.orig -allow-limited-size "$@"
```

3. **注入参数**：`-allow-limited-size` 让 ISO9660 目录项记录一个受限的占位大小，真实大小由 UDF 承载——两个文件系统配合，单文件 >4GB 也能正确读出；
4. **透传参数**：`"$@"` 把 uup 脚本传来的所有参数（含 `--udf`、输出路径等）原样转给原版，UDF 保留；
5. **exec**：用原版进程替换脚本进程，退出码和信号原样传递，调用方完全无感；
6. **物理拦截**：不依赖知道调用点在哪——哪怕调用藏在运行时解压的工具包深处、或用 `mkisofs` 别名（所以 mkisofs 也一并处理），只要走 `/usr/bin`，必然被注入；
7. **零侵入 uup 脚本**：不修改任何下载的脚本内容，升级 uup 脚本也不用改工作流。

### 7.4 自检方法

Patch 步骤末尾的 `cat /usr/bin/genisoimage` 会在日志打印转发脚本内容。排障时第一步就是确认这里显示的是：

```text
#!/bin/bash
exec /usr/bin/genisoimage.orig -allow-limited-size "$@"
```

若显示的是原版二进制乱码或别的内容，说明 Patch 没生效。

---

## 八、下载 Release 产物

### 8.1 Release 命名规则

```text
vYYYYMMDD-HHMM-runN
```

例：`v20261025-0508-run3` = 北京时间 2026-10-25 05:08 构建的第 3 次运行。

好处：每次构建唯一标签、永不撞名、历史全保留，想装回旧版随时下载。

### 8.2 每个 Release 里有什么

- `win11.iso.part-00`、`win11.iso.part-01`、`win11.iso.part-02` …（约 1.9GB/个，通常 3~4 个）
- `SHA256SUMS.txt`

⚠️ **所有 part 一个都不能少**；part 不能直接当 ISO 用；`SHA256SUMS.txt` 是对完整 ISO 的哈希。

### 8.3 ⭐ 方式 A：迅雷下载（强烈推荐，实测最快）

1. Release 页面**右键**某个 part 文件 → **复制链接地址**。链接形如（`***` 为脱敏占位符，实际是你自己的用户名/仓库名）：

```text
https://github.com/***/***/releases/download/vYYYYMMDD-HHMM-runN/win11.iso.part-00
```

2. 打开**迅雷** → 新建任务 → 粘贴链接 → 开始下载；
3. 每个 part 重复一遍（通常 3~4 个），SHA256SUMS.txt 浏览器直接下即可。

💡 实测经验：
- **迅雷 + 挂梯子**速度最快；
- 只开迅雷不挂梯子也能下，只是慢一些；
- 迅雷自动多线程，比浏览器裸下快得多；挂不挂按需选择。

### 8.4 方式 B：浏览器直接下载

直接点文件名即可。挂梯子更稳更快，不挂也能下。适合文件少、网络好的时候。

### 8.5 方式 C：镜像加速前缀（可选，失效就退回 A/B）

把复制好的链接**最前面**拼上镜像前缀。

原链接：

```text
https://github.com/***/***/releases/download/vYYYYMMDD-HHMM-runN/win11.iso.part-00
```

替换后（整串前加一个前缀，只加一次）：

```text
https://gh-proxy.com/https://github.com/***/***/releases/download/vYYYYMMDD-HHMM-runN/win11.iso.part-00
```

丢进迅雷或浏览器下载。

⚠️ 注意：
- 前缀只加在**最前面**，不要改原链接内部、不要叠加多个前缀；
- 镜像站是公益服务，随时可能失效；返回 HTML/403/404 就不要死磕，退回方式 A。

### 8.6 下载注意事项

- part 文件名、后缀不要改；
- 不要漏 `SHA256SUMS.txt`；
- 下载后先看大小是否合理（每块约 1.9GB，最后一块可能小些）；某块明显偏小=下载中断，重下；
- 所有 part 下全后再拼接。

---

## 九、把分块拼回完整 ISO

把**所有** `win11.iso.part-*` 放进**同一个文件夹**（`SHA256SUMS.txt` 也放进来，下一步校验用）。

### 9.1 Windows CMD（推荐）

文件夹空白处 **Shift + 右键** → "在此处打开命令窗口"（或地址栏输入 `cmd` 回车）：

```bat
copy /b win11.iso.part-00+win11.iso.part-01+win11.iso.part-02 win11.iso
```

- 有几个 part 就用 `+` 连几个；
- **顺序从小到大（00→01→02→…），一个不能少、不能乱**；
- 成功提示"已复制 1 个文件"，文件夹里出现 `win11.iso`。

4 个 part 的例子：

```bat
copy /b win11.iso.part-00+win11.iso.part-01+win11.iso.part-02+win11.iso.part-03 win11.iso
```

### 9.2 Windows PowerShell（备选）

```powershell
Get-Content win11.iso.part-* -Encoding Byte -Raw | Set-Content win11.iso -Encoding Byte
```

能用，但大文件可能吃内存。**推荐优先 `copy /b`**。

### 9.3 Linux / macOS

```bash
cat win11.iso.part-* > win11.iso
```

通配符按字典序匹配（part-00、part-01、part-02…），顺序天然正确。

### 9.4 拼接问题对照

| 现象 | 原因 | 处理 |
|---|---|---|
| 输出文件很小 | 命令里漏了 `+`，只拼了一块 | 检查命令拼上全部 part |
| 提示找不到文件 | 文件名不对/不在同一目录 | `dir`（Win）/`ls`（Linux）确认 |
| 校验哈希不一致 | 顺序乱/缺块/某块损坏 | 按 00→01→02 重拼；补下坏块 |
| 提示"已复制 1 个文件"但无法挂载 | 拼接顺序错误或混入了旧版本 part | 清空文件夹重新下载同一次 Release 的全部 part |

---

## 十、校验文件完整性（强烈建议，别跳过）

拼接后必须校验。不校验就刻盘，可能装到一半报错且难定位。

### 10.1 第 1 步：看官方哈希

打开 `SHA256SUMS.txt`，内容一行：

```text
a1b2c3d4e5f6……（64位十六进制串）  win11.iso
```

前 64 位十六进制就是官方 SHA256。

### 10.2 第 2 步：算本地哈希

**Windows CMD：**
```bat
certutil -hashfile win11.iso SHA256
```

**Windows PowerShell：**
```powershell
Get-FileHash win11.iso -Algorithm SHA256
```

**Linux / macOS（txt 和 iso 同目录，自动比对）：**
```bash
sha256sum -c SHA256SUMS.txt
```
输出 `win11.iso: OK` 即一致。

### 10.3 第 3 步：对比与处理

- **完全一致 ✅** → 文件完好，可安装；
- **不一致 ❌** → 下载/拼接坏了：**不要装**。重下可疑 part → 重新拼接 → 重新校验，直到一致。

⚠️ 关键认知：`SHA256SUMS.txt` 是对**完整 ISO**的哈希，**不是**对 part 的。必须先拼接出 `win11.iso` 再校验。哈希不一致时优先怀疑：某块没下全、某块下载损坏、拼接顺序错。

---

## 十一、安装 Windows

校验通过后任选：

### 11.1 挂载 ISO 直接安装（原地升级）

双击 `win11.iso` 挂载（Win10/11 自带虚拟光驱）→ 运行 `setup.exe` → 按提示操作。适合当前系统能正常启动、想原地升级的场景。

### 11.2 Rufus 做 U 盘启动盘

[Rufus](https://rufus.ie)（[rufus.ie](https://rufus.ie)）：选 U 盘 → 选 `win11.iso` → 开始写入。Windows 下最流行，简单可靠。

### 11.3 Ventoy 做万能启动盘

[Ventoy](https://www.ventoy.net)（[ventoy.net](https://www.ventoy.net)）：把 U 盘做成启动盘，之后任何 ISO 直接拷进 U 盘就能启动。适合经常装系统、一个 U 盘放多个镜像。

### 11.4 安装前提醒

- U 盘需 **8GB 以上**；
- 备份重要数据；
- 确认机器满足 Win11 硬件要求（本项目不绕过检查）；
- 确认 BIOS/UEFI 启动设置；
- 只使用校验通过的 ISO。

---

## 十二、日常维护与版本更新

### 12.1 以后想装新版本：零代码修改

1. uupdump 下载**新版本**的 Linux 转换脚本 zip；
2. 改名为 `uup.zip`；
3. Add file → Upload files → 拖入 → Commit（**覆盖旧的**）。

完事，自动开跑。**不用改任何文件、任何字符串**：Release 标签按"构建时间+运行编号"自动生成永不撞名，每次构建在 Releases 留独立版本，历史可回溯。

### 12.2 几条铁律

- ⚠️ **不要删除 `uup.zip`**：它是构建原材料，删了自动触发没了、构建也没输入。换版本=覆盖上传；
- 改了 `main.yml` 不会自动跑（故意设计）：去 Actions → build-iso → Run workflow 手动触发；
- Public 仓库 Actions 免费不限时长，但别滥用——一次构建 50 分钟，无意义重跑浪费公共资源；
- 旧 Release 可手动删除，建议保留最近几个版本以便回滚。

---

## 十三、故障排查与日志判读

### 13.1 红叉处理总流程

1. Actions → 点进失败的那次运行；
2. 找到红叉的步骤，展开日志；
3. **复制报错原文文本**（不要只截图，文本才能搜、才能发给 AI）；
4. 对照 13.2 的错误表；
5. 修复后 Re-run jobs（没改代码）或重新上传 uup.zip（换了输入）；
6. 仍搞不定：把日志原文 + 失败步骤名发给 AI（ChatGPT / Claude / Kimi 均可），一般秒定位。

### 13.2 常见错误对照表

| 日志特征 | 原因 | 处理 |
|---|---|---|
| `File ... larger than 4GiB-1` | 转发脚本未生效，原版 genisoimage 在跑 | 看 Patch 步骤的 `cat` 输出是否为转发脚本；确认没误改回原版 |
| `Unsupported option '--udf'`（xorriso） | 误用 xorriso 替代 | 改回 genisoimage 转发脚本方案 |
| `No space left on device` | 磁盘不足 | 看 Free disk space 步骤 `df -h` 输出；必要时再删别的目录 |
| `Error: ISO file was not generated!` | 上一步打包失败，或路径变了 | 看 Run UUP dump script 的完整日志 |
| `unzip: cannot find uup.zip` | 没上传或文件名不对 | 确认仓库根目录有全小写 `uup.zip` |
| `gh release create` 权限/404 | 缺 `contents: write` 或资产不存在 | 检查 permissions；看 ls -lh 是否列出了 part 文件 |
| 下载散件失败/超时 | uupdump 官方源抽风、网络 | **最常见**，直接 Re-run jobs 重跑；仍失败换个时间 |
| `apt-get` 失败 | 网络/apt 源 | 重跑 |
| 镜像前缀下载返回 HTML/403/404 | 镜像失效 | 退回迅雷+梯子，不要死磕 |
| 拼接后哈希不一致 | 下载损坏/缺块/顺序错 | 重下坏块→重拼→重校验 |
| 日志顶部 Node.js 20 / Ubuntu 26 警告 | 平台公告 | **无害**，忽略 |

### 13.3 排障决策树

```text
构建红叉
  │
  ├─ 失败在 Checkout？→ 平台问题，重跑
  │
  ├─ 失败在 Free disk space？→ 极少见，重跑
  │
  ├─ 失败在 Install dependencies？→ apt 网络问题 → 重跑
  │
  ├─ 失败在 Patch genisoimage？→ 极少见，重跑
  │
  ├─ 失败在 Run UUP dump script？
  │    ├─ 出现 4GiB-1 → Patch 未生效 → 检查 cat 输出
  │    ├─ 出现 --udf unsupported → 误用 xorriso → 改回转发脚本
  │    ├─ 出现 No space → 磁盘不足 → 增强清理
  │    ├─ 下载散件失败/超时 → UUP 源抽风 → Re-run 重跑（最常见）
  │    └─ 其他 → 复制日志发 AI
  │
  ├─ 失败在 Locate and split ISO？→ ISO 没生成 → 看前一步
  │
  ├─ 失败在 Publish to Release？→ 权限/资产 → 检查 permissions 和 ls 输出
  │
  └─ 绿勾 ✅ → Releases 下载 → 拼接 → 校验 → 安装
```

### 13.4 重跑的正确姿势

Actions → 那次运行 → 右上角 **Re-run jobs**。适用于：UUP 源临时抽风、网络波动、runner 临时问题——这些情况代码没错，重跑即可。改代码或换输入（新 uup.zip）则不需要 Re-run，直接推新提交。

### 13.5 日志复制技巧

- 点开失败步骤 → 展开完整日志；
- 复制**报错前后若干行**（错误类型、文件路径、命令、退出码），别只复制最后一行；
- 文本优先于截图；
- 日志太长就抓关键片段。

---

## 十四、FAQ

**Q：构建失败（红叉）怎么办？**  
展开日志复制报错原文 → 对照第十三节 → 重跑一次（最常见是 UUP 源抽风）。仍失败把日志发给 AI。

**Q：ISO 大于 4GB 会打包失败吗？**  
不会。转发脚本自动注入 `-allow-limited-size`，彻底解决了 `File ... is larger than 4GiB-1` 报错，同时保留 UDF，Windows 挂载时 install.wim 完整可读。

**Q：为什么不用 xorriso 替代 genisoimage？**  
uup 脚本打包传 `--udf`，xorriso 硬性不支持 UDF，会报 `Unsupported option '--udf'`。UDF 是 Windows 正确挂载大 install.wim 的关键，不能删。

**Q：为什么不用 sed 直接改 uup 脚本里的打包命令？**  
新版 uup 脚本的打包命令藏在运行时解压的 `uup-converter-wimlib` 工具包内，sed 在解压前运行，够不着。所以最终方案改成在 `/usr/bin` 系统层物理拦截。

**Q：为什么我改了 main.yml 没有自动开跑？**  
故意的。工作流只监听 `uup.zip` 的变动——改代码不该白白烧 50 分钟构建。想测试就去 Actions → Run workflow 手动触发。

**Q：Actions 要钱吗？**  
本仓库是 Public，GitHub Actions 完全免费、不限时长。Private 仓库有月度额度限制。

**Q：构建产物去哪了？仓库 Code 页面怎么看不到 ISO？**  
全在云端构建，Code 页面**不会出现** ISO（太大）。成品只出现在 Releases 页面。

**Q：part 文件是什么？能直接用吗？**  
不能直接用的。GitHub Release 单文件限制 2GB，所以用 `split -b 1900M` 把 ISO 切成约 1.9GB 的小块。按第九节拼回 `win11.iso` 就能用了。

**Q：SHA256SUMS.txt 是对 part 还是完整 ISO 的？**  
对完整 ISO。所以必须先拼接再校验。

**Q：哈希校验不一致怎么办？**  
不要安装。检查 part 是否下全 → 重下可疑 part → 重新拼接 → 重新校验。

**Q：下载很慢怎么办？**  
优先方式 A（迅雷+梯子实测最快）；方式 B 浏览器直接下；方式 C 镜像前缀是可选兜底，失效就退回 A。

**Q：拼接命令里有几个 part 就连几个？**  
对。`copy /b` 后按 00→01→02 顺序用 `+` 连，一个不能少不能乱。

**Q：日志顶部的 Node.js 20 / Ubuntu 26 黄色警告要管吗？**  
不用管，是 Actions 平台公告，无害，不影响构建。

**Q：可以给朋友用吗？**  
可以。把本仓库地址发给对方，从"准备工作"照做；或对方 Fork 你的仓库自己跑。公开前记得脱敏（见第十六节）。

**Q：以后换新版本要改代码吗？**  
完全不用。uupdump 下新 zip → 改名 uup.zip → 覆盖上传，自动开跑。

**Q：为什么不要删除 uup.zip？**  
它是构建输入和触发器。删了既不能自动触发也没东西可构建。换版本=覆盖。

**Q：一次构建多久？**  
通常 40~55 分钟，大头是下载散件和合成 wim，取决于 UUP 源速度和 runner 负载。

**Q：Release 标签会重复吗？**  
不会。`v北京时间-run编号`，run_number 单调递增，同分钟也不会撞。

---

## 十五、维护者检查清单

每次构建后快速自检：

- [ ] Actions 页面出现绿勾 ✅
- [ ] Releases 新增 `vYYYYMMDD-HHMM-runN` 版本
- [ ] 该版本含**全部** `win11.iso.part-*` + `SHA256SUMS.txt`
- [ ] part 数量与大小合理（通常 3~4 个，每个约 1.9GB，最后一块可能小些）
- [ ] Patch 步骤日志中 `cat /usr/bin/genisoimage` 显示转发脚本
- [ ] Run UUP 步骤日志出现 `extents written (xxxx MB)` + `Done.`
- [ ] 本地拼接后哈希与 SHA256SUMS.txt 一致
- [ ] 挂载 ISO 能打开、`sources/install.wim` 大小完整
- [ ] README/日志无真实个人信息、无硬编码 Token
- [ ] `uup.zip` 仍在仓库根目录
- [ ] 旧 Release 按需清理（建议保留最近几个）

---

## 十六、隐私、安全与合规

### 16.1 隐私

- 本文档所有 `***` 均为脱敏占位符（用户名/仓库名）；
- 示例链接统一 `https://github.com/***/***/releases/download/vYYYYMMDD-HHMM-runN/win11.iso.part-00`；
- 公开仓库的 Actions 日志人人可见——工作流中不要 echo 敏感信息（当前 main.yml 无此问题）；
- Fork 给别人用时，建议 README 继续保留占位符写法（"你的用户名/你的仓库名"），不写真实账号。

### 16.2 安全

- `GITHUB_TOKEN` 由 Actions 自动注入，**绝不硬编码任何 Token**；
- 工作流权限最小化，仅需 `contents: write`；
- `sudo` 修改 `/usr/bin` 只发生在一次性临时 runner 上，构建完机器销毁，不影响你的本地电脑，也不留后门；
- 不从不可信来源下载 uup.zip（zip 内的脚本会在云端执行）；
- 镜像加速站是第三方服务，注意其可见你的下载链接（含 Release 路径）。

### 16.3 合规

- Windows ISO 版权归 Microsoft，遵守微软许可协议；
- 仅个人合法用途，不盗版、不未授权分发；
- 本项目不绕过 Windows 11 硬件检查。

---

## 附录 A：成功日志样例（脱敏）

打包阶段成功标志：

```text
Creating ISO image...
I: -input-charset not specified, using utf-8 (detected in locale settings)
...
Total translation table size: 2048
Total rockridge attributes bytes: 0
Total directory bytes: 0
Path table size(bytes): 10
Max brk space used fc000
2807089 extents written (5482 MB)
Done.
```

切块与校验阶段：

```text
== Searching for generated ISO files ==
5748918272 ./29695.1001_PROFESSIONAL_X64_ZH-CN.ISO
== Found ISO: ./29695.1001_PROFESSIONAL_X64_ZH-CN.ISO ==
-rw-r--r-- 1 runner runner   76 ... SHA256SUMS.txt
-rw-r--r-- 1 runner runner 1.9G ... win11.iso.part-00
-rw-r--r-- 1 runner runner 1.9G ... win11.iso.part-01
-rw-r--r-- 1 runner runner 1.7G ... win11.iso.part-02
```

发布阶段最后一行：

```text
https://github.com/***/***/releases/tag/vYYYYMMDD-HHMM-runN
```

判读：`Done.` = 打包完成；`extents written (5482 MB)` = ISO 约 5482MB；`Found ISO` = 找到产物；part 列表 = 切块成功；Release 链接 = 发布成功。

## 附录 B：失败日志样例与原因对照（脱敏）

### B.1 genisoimage 4GB 限制（Patch 未生效时）

```text
File ISODIR/sources/install.wim is larger than 4GiB-1.
-allow-limited-size was not specified. There is no way do represent this file size. Aborting.
Failed to create ISO image
Error: Process completed with exit code 1.
```
→ 检查 Patch 步骤的 `cat /usr/bin/genisoimage` 输出。

### B.2 xorriso 不支持 UDF（误用替代方案时）

```text
xorriso 1.5.6 : RockRidge filesystem manipulator, libburnia project.
...
xorriso : FAILURE : -as mkisofs: Unsupported option '--udf'
xorriso : aborting : -abort_on 'FAILURE' encountered 'FAILURE'
```
→ 改回 genisoimage 转发脚本方案，UDF 不能删。

### B.3 其他

| 日志 | 原因 | 处理 |
|---|---|---|
| `No space left on device` | 磁盘不足 | 增强 Free disk space |
| `Error: ISO file was not generated!` | 上一步打包失败 | 看 Run UUP dump script 日志 |
| `gh: ...` 权限错误 | 缺 `contents: write` | 补 permissions |
| `unzip: cannot find uup.zip` | 没上传/文件名错 | 确认根目录全小写 uup.zip |

## 附录 C：命令速查表

| 场景 | 命令 |
|---|---|
| Windows 拼接 | `copy /b win11.iso.part-00+win11.iso.part-01+win11.iso.part-02 win11.iso` |
| PowerShell 拼接 | `Get-Content win11.iso.part-* -Encoding Byte -Raw \| Set-Content win11.iso -Encoding Byte` |
| Linux/macOS 拼接 | `cat win11.iso.part-* > win11.iso` |
| Windows 校验 | `certutil -hashfile win11.iso SHA256` |
| PowerShell 校验 | `Get-FileHash win11.iso -Algorithm SHA256` |
| Linux 校验 | `sha256sum -c SHA256SUMS.txt` |
| 手动触发构建 | Actions → build-iso → Run workflow |
| 失败后重跑 | 那次运行 → 右上角 Re-run jobs |

## 附录 D：术语表

| 术语 | 解释 |
|---|---|
| UUP dump | 从微软更新服务器下载 Windows 散件并生成转换脚本的第三方站点 |
| UUP 散件 | 微软更新服务器的增量文件，合成 install.wim 的原料 |
| install.wim | Windows 安装映像，常大于 4GB |
| wimlib | 处理 WIM 文件的工具集（wimtools） |
| genisoimage | ISO 打包工具，原生支持 UDF，默认有 4GiB-1 单文件限制 |
| mkisofs | genisoimage 的别名/同源工具，故一并 Patch |
| xorriso | 另一 ISO 工具，支持大文件但不支持 `--udf` |
| UDF | 通用光盘文件系统，Windows 挂载 >4GB 单文件 ISO 必需 |
| ISO9660 | 传统光盘文件系统，单文件上限 4GiB-1 |
| `-allow-limited-size` | genisoimage 参数：ISO9660 目录记受限占位大小，真实大小由 UDF 承载 |
| 转发脚本 | 替换原程序、自动注入参数再透传全部参数的两行 shell 脚本 |
| `split` | 文件切块工具，`-b 1900M -d -a 2` = 1.9GB/块、数字后缀、定长 2 位 |
| SHA256 | 文件哈希算法，用于完整性校验 |
| Release | GitHub 发布页，存放二进制资产（单文件限 2GB） |
| runner | GitHub Actions 的临时构建机器 |
| workflow / 工作流 | GitHub Actions 的自动化定义（main.yml） |
| `workflow_dispatch` | 网页手动触发工作流的事件 |
| `copy /b` | Windows 二进制拼接命令 |
| `certutil -hashfile` | Windows 文件哈希命令 |
| `sha256sum -c` | Linux 按校验文件自动比对哈希 |

## 附录 E：变更记录模板

```markdown
## 变更记录

### vYYYYMMDD-HHMM-runN
- 构建版本（如 29695.1001）/ 语言 / 架构：
- ISO 大小 / 分块数量：
- 校验：SHA256 与 SHA256SUMS.txt 一致 ✅
- 备注：

### 历史修复
- 修复 genisoimage 4GB 限制：转发脚本 + -allow-limited-size（放弃 sed、放弃 xorriso）
- Release 标签自适应：v北京时间-run编号
- 切块：split -b 1900M -d -a 2
- 校验：SHA256SUMS.txt
```

## 附录 F：脱敏规则

| 原始信息类型 | 脱敏写法 |
|---|---|
| GitHub 用户名 | `***` |
| 仓库名 | `***` |
| Release 路径 | `***/***` |
| 具体 Release 标签（示例中） | `vYYYYMMDD-HHMM-runN` |
| runner 工作路径 | `/home/runner/work/***/***` |
| Token | 不出现，只用 `${{ secrets.GITHUB_TOKEN }}` |

公开文档建议：保留 `***` 占位符，或用"你的用户名/你的仓库名"这类说法；Fork 后如要写自己仓库信息，也建议先检查全文再发布。

---

> **总结**  
> 已跑通的最终方案一句话：**genisoimage 转发脚本（自动注入 `-allow-limited-size`）+ 保留 UDF + `split -b 1900M` 切块 + SHA256 校验 + 北京时间 run 编号自动发 Release**。  
> 以后换版本只需覆盖上传 `uup.zip`，零代码修改。  
> 遇红叉：复制日志原文 → 对照第十三节 → 重跑 → 发给 AI。
