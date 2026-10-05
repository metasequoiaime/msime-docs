# Linux 使用指南

水杉 Linux 版的同一个安装包同时提供 Fcitx5 插件和 IBus 引擎，两者共用同一套引擎与设置，支持全拼、双拼、五笔、日语、韩语、粤拼、注音、越南文和藏文等方案，并带有设置窗口、手写、屏幕键盘、剪贴板历史和语音输入。本文按 [msime](https://github.com/metasequoiaime/msime) 主仓发布的 [linux-v0.10.0](https://github.com/metasequoiaime/msime/releases/tag/linux-v0.10.0) 整理；其他版本可能存在功能差异，请同时查看对应 Release。

已归档的 [msime-linux](https://github.com/metasequoiaime/msime-linux) 仓库（v0.8.2 及更早，仅 IBus）不再更新，它的包名、设置程序和配置文件位置都与本文不同，本文不适用于它。

## 下载与启用

在 [msime Releases](https://github.com/metasequoiaime/msime/releases) 中找标签以 `linux-v` 开头的版本。目前只提供 x86_64（64 位 Intel/AMD）：`msime-linux_0.10.0_amd64.deb` 用于 Debian 系，`msime-linux-0.10.0-1.x86_64.rpm` 用于 Fedora 等 DNF 系统，`msime-linux-0.10.0-linux-x86_64.tar.gz` 是给不经 apt 安装的 Debian 系系统用的同内容归档。`.deb` 在 Debian 12 环境构建，`.rpm` 面向 Fedora 44；两种包都依赖 IBus 与 Fcitx5，能否安装以包管理器的依赖检查为准。下载时一并下载 `SHA256SUMS` 核对校验值：

```sh
sha256sum -c SHA256SUMS --ignore-missing
sudo apt install ./msime-linux_0.10.0_amd64.deb
sudo dnf install ./msime-linux-0.10.0-1.x86_64.rpm
```

第二、三条按发行版二选一。归档的解压方法、需要自备的依赖和手动删除方式见 [Linux 宿主说明](https://github.com/metasequoiaime/msime/blob/linux-v0.10.0/platforms/linux/README.md#生成-linux-安装包)。

同一个 Release 还提供水杉拼音、水杉五笔、水杉日语、水杉越南语和水杉藏文这几个独立版本（包名 `msime-linux-pinyin`、`msime-linux-wubi`、`msime-linux-japanese`、`msime-linux-vietnamese`、`msime-linux-tibetan`），装在 `/opt/msime-linux-<版本>` 下，可与完整版同时安装和启用。日语、越南语、藏文版不带中文词库和手写。

**安装后还要做一次首次配置。** 安装本身不准备词库、也不替你选中输入法，完整版安装包也不附带主词库。打开应用列表中的「水杉输入法」，首次会进入配置页：勾选「词库不完整时从固定地址下载」，确认是否启用云候选，再点「开始配置」。也可以在终端运行：

```sh
msime-linux-setup --download
```

首次下载约 190 MB 词库，存到 `~/.local/share/msime-client/resources`。加 `--no-cloud-candidates` 可在配置时关闭云候选。独立版本运行各自的命令，例如 `msime-linux-wubi-setup --download`。

配置完成后会把水杉加入正在运行的输入法框架的列表，两者都在运行时以 Fcitx5 为准：Fcitx5 中显示为「水杉输入法」（英文界面为「MSIME」），IBus 与 GNOME 输入源中显示为「Metasequoia 水杉输入法」。自动加入失败时，输出会说明原因和手动步骤：Fcitx5 用 `fcitx5-configtool` 把「水杉输入法」加入当前输入法组；IBus 先执行 `ibus restart`，再在桌面输入源里添加「Metasequoia 水杉输入法」。切换过去后在文本框输入 `nihao`，按空格上屏。

如果在首次配置之前就选中了水杉，Fcitx5 会在输入面板上提示先完成首次配置，IBus 下会打开设置窗口或发出桌面通知引导配置。

## 输入与快捷键

| 操作 | 方法 |
| --- | --- |
| 切换输入方案 | IBus 属性菜单或 Fcitx5 状态区的「输入方案」，或设置的「输入」页 |
| 中文／英文 | 单独轻按 `Shift`，或 `Ctrl + Space`、`Ctrl + Alt + Space`；可在「快捷键 → 输入模式切换」改为单击 Ctrl 或「不使用」 |
| 选择候选 | 空格选高亮项，数字键（主键盘与小键盘）或鼠标选对应项 |
| 移动高亮 | 方向键 |
| 翻页 | `-` / `=`、`,` / `.`、Shift + Tab / Tab、Page Up / Page Down；方括号翻页可在设置中开启 |
| 提交原始输入／取消 | `Enter` / `Esc` |
| 中英文标点 | `Ctrl + .` |
| 半角／全角 | `Ctrl + Shift + Space` |
| 简体／繁体输出 | `Ctrl + Shift + F` |
| 独立英文模式 | `Ctrl + Shift + E` 进入或退出 |
| 重启输入法 | `Ctrl + Shift + Alt + R`：IBus 下重启 IBus，Fcitx5 下只重置水杉插件，不影响其他输入法 |

“以词定字”默认开启：有候选时 `[` / `]` 上屏高亮词的首／末字，可在设置中改为 `-` / `=`。单独的 `Shift` 只有在按下后没有按其他键、且很快松开时才切换中英文，组合键中的 Shift 不触发切换。

## 设置与辅助码

从应用列表打开「水杉输入法」，或运行 `msime-linux-settings`，也可以从 IBus 属性菜单或 Fcitx5 状态区的「设置…」进入。修改保存后两个框架都会自动重新加载，无需重启。应用列表中该图标的右键菜单（桌面支持时）还能直接打开词库、手写、屏幕键盘等面板，见下文“桌面工具”。

「输入」页可以调整中英文默认状态、选词与翻页、云候选、中英混输、快捷模式、模糊音、辅助码和调频。全拼与双拼的辅助码默认开启，分别默认使用自然码和蓝天小雨点，另可选首右2.0、首右plus、小鹤和加加。模糊音默认关闭，首次开启时一次选中全部 11 条规则，可再删减。调频可选关闭、一次置顶、折半调频、线性调频或一次置前（默认）。

Fcitx5 的中英文、简繁、工具栏等入口挂在输入法状态区，需要桌面提供托盘；以 `fcitx5 --disable notificationitem` 启动的发行版（例如 Omarchy）看不到这些入口，但输入不受影响，设置仍可从应用列表打开。Omarchy 上的主题同步、状态栏插件和 Hyprland 规则见 [Omarchy 说明](https://github.com/metasequoiaime/msime/blob/linux-v0.10.0/platforms/linux/README.md#omarchy)。

## 快捷模式与混合候选

中文模式下按以下快捷键进入单次输入模式，可在「输入 → 快捷模式」中分别关闭：

| 快捷键 | 用途 |
| --- | --- |
| `Shift + U` | Unicode：输入十六进制码位，例如 `4e00` 或 `+1f600`，空格上屏，`Shift + 数字` 选词 |
| `Shift + T` | 日期 `rq` / `riqi` / `date`、时间 `sj` / `shijian` / `time`、星期 `xq` / `xingqi` / `week` |
| `Shift + K` | 输入编码调用快捷短语 |
| `Shift + E` / `Shift + M` | 搜索 Emoji／颜文字，支持全拼、简拼、双拼或英文关键词 |
| `Shift + J` | 超级简拼，每个字母作为简拼；双拼按当前方案转换声母 |
| `Shift + Y` / `Shift + R` | 临时英文／日语罗马字，上屏后回到中文 |
| `Shift + V` | 计算与数字：算式、数字转大写或金额、日期（默认关闭） |

指令（`/`）和名单（`@`）模式同样默认关闭，需在快捷模式中开启。

中英混输默认开启，拼写达到“触发字符数”（1～8，默认 5）后在候选中补充英文单词；“emoji 混输”和“颜文字混输”默认关闭。独立英文模式（`Ctrl + Shift + E`）与临时英文不同：上屏后仍按英文匹配，需再按一次退出。

## 联网功能与语音

**云候选默认开启**，首次配置时可以取消：输入停顿 500 毫秒后，把正在输入的拼写经 HTTPS 发给 Google 输入工具（`inputtools.google.com`）换回一条额外候选，已上屏文本、词库和词频不会发送。之后可在「输入 → 候选与联想 → 云候选」关闭。

**候选翻译在新装时默认使用「水杉账号」**，会把当前页的中文候选词发到 `api.msime.app`。安装包会为已登录用户注册一个本机匿名水杉账号供它使用。不想发送时，在「标点与翻译」页的“翻译服务”改选关闭、腾讯云机器翻译、小牛翻译或自定义 DeepLX 兼容服务。**匿名使用统计默认开启**，不含输入内容，可在「关于 → 许可与隐私 → 匿名使用统计」关闭。AI 联想默认关闭，需在「AI 辅助」页填入自己的服务与凭据。服务商凭据保存在本机仅当前用户可读写的文件中。

语音由随包安装的语音服务处理，首次配置时已为当前用户启用。在「语音输入」页选择识别服务：默认的豆包等云端服务需要填写自己的凭据，选择「本地模型（离线）」则在设置页下载识别模型，不联网。快捷键：长按右 Alt 录音、松开结束；`Ctrl + F9` 按一次开始、再按一次结束；长按期间按空格锁定录音，`Esc` 取消。长按 Ctrl+Win、长按右 Ctrl+右 Alt 默认关闭，可在“语音快捷键”中开启。

录音需要 `parec`、`pw-cat` 或 `arecord` 之一（`pulseaudio-utils`、`pipewire-bin` 或 `alsa-utils`）。豆包实时识别还需要 websockets 15 或更高版本；Debian 12 等发行版自带的 `python3-websockets` 过旧，可为当前用户安装随包列出的版本后重启语音服务：

```sh
python3 -m pip install --user --break-system-packages -r /usr/share/msime-client/requirements-voice.txt
systemctl --user restart msime-linux-voice.service
```

## 桌面工具

应用列表中「水杉输入法」的动作、IBus 属性菜单的“桌面工具”，或命令 `msime-linux-settings --panel <面板>` 都能打开以下面板：手写识别板（`handwriting`）、屏幕键盘（`keyboard`）、表情与符号（`emoji`）、本地剪贴板（`clipboard`）、词库（`dictionary`）、语音输入（`voice`）、云词库（`cloud-dictionary`）和云剪贴板（`cloud-clipboard`）。

手写使用随包的本地模型识别。剪贴板历史默认关闭，在「剪贴板」页开启后只在本机记录文本，关闭时清空已保存记录。云剪贴板需要登录水杉账号，只上传你在面板里明确选择的文字。

## 数据、升级与卸载

配置、学习数据、偏好和剪贴板历史位于 `~/.config/msime-client`（设置了 `XDG_CONFIG_HOME` 时位于该目录下），自行下载的词库位于 `~/.local/share/msime-client/resources`。可在「维护与诊断 → 数据目录」把数据移到另一个空目录。独立版本的目录名为 `msime-client-<版本>`。

「关于 → 版本与更新 → 检查更新」只查找 `linux-v` 正式版，发现新版本时显示校验值并通过「前往下载」打开 Release 页面，不会自动安装。升级时用同样的命令安装新包，用户数据和配置保持不变：IBus 下通常切换一次窗口即换到新版本；Fcitx5 会提示执行 `fcitx5 -r` 或注销后重新登录。如果词库是用 `--download` 自行下载的，新版本需要新词库时会弹出通知「水杉输入法词库需要更新」，按提示运行 `msime-linux-setup --update --download`。

卸载使用 `sudo apt remove msime-linux` 或 `sudo dnf remove msime-linux`（独立版本换成对应包名）。卸载会为已登录用户停用水杉的后台服务，并把水杉从输入法列表中移除；之后执行 `fcitx5 -r`、`ibus restart` 或注销以完全退出。卸载不删除用户数据，确认不再需要时手动删除 `~/.config/msime-client` 和 `~/.local/share/msime-client`。

## 故障排查与反馈

- **列表里没有水杉**：确认已完成首次配置；已配置过的可运行 `msime-linux-setup --register` 重新加入列表，或按上文用 `fcitx5-configtool`、`ibus restart` 手动添加。
- **选中后不能输入**：先按 `Ctrl + Shift + Alt + R`，或在「维护与诊断 → 输入法服务」点「重启」；仍不行就在另一个文本编辑器对比，记录桌面、Wayland/X11、输入法框架和所选方案。
- **语音无法录制**：提示“未找到录音工具”时安装上文的录音工具；豆包提示需要 websockets 时按上文安装。也可以先改用本地模型，区分采集问题和服务问题。
- **在线结果不出现**：检查对应开关和凭据，先确认本地候选正常。
- **需要日志**：在「维护与诊断 → 诊断日志」开启后复现，日志文件是数据目录下的 `diagnostic.log`，不记录按键、输入内容或候选文本。

请向 [msime Issues](https://github.com/metasequoiaime/msime/issues) 反馈，并提供发行版、桌面环境、会话类型（Wayland/X11）、Fcitx5 或 IBus、输入法版本、安装方式与复现步骤。日志和截图先去除凭据及真实输入。各项联网功能的完整数据流见 [linux-v0.10.0 的网络请求与数据流向说明](https://github.com/metasequoiaime/msime/blob/linux-v0.10.0/PRIVACY.md)。
