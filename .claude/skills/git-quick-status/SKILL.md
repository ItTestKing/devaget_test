---
name: git-quick-status
description: 当用户想快速了解 Git 仓库当前状态、查看分支与远程同步情况、未提交改动概览，或提到 git status/仓库状态/看一下改动/快速状态 时激活。汇总当前分支、远程同步状态、暂存/未暂存/未跟踪文件，输出一份简洁的只读状态摘要，不修改任何内容。
---

# Git Quick Status

快速生成一份当前 Git 仓库的只读状态摘要,帮助用户在提交、切换分支或开始新工作前快速对齐现状。

## 何时使用

- 用户问"现在改了什么 / 仓库什么状态 / 跟远程同步了吗"
- 提交或切分支前想先看一眼概览
- 需要一份结构化的状态报告而不是原始 `git status` 输出

## 执行步骤

1. **确认在 Git 仓库内**

   ```bash
   git rev-parse --is-inside-work-tree
   ```

   若不是仓库,直接告知用户并停止。

2. **并行收集信息**(可在一条命令中合并执行,减少往返):

   ```bash
   git branch --show-current          # 当前分支
   git status --short --branch        # 改动概览 + 与远程的领先/落后
   git log --oneline -5               # 最近 5 条提交
   git stash list                     # 是否有 stash
   ```

3. **判断远程同步状态**

   - `git status -sb` 输出首行的 `[ahead N]` / `[behind M]` 表示领先/落后远程。
   - 若无 upstream,提示用户该分支尚未关联远程。

## 输出格式

用简洁的中文摘要呈现,例如:

```
分支:dev/test → origin/dev/test(领先 0 / 落后 0)
改动:2 个已暂存,1 个未暂存,1 个未跟踪
最近提交:083d8a1 Initial commit
Stash:无
```

随后用一句话给出建议(如"工作区干净,可以安全切换分支"或"有未提交改动,建议先提交或 stash")。

## 约束

- **只读**:本 skill 绝不执行 `add`、`commit`、`checkout`、`reset` 等会修改仓库的命令。
- 若用户在看完状态后想提交,引导其使用 `git-commit` 流程。
