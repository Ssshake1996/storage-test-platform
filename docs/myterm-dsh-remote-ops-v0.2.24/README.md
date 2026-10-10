# dsh-remote-ops v0.2.24

这是 [Ssshake1996/myterm](https://github.com/Ssshake1996/myterm) 插件 `@dsh/remote-ops` 的 v0.2.24 改动。当前云端代理只能写入本仓库，对 `Ssshake1996/myterm` 的 push 和 fork 都返回 403（`cursor[bot]` 没有写权限），所以 GitHub Release 没有从这里发出。

补丁相对 myterm `main`（`7c16f69`，已发布的 v0.2.23）。在 myterm 检出上：

```powershell
git apply --check .\dsh-remote-ops-v0.2.24.patch
git apply .\dsh-remote-ops-v0.2.24.patch
npm --prefix integrations/dsh-remote-ops ci
npm --prefix integrations/dsh-remote-ops run check
powershell -NoProfile -ExecutionPolicy Bypass `
  -File scripts/release-dsh-remote-ops.ps1 -Version 0.2.24
```

`npm run check` 已在代理上通过：61 项后端/状态/传输、31 项客户端、9 项契约、smoke。没有 DSH Web profile，没有做 3080 验收，也没有连接真实设备。

行为说明见 `release-notes.md`。还没做的优化见 `followups.md`。
