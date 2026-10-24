# Win11 ISO 自动构建器

上传一个 `uup.zip`，GitHub 云端自动下载散件并打包出 Windows 11 安装 ISO。
**自己电脑什么都不用装**，只需要浏览器和一个 GitHub 账号。

---

## 一、这个项目是干嘛的

- 从 [uupdump.net](https://uupdump.net)（微软官方更新服务器的第三方下载器）选好想装的 Windows 版本
- 把它的 Linux 转换脚本包（一个 zip）改名为 `uup.zip` 传到本仓库
- GitHub 云端自动：下载系统散件 → 合成 `install.wim` → 打包成 ISO → 切成 &lt;2GB 的分块 → 发到 Releases
- 你只管从 Releases 下载分块、拼回、校验、安装

全程约 **40~55 分钟**，全程免费（Public 仓库的 GitHub Actions 不限量）。

---

## 二、准备工作

1. 注册/登录 GitHub 账号（免费）
2. 把本仓库 **Fork** 到自己账号下，或直接在网页导入复制一份（Import repository）
3. 不用装任何软件，不用配置任何东西

---

## 三、构建步骤（第一次 + 每一次都一样）

### 第 1 步：去 uupdump 选版本

1. 打开 [uupdump.net](https://uupdump.net)
2. 选 **x64**（或你要的架构）→ 选 Windows 11 版本（如 24H2 / 25H2，挑最新的）
3. 选语言（中文简体是 **Chinese (Simplified)**）
4. 版本勾选（一般全选默认即可，专业版 Professional 必含）
5. 下载方式选 **Download the conversion script (Linux)**，得到一个 zip 文件

### 第 2 步：改名

把下载的 zip **改名为 `uup.zip`**。

⚠️ 必须一字不差：全小写、8 个字符 `u u p . z i p`。名字不对触发不了自动构建。

### 第 3 步：上传到本仓库

1. 回到本仓库首页 → **Add file → Upload files**
2. 把 `uup.zip` 拖进去 → 点 **Commit changes**
3. **上传成功的一瞬间构建就自动开始了**（工作流监听了 uup.zip 的变动）：
   - 去 **Actions** 标签页能看到一个黄色圆点 🟡 在转，就是在跑了
   - 如果没自动跑（个别情况）：Actions → 左侧 **build-iso** → 右侧 **Run workflow** → 点绿色按钮手动触发

### 第 4 步：等 40~55 分钟

Actions 页面里的状态圆点：
- 🟡 黄点 = 进行中
- ✅ 绿勾 = 成功
- ❌ 红叉 = 失败（处理方法见最后 FAQ）

成功后到仓库右侧 **Releases** 领成品。

---

## 四、下载成品（Releases 页面）

每次构建成功，Releases 都会多出一个新版本，名字形如 `v20260513-1430-run3`，含义是：**2026年5月13日 14:30 构建，第 3 次运行**。每个版本里包含：

- `win11.iso.part-00`、`win11.iso.part-01`、`win11.iso.part-02` …（ISO 分块，每个约 1.9GB）
- `SHA256SUMS.txt`（校验文件，用来核对下载有没有损坏）

⚠️ **所有 part 文件一个都不能少**，缺一个就拼不回来。

### 下载方式（按推荐程度排序）

#### ⭐ 方式 A：迅雷下载（实测最快，强烈推荐）

1. 在 Release 页面，**右键**某个 part 文件 → **复制链接地址**。
   链接长这样：

   https://github.com/你的用户名/你的仓库名/releases/download/v20260513-1430-run3/win11.iso.part-00

2. 打开**迅雷** → **新建任务** → 粘贴链接 → 开始下载
3. 每个分块都这样复制链接、粘贴、下载一遍（通常 3~4 个）

&gt; 💡 **实测经验：迅雷 + 挂梯子速度最快；只开迅雷不挂梯子也能下，只是慢一些。**
&gt; 迅雷会自动多线程加速，比浏览器裸下快得多。挂不挂梯子都可行，按需选择。

#### 方式 B：浏览器直接下载

直接点文件名就能下。挂梯子更稳更快，不挂也能下。

#### 方式 C：镜像加速前缀（可选，失效就退回 A/B）

把复制好的链接**在最前面**拼上一个镜像前缀：

原链接：
```
https://github.com/你的用户名/你的仓库名/releases/download/v20260513-1430-run3/win11.iso.part-00
```

替换后（整串前面加 `https://gh-proxy.com/`）：
```
https://gh-proxy.com/https://github.com/你的用户名/你的仓库名/releases/download/v20260513-1430-run3/win11.iso.part-00
```

把这个新链接丢进迅雷或浏览器下载即可。
⚠️ 镜像站是公益服务，随时可能失效；失效了就退回方式 A（迅雷+梯子），不要死磕。

---

## 五、把分块拼回完整 ISO

把**所有** `win11.iso.part-*` 文件放进**同一个文件夹**，然后根据你的系统选一种：

### Windows（推荐，CMD 命令）

在文件夹空白处按住 Shift + 右键 → "在此处打开命令窗口"（或地址栏输入 `cmd` 回车），执行：

```
copy /b win11.iso.part-00+win11.iso.part-01+win11.iso.part-02+win11.iso.part-03 win11.iso
```

- 有几个 part 就用 `+` 连几个，**顺序从小到大，一个不能少、不能乱**
- 回车后会显示"已复制 1 个文件"，文件夹里出现 `win11.iso`

### Windows（PowerShell 版）

```powershell
Get-Content win11.iso.part-* -Encoding Byte -Raw | Set-Content win11.iso -Encoding Byte
```

（推荐还是用上面的 `copy /b`，简单可靠。）

### Linux / macOS

```bash
cat win11.iso.part-* &gt; win11.iso
```

---

## 六、校验文件完整性（强烈建议，别跳过）

拼完后必须校验，确保下载/拼接过程没有损坏。不校验就刻盘，可能装到一半报错。

### 第 1 步：看官方哈希

打开下载的 `SHA256SUMS.txt`，里面长这样（一行）：

```
a1b2c3d4e5f6……（64位十六进制串）  win11.iso
```

前面那串 64 位的值就是官方哈希。

### 第 2 步：算你拼出来的文件的哈希

**Windows（CMD）：**
```
certutil -hashfile win11.iso SHA256
```

**Windows（PowerShell）：**
```powershell
Get-FileHash win11.iso -Algorithm SHA256
```

**Linux / macOS（把 txt 和 iso 放同一文件夹，自动比对）：**
```bash
sha256sum -c SHA256SUMS.txt
```

### 第 3 步：对比

- 两串哈希**完全一致** = 文件完好 ✅，可以安装
- **不一致** = 下载坏了 ❌，重新下载坏的那个 part 文件，重新拼接、重新校验

---

## 七、安装 Windows

校验通过后任选：

1. **原地升级/直接安装**：双击 `win11.iso` 挂载（Win10/11 自带虚拟光驱）→ 运行里面的 `setup.exe`
2. **做 U 盘启动盘**：
   - [Rufus](https://rufus.ie)（Windows 下最流行，简单）
   - [Ventoy](https://ventoy.net)（把 U 盘做成"万能启动盘"，以后 ISO 直接拷进去就能启动）
   - 需要 8GB 以上的 U 盘

---

## 八、以后想装新版本怎么办？（零代码修改）

1. 去 uupdump 下载**新版本**的 Linux 转换脚本 zip
2. 改名为 `uup.zip`
3. 上传到本仓库**覆盖**旧的（Add file → Upload files → 拖入 → Commit）

完事，自动开跑。**不用改任何文件、任何字符串**：
- Release 版本号按"构建时间+运行编号"自动生成，永不撞名
- 每次构建在 Releases 里留下独立版本，历史可回溯，想装回旧版也行

---

## 九、常见问题（FAQ）

**Q：构建失败（红叉）怎么办？**
点进那次运行 → 点红叉的步骤 → 展开日志 → 把报错文字**原样复制**发给 AI（ChatGPT / Claude / Kimi 都行），一般秒定位。最常见原因是 uupdump 官方源临时抽风，**重跑一次**就好（Actions → 那次运行 → 右上角 Re-run jobs）。

**Q：ISO 大于 4GB 会打包失败吗？**
不会。本项目已改用 **xorriso**（`xorriso -as mkisofs` 完全兼容 genisoimage 参数且支持超大文件），彻底解决了旧版 genisoimage 的 `File ... is larger than 4GiB-1` 报错。

**Q：为什么我改了 main.yml 没有自动开跑？**
故意的。工作流只监听 `uup.zip` 的变动——改代码不该白白烧 50 分钟构建。想测试就去 Actions → **Run workflow** 手动触发。

**Q：Actions 要钱吗？**
本仓库是 Public，GitHub Actions **完全免费、不限时长**。

**Q：构建产物去哪了？仓库里怎么看不到 ISO？**
全在云端构建，仓库 Code 页面**不会出现** ISO（太大）。成品只出现在 **Releases** 页面。

**Q：part 文件是什么？能直接用吗？**
不能直接用的。因为 GitHub Release 单文件限制 2GB，所以把 ISO 切成约 1.9GB 的小块。按第五节拼回 `win11.iso` 就能用了。

**Q：可以给朋友用吗？**
可以。把本仓库地址发给对方，对方从"准备工作"一节开始照做即可；或者对方 Fork 你的仓库自己跑。

---

## 十、给看代码的人：本项目关键技术点

1. **大文件打包**（核心修复）：
   `sed -i 's/\bgenisoimage\b/xorriso -as mkisofs/g' files/converter_multi`
   运行 uupdump 脚本前，把脚本里的 genisoimage 调用全部换成 `xorriso -as mkisofs`，绕开 ISO9660 的 4GB 单文件上限。genisoimage 仍安装只是兜底，实际打包走 xorriso。

2. **版本号自适应**：
   `TAG="v$(TZ='Asia/Shanghai' date +%Y%m%d-%H%M)-run${{ github.run_number }}"`
   Release 标签 = 北京时间构建时刻 + Actions 运行编号，重复构建不冲突、历史不覆盖。

3. **自动触发**：
   `on: push: paths: ['uup.zip']` —— 只有上传/覆盖 uup.zip 才自动构建，改代码不触发。

4. **磁盘清理**：
   GitHub 托管跑器只有约 14GB 可用空间，构建前删掉 .NET/GHC/Android/CodeQL 等预装大头，为 6GB+ 的 ISO 腾地方。

5. **切块发布**：
   `split -b 1900M` 切成 &lt;2GB 分块绕开 GitHub Release 单文件 2GB 限制；`sha256sum` 输出 `SHA256SUMS.txt` 供用户校验。

---

&gt; 本 README 与 `main.yml` 配套使用。遇到问题：先翻 FAQ → 再看 Actions 日志 → 还不行把日志原文发给 AI 分析。
