# Codex-Pro

`Codex-Pro` 是基于 OpenAI Codex CLI 的自用增强版，重点改的是终端 TUI 底部状态栏和本地使用体验。

上游项目仍然是 OpenAI Codex CLI；本仓库不是官方发布渠道。如果你只想安装官方 Codex，请使用官方 npm/Homebrew/Release。这里的说明只针对 `Shuran-z/Codex-Pro` 这个仓库。

当前定制基线：OpenAI Codex CLI `0.132.0`（上游标签 `rust-v0.132.0`）。

## 功能

- 底部状态栏显示当前模型、额度和上下文等信息。
- 额度显示加入进度条和剩余百分比，例如 `5h [████░░░░] 80% left`。
- 额度进度条改为蓝色，不再使用醒目的红色。
- 额度信息会近似实时刷新：登录 ChatGPT/OpenAI 账号后，TUI 后台约每 60 秒请求一次额度数据。
- 后台额度刷新有并发保护，慢请求不会堆积。
- 支持从底部状态栏切换模型：启用鼠标模式后，点击左下角模型名称即可打开模型选择器。
- 模型选择器沿用原有快捷键，按 `Esc` 退出。
- 默认保持终端文本复制可用，不会强行开启鼠标捕获。
- CI 已简化为适合本 fork 的 Rust 格式检查和 CLI 构建检查。

## 下载

目前本仓库还没有预编译 Release 包。你可以用下面两种方式下载源码。

### 方式一：clone 仓库

```bash
git clone https://github.com/Shuran-z/Codex-Pro.git
cd Codex-Pro
```

### 方式二：下载 ZIP

打开仓库页面：

```text
https://github.com/Shuran-z/Codex-Pro
```

点击 `Code` -> `Download ZIP`。

也可以直接下载 main 分支源码：

```text
https://github.com/Shuran-z/Codex-Pro/archive/refs/heads/main.zip
```

## 构建

进入 Rust workspace：

```bash
cd codex-rs
```

建议使用 Rust `1.93.0` 或更新版本。Linux 上通常还需要 OpenSSL 开发包：

```bash
# Debian / Ubuntu
sudo apt-get update
sudo apt-get install -y pkg-config libssl-dev
```

然后构建：

```bash
cargo build -p codex-cli --bin codex --release
```

构建完成后的二进制在：

```text
codex-rs/target/release/codex
```

可以直接运行：

```bash
./target/release/codex
```

## 安装到本机

如果你已经通过 npm 安装了官方 Codex，并想把本 fork 编译出来的二进制替换进去，可以参考下面流程。

先确认当前 `codex` 来自哪里：

```bash
which codex
codex --version
```

构建本 fork：

```bash
cd codex-rs
cargo build -p codex-cli --bin codex --release
```

然后把 `target/release/codex` 复制到你本机 npm Codex 的 native binary 路径。不同机器路径可能不同，本机这次使用的路径是：

```text
~/.npm-global/lib/node_modules/@openai/codex/node_modules/@openai/codex-linux-x64/vendor/x86_64-unknown-linux-musl/codex/codex
```

建议先备份原二进制：

```bash
cp -p <installed-codex-binary> <installed-codex-binary>.backup
install -m 755 codex-rs/target/release/codex <installed-codex-binary>.new
mv <installed-codex-binary>.new <installed-codex-binary>
```

替换后验证：

```bash
codex --version
```

## 使用

普通启动：

```bash
codex
```

默认模式保留终端拖选复制能力。

如果想点击底部模型名称切换模型，启动时开启鼠标模式：

```bash
CODEX_TUI_MOUSE=1 codex
```

然后点击底部状态栏里的模型名称打开模型选择器，按 `Esc` 退出。

注意：标准终端鼠标上报是整个窗口级别开关，不支持“只监听最下面一行”。因此本 fork 默认不开启鼠标捕获，避免影响上方聊天记录复制。开启 `CODEX_TUI_MOUSE=1` 后，部分终端需要按住 `Shift` 才能拖选复制文本。

## CI

当前启用的 GitHub Actions workflow：

```text
.github/workflows/codex-pro-ci.yml
```

它会检查：

- `cargo fmt --check`
- `cargo check -p codex-cli --bin codex`

上游不适合本 fork 的 workflow 已移动到：

```text
.github/workflows.upstream-disabled
```

## License

本仓库继承上游许可证，详见 [LICENSE](LICENSE)。
