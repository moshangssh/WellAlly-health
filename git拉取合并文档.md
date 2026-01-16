# Git 全能协作手册：从环境配置到冲突处理

## 一、 初始配置：建立仓库关联

在开始开发之前，您需要确保本地仓库既连接了您的个人远程库（Origin），也连接了原始项目库（Upstream）。

### 1. 关联上游仓库
如果您是通过 *Fork* 方式获取的项目，请执行以下命令：
```bash
# 添加原始项目地址
git remote add upstream https://github.com/original-owner/repo.git

# 验证远程服务器列表
git remote -v
```

### 2. 配置个人用户信息
确保您的提交记录带有正确的身份标识。
```bash
git config --global user.name "您的用户名"
git config --global user.email "您的邮箱"
```
> Upstream: 在 Git 协作中特指原始项目仓库，通常用于获取核心更新。

---

## 二、 开发循环：同步与提交

当您准备开始编写代码，或者本地已有修改但上游发生了更新时，请遵循以下标准流程。

### 1. 处理本地未完成的修改
如果您的代码写了一半，上游突然有了重要更新，请先暂存修改：
```bash
# 将当前修改存入临时栈
git stash save "功能开发中-同步前备份"
```

### 2. 获取上游最新动态
```bash
# 拉取所有远程分支的最新数据
git fetch upstream
```

### 3. 执行变基同步
将本地分支的基准点移动到上游最新的提交之上。
```bash
# 切换到主分支（如 main 或 master）
git checkout main

# 执行变基
git rebase upstream/main
```
> Rebase: 指将分支的起始基准重新定位到另一个提交点上的操作，旨在创造线性的提交历史。

---

## 三、 冲突处理：应对代码碰撞

如果在执行 `rebase` 或 `stash pop` 时提示 *Conflict*，请按照以下步骤操作。

### 1. 识别冲突位置
Git 会列出冲突文件。打开这些文件，您会看到如下标记：
```text
<<<<<<< Updated upstream
上游的代码内容
=======
您本地修改的代码内容
>>>>>>> Stashed changes
```

### 2. 手动解决并标记
1. 删除标记符号，根据需求合并代码。
2. 保存文件后，将其加入暂存区：
```bash
git add <文件名>
```

### 3. 完成整合流程
*   **如果是变基冲突**：执行 `git rebase --continue`。
*   **如果是暂存冲突**：直接保存即可，无需特殊命令。
> Conflict Marker: Git 在冲突文件中自动插入的特殊字符串，用于标识不同版本代码的边界。

---

## 四、 成果发布：推送与合并

完成本地开发并与上游同步后，将代码推送到您的远程仓库。

### 1. 强制推送（变基后必需）
由于变基改变了提交历史，必须使用强制推送更新您的 *Fork* 库：
```bash
# force-with-lease 比 -f 更安全
git push origin main --force-with-lease
```

### 2. 恢复暂存的修改
如果您在第一步使用了 `stash`，现在可以取回：
```bash
git stash pop
```
> Force-with-lease: 强制推送的变体，它在推送前会检查远程分支是否已被他人更新，防止覆盖他人的工作。

---

# Git 全程操作逻辑总结

| 阶段 | 核心任务 | 关键命令 |
| :--- | :--- | :--- |
| **配置阶段** | 建立连接 | `git remote add upstream <URL>` |
| **备份阶段** | 保护现场 | `git stash` 或 `git commit` |
| **同步阶段** | 获取更新 | `git fetch upstream` |
| **整合阶段** | 历史重排 | `git rebase upstream/main` |
| **解决阶段** | 消除冲突 | `git add .` -> `git rebase --continue` |
| **发布阶段** | 同步云端 | `git push origin --force-with-lease` |

---

# 后续建议

1. 您是否需要将这套流程配置为 *Alias*（别名），以便通过一个简短的命令完成复杂的同步动作？
2. 在发起 *Pull Request* 之前，您是否需要了解如何使用 `git commit --amend` 来润色您的最后一次提交信息？