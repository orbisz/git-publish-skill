# git-publish

将当前项目一键推送到 GitHub 远程仓库的 Claude Code skill。

## 功能

- 自动检测并初始化 Git 仓库（如果尚未初始化）
- 自动暂存并提交未保存的改动，生成规范的 commit message
- 自动检测当前分支并推送到远程仓库
- 推送失败时提供强制推送选项

## 安装

将本仓库克隆到 `.claude/skills/git-publish/` 目录下即可。

## 使用方式

在 Claude Code 中，进入任意项目目录，直接告诉 Claude：

- "帮我把这个项目推送到 GitHub"
- "push 到 https://github.com/user/repo.git"
- "上传到远程仓库"

Claude 会自动识别并调用此 skill，按以下流程执行：

1. **询问仓库地址** — 如果你还没提供，Claude 会先向你要 GitHub 仓库 URL
2. **初始化 Git**（如需） — 如果项目还未初始化 git，自动执行 `git init` + 初始提交 + 添加 remote
3. **处理未提交改动** — 自动暂存所有改动，基于 diff 生成 conventional commit 格式的提交信息
4. **推送到远程** — 自动检测当前分支，先尝试普通推送；失败时询问是否强制推送

## 支持的仓库地址格式

- `https://github.com/user/repo.git`
- `git@github.com:user/repo.git`
- `https://github.com/user/repo`

## 示例

```
用户: 把这个项目push到 https://github.com/myuser/my-project.git

Claude 会:
1. 检查 git 状态
2. 如需则 git init 并初始提交
3. 处理未提交改动，生成如 "feat: add user authentication module" 的提交信息
4. git push -u origin main
5. 报告推送结果
```
