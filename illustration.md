可以，下面是完整复盘（问题 + 处理 + 全部命令与输出）。

**问题是什么**
- 你本地 `main` 和远程 `origin/main` 都各自有新提交，分支发生了分叉。
- 所以直接 `git push -u origin main` 被拒绝（non-fast-forward），Git 防止你覆盖远程历史。

**为什么会出现 `no rebase in progress`**
- 你执行了 `git rebase --continue`，但当时并没有正在进行的 rebase 流程，所以报：
  - `fatal: no rebase in progress`

**怎么解决的**
- 先把远程最新提交拉下来并做 rebase：`git pull --rebase origin main`
- 再推送：`git push -u origin main`
- 最终推送成功，`main` 已设置为跟踪 `origin/main`。

**全部执行命令与输出（按时间顺序）**
```powershell
git push -u origin main
To https://github.com/17hy/test
 ! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'https://github.com/17hy/test'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. If you want to integrate the remote changes,
hint: use 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

```powershell
git rebase --continue
fatal: no rebase in progress
```

```powershell
git push -u origin main
To https://github.com/17hy/test
 ! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'https://github.com/17hy/test'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. If you want to integrate the remote changes,
hint: use 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

```powershell
git status -sb; git fetch origin; git status -sb; git log --oneline --decorate --graph --left-right --cherry-pick main...origin/main -n 20
## main
## main
> 0fb0ea0 (origin/main) Initial commit
< b4b3e2f (HEAD -> main) hello
```

```powershell
git pull --rebase origin main
From https://github.com/17hy/test
 * branch            main       -> FETCH_HEAD
Successfully rebased and updated refs/heads/main.
```

```powershell
git push -u origin main
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 20 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 280 bytes | 280.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To https://github.com/17hy/test
   0fb0ea0..ff914e4  main -> main
branch 'main' set up to track 'origin/main'.
```

如果你愿意，我可以再给你一个“以后遇到 non-fast-forward 直接照抄”的 4 行速查版。