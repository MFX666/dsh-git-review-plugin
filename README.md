# dsh-git-review-plugin

Orca 风格 Git 审查插件（DeepSeek Harness bundle）——从 `dsh-worktree-plugin` 拆分的独立插件：

1. **对话页「审查」标签（`conversation.view` `id: git-review` `order: 40`）**：
   - 变更 / 日志 / 比较 三标签：组合 diff（暂存/未暂存/未跟踪内联 diff）、按文件与 hunk 选择性暂存提交、推送（`--force-with-lease`）、日志/单提交详情/`ref..HEAD` 比较、blame 行级标注。
   - AI 生成提交说明：读取工作区 diff，走宿主 `llm` 运行时流式生成，自动剥除代码围栏。
   - 大体积产物保护：`*.map` / `*.min.js` 等噪音文件截断展示，不挤占源码与未跟踪目录的 diff 空间。
   - 打开任意对话即审查其工作树（`sessions.byId[sessionId].cwd`），无需 worktree 池。
2. **Host 远程服务（`rGitReview`）**：
   - `branches(root, refresh?)`、`status(path)`、`diff(path, file?, ignoreWhitespace?)`、`commit(path, message, files, hunks?)`、`push(path, forceWithLease?)`、`suggestCommitMessage(path)`、`log(path, {refRange?, file?, limit?})`、`showCommit(path, hash)`、`compare(path, ref)`、`blame(path, file)`、`applyPatch(path, patch, {cached?, reverse?})`。
   - 全部浏览器接口与当前是否打开 Session 解耦（无 `agent` 查找参数），git 动词只接受普通 `path`，不依赖 worktree 池/注册表。
3. **Agent 工具**：
   - `git_diff`、`git_commit`。

## 构建与自检

```bash
pnpm build
node --test test/*.test.mjs
```

## 契约与设计文档

- `docs/p0-git-review-contract.md` — 冻结的 wire 契约 v1（log `%x1f` 解析、blame 5000 行上限、applyPatch 路径逃逸校验、temp-index 提交）。
- `docs/idea-git-parity/` — 与 Codex/Orca 对齐的交互与视觉拆解。
