# Linux 使用指南

水杉 Linux 版的同一個安裝套件同時提供 Fcitx5 外掛和 IBus 引擎，兩者共用同一套引擎與設定，支援全拼、雙拼、五筆、日語、韓語、粵拼、注音、越南文和藏文等方案，並附帶設定視窗、手寫、螢幕小鍵盤、剪貼簿歷史和語音輸入。本文按 [msime](https://github.com/metasequoiaime/msime) 主儲存庫發布的 [linux-v0.10.0](https://github.com/metasequoiaime/msime/releases/tag/linux-v0.10.0) 整理；其他版本可能存在功能差異，請同時檢視對應 Release。

已歸檔的 [msime-linux](https://github.com/metasequoiaime/msime-linux) 儲存庫（v0.8.2 及更早，僅 IBus）不再更新，它的套件名稱、設定程式和設定檔位置都與本文不同，本文不適用於它。

本頁採用臺灣繁體中文用語；引號內的應用程式選項、程式碼、檔名與範例保留原文，方便對照實際介面。

## 下載與啟用

在 [msime Releases](https://github.com/metasequoiaime/msime/releases) 中找標籤以 `linux-v` 開頭的版本。目前只提供 x86_64（64 位元 Intel/AMD）：`msime-linux_0.10.0_amd64.deb` 用於 Debian 系，`msime-linux-0.10.0-1.x86_64.rpm` 用於 Fedora 等 DNF 系統，`msime-linux-0.10.0-linux-x86_64.tar.gz` 是給不經 apt 安裝的 Debian 系系統使用的同內容封存檔。`.deb` 在 Debian 12 環境建置，`.rpm` 以 Fedora 44 為目標；兩種套件都依賴 IBus 與 Fcitx5，能否安裝以套件管理器的依賴檢查為準。下載時一併下載 `SHA256SUMS` 核對校驗值：

```sh
sha256sum -c SHA256SUMS --ignore-missing
sudo apt install ./msime-linux_0.10.0_amd64.deb
sudo dnf install ./msime-linux-0.10.0-1.x86_64.rpm
```

第二、三條按發行版二選一。封存檔的解壓方法、需要自備的依賴和手動刪除方式見 [Linux 宿主說明](https://github.com/metasequoiaime/msime/blob/linux-v0.10.0/platforms/linux/README.md#生成-linux-安装包)。

同一個 Release 還提供水杉拼音、水杉五筆、水杉日語、水杉越南語和水杉藏文這幾個獨立版本（套件名稱 `msime-linux-pinyin`、`msime-linux-wubi`、`msime-linux-japanese`、`msime-linux-vietnamese`、`msime-linux-tibetan`），安裝在 `/opt/msime-linux-<版本>` 下，可與完整版同時安裝和啟用。日語、越南語、藏文版不含中文詞庫和手寫。

**安裝後還要做一次首次設定。** 安裝本身不準備詞庫、也不會替你選取輸入法，完整版安裝套件也不附帶主詞庫。開啟應用程式清單中的「水杉输入法」，首次會進入設定頁：勾選「词库不完整时从固定地址下载」，確認是否啟用雲端候選字，再點「开始配置」。也可以在終端機執行：

```sh
msime-linux-setup --download
```

首次下載約 190 MB 詞庫，存到 `~/.local/share/msime-client/resources`。加上 `--no-cloud-candidates` 可在設定時關閉雲端候選字。獨立版本執行各自的命令，例如 `msime-linux-wubi-setup --download`。

設定完成後會把水杉加入正在執行的輸入法框架的清單，兩者都在執行時以 Fcitx5 為準：Fcitx5 中顯示為「水杉输入法」（英文介面為「MSIME」），IBus 與 GNOME 輸入來源中顯示為「Metasequoia 水杉输入法」。自動加入失敗時，輸出會說明原因和手動步驟：Fcitx5 用 `fcitx5-configtool` 把「水杉输入法」加入目前的輸入法群組；IBus 先執行 `ibus restart`，再在桌面輸入來源中新增「Metasequoia 水杉输入法」。切換過去後在文字框輸入 `nihao`，按空格送出文字。

如果在首次設定之前就選取了水杉，Fcitx5 會在輸入面板上提示先完成首次設定，IBus 下會開啟設定視窗或發出桌面通知引導設定。

## 輸入與快捷鍵

| 操作 | 方法 |
| --- | --- |
| 切換輸入方案 | IBus 屬性選單或 Fcitx5 狀態區的「输入方案」，或設定的「输入」頁 |
| 中文／英文 | 單獨輕按 `Shift`，或 `Ctrl + Space`、`Ctrl + Alt + Space`；可在「快捷键 → 输入模式切换」改為單擊 Ctrl 或「不使用」 |
| 選擇候選 | 空格選反白項，數字鍵（主鍵盤與小鍵盤）或滑鼠選對應項 |
| 移動反白 | 方向鍵 |
| 翻頁 | `-` / `=`、`,` / `.`、Shift + Tab / Tab、Page Up / Page Down；方括號翻頁可在設定中開啟 |
| 提交原始輸入／取消 | `Enter` / `Esc` |
| 中英文標點 | `Ctrl + .` |
| 半形／全形 | `Ctrl + Shift + Space` |
| 簡體／繁體輸出 | `Ctrl + Shift + F` |
| 獨立英文模式 | `Ctrl + Shift + E` 進入或退出 |
| 重新啟動輸入法 | `Ctrl + Shift + Alt + R`：IBus 下重新啟動 IBus，Fcitx5 下只重設水杉外掛，不影響其他輸入法 |

“以词定字”預設開啟：有候選時 `[` / `]` 送出反白詞的首／末字，可在設定中改為 `-` / `=`。單獨的 `Shift` 只有在按下後沒有按其他鍵、且很快放開時才切換中英文，組合鍵中的 Shift 不觸發切換。

## 設定與輔助碼

從應用程式清單開啟「水杉输入法」，或執行 `msime-linux-settings`，也可以從 IBus 屬性選單或 Fcitx5 狀態區的「设置…」進入。修改儲存後兩個框架都會自動重新載入，無需重新啟動。應用程式清單中該圖示的右鍵選單（桌面支援時）還能直接開啟詞庫、手寫、螢幕小鍵盤等面板，見下文“桌面工具”。

「输入」頁可以調整中英文預設狀態、選詞與翻頁、雲端候選字、中英混輸、快捷模式、模糊音、輔助碼和調頻。全拼與雙拼的輔助碼預設開啟，分別預設使用自然碼和藍天小雨點，另可選首右2.0、首右plus、小鶴和加加。模糊音預設關閉，首次開啟時一次選取全部 11 條規則，可再刪減。調頻可選关闭、一次置顶、折半调频、线性调频或一次置前（預設）。

Fcitx5 的中英文、簡繁、工具列等入口掛在輸入法狀態區，需要桌面提供系統匣；以 `fcitx5 --disable notificationitem` 啟動的發行版（例如 Omarchy）看不到這些入口，但輸入不受影響，設定仍可從應用程式清單開啟。Omarchy 上的主題同步、狀態列外掛和 Hyprland 規則見 [Omarchy 說明](https://github.com/metasequoiaime/msime/blob/linux-v0.10.0/platforms/linux/README.md#omarchy)。

## 快捷模式與混合候選

中文模式下按以下快捷鍵進入單次輸入模式，可在「输入 → 快捷模式」中分別關閉：

| 快捷鍵 | 用途 |
| --- | --- |
| `Shift + U` | Unicode：輸入十六進位碼位，例如 `4e00` 或 `+1f600`，空格送出，`Shift + 数字` 選詞 |
| `Shift + T` | 日期 `rq` / `riqi` / `date`、時間 `sj` / `shijian` / `time`、星期 `xq` / `xingqi` / `week` |
| `Shift + K` | 輸入編碼呼叫快捷片語 |
| `Shift + E` / `Shift + M` | 搜尋 Emoji／顏文字，支援全拼、簡拼、雙拼或英文關鍵詞 |
| `Shift + J` | 超級簡拼，每個字母作為簡拼；雙拼按目前方案轉換聲母 |
| `Shift + Y` / `Shift + R` | 臨時英文／日語羅馬字，送出後回到中文 |
| `Shift + V` | 計算與數字：算式、數字轉大寫或金額、日期（預設關閉） |

指令（`/`）和名單（`@`）模式同樣預設關閉，需在快捷模式中開啟。

中英混輸預設開啟，拼寫達到“触发字符数”（1～8，預設 5）後在候選中補充英文單字；“emoji 混输”和“颜文字混输”預設關閉。獨立英文模式（`Ctrl + Shift + E`）與臨時英文不同：送出文字後仍按英文匹配，需再按一次退出。

## 聯網功能與語音

**雲端候選字預設開啟**，首次設定時可以取消：輸入停頓 500 毫秒後，把正在輸入的拼寫經 HTTPS 傳送給 Google 輸入工具（`inputtools.google.com`）換回一條額外候選，已送出的文字、詞庫和詞頻不會傳送。之後可在「输入 → 候选与联想 → 云候选」關閉。

**候選翻譯在全新安裝時預設使用「水杉账号」**，會把目前頁面的中文候選詞傳送到 `api.msime.app`。安裝套件會為已登入的使用者註冊一個本機匿名水杉帳號供它使用。不想傳送時，在「标点与翻译」頁的“翻译服务”改選关闭、腾讯云机器翻译、小牛翻译或自定义 DeepLX 兼容服务。**匿名使用統計預設開啟**，不含輸入內容，可在「关于 → 许可与隐私 → 匿名使用统计」關閉。AI 聯想預設關閉，需在「AI 辅助」頁填入自己的服務與憑證。服務商憑證儲存在本機僅目前使用者可讀寫的檔案中。

語音由隨套件安裝的語音服務處理，首次設定時已為目前使用者啟用。在「语音输入」頁選擇辨識服務：預設的豆包等雲端服務需要填寫自己的憑證，選擇「本地模型（离线）」則在設定頁下載辨識模型，不聯網。快捷鍵：長按右 Alt 錄音、放開結束；`Ctrl + F9` 按一次開始、再按一次結束；長按期間按空格鎖定錄音，`Esc` 取消。長按 Ctrl+Win、長按右 Ctrl+右 Alt 預設關閉，可在“语音快捷键”中開啟。

錄音需要 `parec`、`pw-cat` 或 `arecord` 之一（`pulseaudio-utils`、`pipewire-bin` 或 `alsa-utils`）。豆包即時辨識還需要 websockets 15 或更高版本；Debian 12 等發行版內建的 `python3-websockets` 過舊，可為目前使用者安裝隨套件列出的版本後重新啟動語音服務：

```sh
python3 -m pip install --user --break-system-packages -r /usr/share/msime-client/requirements-voice.txt
systemctl --user restart msime-linux-voice.service
```

## 桌面工具

應用程式清單中「水杉输入法」的動作、IBus 屬性選單的“桌面工具”，或命令 `msime-linux-settings --panel <面板>` 都能開啟以下面板：手寫辨識板（`handwriting`）、螢幕小鍵盤（`keyboard`）、表情與符號（`emoji`）、本機剪貼簿（`clipboard`）、詞庫（`dictionary`）、語音輸入（`voice`）、雲端詞庫（`cloud-dictionary`）和雲端剪貼簿（`cloud-clipboard`）。

手寫使用隨套件的本機模型辨識。剪貼簿歷史預設關閉，在「剪贴板」頁開啟後只在本機記錄文字，關閉時清空已儲存的記錄。雲端剪貼簿需要登入水杉帳號，只上傳你在面板中明確選擇的文字。

## 資料、升級與解除安裝

設定、學習資料、偏好和剪貼簿歷史位於 `~/.config/msime-client`（設定了 `XDG_CONFIG_HOME` 時位於該目錄下），自行下載的詞庫位於 `~/.local/share/msime-client/resources`。可在「维护与诊断 → 数据目录」把資料移到另一個空目錄。獨立版本的目錄名稱為 `msime-client-<版本>`。

「关于 → 版本与更新 → 检查更新」只尋找 `linux-v` 正式版，發現新版本時顯示校驗值並透過「前往下载」開啟 Release 頁面，不會自動安裝。升級時用同樣的命令安裝新套件，使用者資料和設定保持不變：IBus 下通常切換一次視窗即換到新版本；Fcitx5 會提示執行 `fcitx5 -r` 或登出後重新登入。如果詞庫是用 `--download` 自行下載的，新版本需要新詞庫時會跳出通知「水杉输入法词库需要更新」，按提示執行 `msime-linux-setup --update --download`。

解除安裝使用 `sudo apt remove msime-linux` 或 `sudo dnf remove msime-linux`（獨立版本換成對應套件名稱）。解除安裝會為已登入的使用者停用水杉的背景服務，並把水杉從輸入法清單中移除；之後執行 `fcitx5 -r`、`ibus restart` 或登出以完全結束。解除安裝不刪除使用者資料，確認不再需要時手動刪除 `~/.config/msime-client` 和 `~/.local/share/msime-client`。

## 故障排查與回報

- **清單中沒有水杉**：確認已完成首次設定；已設定過的可執行 `msime-linux-setup --register` 重新加入清單，或按上文用 `fcitx5-configtool`、`ibus restart` 手動新增。
- **選取後無法輸入**：先按 `Ctrl + Shift + Alt + R`，或在「维护与诊断 → 输入法服务」點「重启」；仍不行就在另一個文字編輯器對比，記錄桌面、Wayland/X11、輸入法框架和所選方案。
- **語音無法錄製**：提示“未找到录音工具”時安裝上文的錄音工具；豆包提示需要 websockets 時按上文安裝。也可以先改用本機模型，區分擷取問題和服務問題。
- **線上結果不出現**：檢查對應開關和憑證，先確認本機候選正常。
- **需要日誌**：在「维护与诊断 → 诊断日志」開啟後重現問題，日誌檔是資料目錄下的 `diagnostic.log`，不記錄按鍵、輸入內容或候選文字。

請向 [msime Issues](https://github.com/metasequoiaime/msime/issues) 回報，並提供發行版、桌面環境、會話類型（Wayland/X11）、Fcitx5 或 IBus、輸入法版本、安裝方式與重現步驟。日誌和截圖先去除憑證及真實輸入。各項聯網功能的完整資料流見 [linux-v0.10.0 的網路請求與資料流向說明](https://github.com/metasequoiaime/msime/blob/linux-v0.10.0/PRIVACY.md)。
