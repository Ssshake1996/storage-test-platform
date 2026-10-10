# dsh-remote-ops v0.2.24

## 变更

- `remote_terminal_send` 新增 `autoConfirm`（默认 `false`）和 `confirmPattern`。启用后，若本次输出末尾匹配 `(y/n)`（默认正则 `\(y\/n\)\s*$`），同一次工具调用写入 `y\n` 并继续等待，最多 3 次。
- 新增 `autoQuitMore`（默认 `false`）。仅当输出最后一行仍包含 `--More--` 时写入 `q`，再等待 300ms 读取剩余输出。末尾已经是确认提示时不发送 `q`，避免把 `q` 打进下一条命令。
- 返回前检测参数错误画面（换行、缩进 `^`、下一行 `[参数=?]`，正则 `\n\s+\^\s*\n\s*\[.*\=.*\]`）。命中后发送一次 SIGINT 清行，等待 500ms 读取恢复输出，并在工具结果末尾追加 `[auto-sigint: command line cleared]`。
- 该标记只存在于工具结果里，不写入终端滚动缓冲，续读游标也不包含它。`completion` 仍为 `unknown`。
- 结果增加 `autoConfirmed`、`autoMoreQuit`、`autoSigint`。系统提示要求只对用户已经要求执行的操作打开 `autoConfirm`。

## 自动化验证

- `npm run check`：通过。
- 61 项后端/状态/传输测试、31 项生产客户端行为测试、9 项契约测试和静态 smoke 全部通过。
- 新增回归覆盖：双重 `(y/n)` 在一次发送内回答且第 3 次停止；默认关闭时不写 `y`；非法 `confirmPattern` 在写入前拒绝；`--More--` 后继续确认；历史分页行不再次发送 `q`；参数错误画面发送 SIGINT，标记不进入缓冲区；SSH backend 的 `startSend` 路径同样回答确认并清行。

## 本机 3080 验收

- 本次发布环境没有 DSH Web profile，未启动 `dsh web`，未安装到 web profile，也未连接真实存储设备 CLI。
- 不能把单元测试里的假终端输出写成 3080 页面验收或真机成功。

## 已知限制

- 自动确认和退出分页只处理本次等待窗口里已经出现的输出。设备慢于 `quietMs`（默认 700ms）时，提示还没出现，调用方仍要 `remote_terminal_read`。
- 确认应答固定为 `y\n`，分页退出固定为 `q`。只认 CR 的 CLI 需要后续的 `confirmText`。
- 参数错误清行对每一次 `remote_terminal_send` 生效，没有单独开关。快捷按钮不走这条路径。
- 只扫描本次输出最后 8KB。SIGINT 清的是当前行；如果误匹配正在运行的前台程序，会中断它。
- `remote_terminal_batch` 还没有透传这三个参数。
