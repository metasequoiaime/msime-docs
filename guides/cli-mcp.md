# 命令行与 MCP

桌面版安装包带有 `msime-mcp`，并通常另提供同一个程序的 `msime` 命令。macOS 的 Homebrew、macOS DMG 和 Linux 安装包会把它放到 `PATH`；Windows 版可直接运行输入法安装目录 `server` 下的 `msime-mcp.exe`。

## 用命令行查候选

查看当前输入方案对一串编码给出的本地候选：

```sh
msime expand nihao
```

每行依次输出名次、候选文字、编码、来源和词频权重。需要机器读取的 JSON 时：

```sh
msime expand nihao --json
msime expand nihao zhongguo --json
```

多串输入时，`--json` 每行对应一串编码。也可以从标准输入批量读取，空行和 `#` 开头的行会跳过：

```sh
printf 'nihao\nzhongguo\n' | msime expand - --json
```

`--scheme quanpin`、`--scheme shuangpin` 或 `--scheme wubi` 可以临时指定方案，`--limit 50` 可以调整最多返回的候选数。`expand` 每次读取当前设置；它只查询本地词典，不包含云候选、AI 候选或前文上下文。

## 运行完整输入会话

`session` 使用当前运行时选项创建完整的 Host API 会话，逐行读取 JSON 请求，并逐行输出 transition JSON。返回的 `value.view` 包含当前组字、候选和光标位置，`value.commit` 是这次操作产生的上屏文字。

```sh
printf '%s\n' \
  '{"op":"focus","focused":true}' \
  '{"op":"text","value":"nihao"}' \
  '{"op":"command","value":"next-candidate"}' \
  '{"op":"command","value":"finish"}' \
  | msime session
```

支持的请求包括：

| 请求 | 作用 |
| --- | --- |
| `{"op":"focus","focused":true}` | 聚焦或失焦会话 |
| `{"op":"character","value":"n"}` | 输入一个 ASCII 按键 |
| `{"op":"text","value":"nihao"}` | 连续输入 ASCII 按键 |
| `{"op":"command","value":"backspace"}` | 执行编辑命令；也支持 `left`、`right`、`cancel`、`finish`、`next-page`、`previous-page` 等 |
| `{"op":"select","generation":1,"index":0}` | 按返回的会话代次和候选下标选词 |

每一行都是 `{ "ok": true, "value": ... }` 或 `{ "ok": false, "error": ... }`。输入来自终端时，`session` 和 `expand` 自动允许读取本地词库；被脚本或其他程序调用时，请显式加 `--allow-dictionary-read`。

## MCP

不带子命令运行 `msime-mcp` 会通过标准输入输出提供 MCP 服务。设置页的「连接 AI 助手」可以自动写入支持的客户端配置，也可以手动配置：

```json
{
  "mcpServers": {
    "msime": {
      "command": "msime-mcp"
    }
  }
}
```

命令行也能直接调用同一套 MCP 工具：

```sh
msime-mcp tools
msime-mcp call lookup_candidates '{"code":"nihao"}'
msime-mcp prompt diagnose
```

默认只提供只读工具。`--allow-write` 才会提供偏好、快捷短语和皮肤的修改工具；`--allow-dictionary-read` 才会提供读取用户词库的工具。两个开关都必须由用户在设置页授权后写进助手配置，不要把凭据或真实输入放进命令行参数、脚本和日志。

## 其他平台

Android 和 iOS 的发布包是原生移动端输入法，不包含桌面 CLI、`msime-mcp` 或 MCP stdio 服务；HarmonyOS 当前仍是开发中的宿主，也没有可供用户安装的 CLI/MCP 包。需要使用这些接口时，请安装 macOS、Linux 或 Windows 桌面版，并以对应平台指南中的安装路径为准。

更多参数可运行 `msime --help` 或 `msime-mcp --help` 查看。
