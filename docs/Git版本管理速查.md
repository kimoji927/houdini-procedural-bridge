# Houdini 工程 Git 版本管理速查

> 仓库：`D:\Houdini` → https://github.com/kimoji927/houdini-procedural-bridge
> 环境：Git for Windows 2.56.0 + Git LFS 3.8.0

## 一、日常三步：改 → 存 → 传

在 `D:\Houdini` 目录下打开 Git Bash 或 PowerShell：

```bash
git status                 # 1. 看哪些文件改了
git add -A                 # 2. 把改动放进暂存区
git commit -m "说明这次改了什么"   # 3. 存成一个版本快照
git push                   # 4. 传到 GitHub（可选，但强烈建议）
```

只想提交某一个文件：

```bash
git add Wall.hipnc
git commit -m "Wall: 增加砖块置换"
git push
```

## 二、回退到过去的版本（Houdini 场景最常用）

```bash
git log --oneline                # 列出所有版本，记下前面的 7 位编号
git checkout 779857f -- Wall.hipnc   # 只把 Wall.hipnc 恢复成那个版本
```

**注意**：`checkout` 恢复出来的文件会直接覆盖工作区，先提交手头的改动再回退。

想整体回到某个版本、但保留之后的改动记录：

```bash
git revert <编号>      # 生成一个"反向提交"，不改写历史，适合已经 push 过的版本
```

## 三、Houdini 专属注意事项

| 文件类型 | 要不要进 Git | 说明 |
|---|---|---|
| `.hip` / `.hipnc` | ✅ 要 | 场景文件，主要版本管理对象 |
| `.hdanc` | ✅ 要 | Houdini 数字资产（HDA），**必须纳入** |
| `.picnc` | ❌ 不要 | 缓存文件，已在 `.gitignore` 排除 |
| `*.bak` / `*.$*` | ❌ 不要 | Houdini 自动备份，已排除 |
| 大体积贴图/视频 | ⚠️ 慎用 | 单个超 50 MB 建议用 Git LFS |

**`.hipnc` 是二进制文件**，Git 无法显示"改了哪几行"，只能整文件对比。所以：

- 提交说明一定要写清楚**改了什么**，这是唯一的线索；
- 不要频繁提交半成品，一个功能做完提交一次更清晰；
- 需要对比两个版本的差异，用 Houdini 自带的 `File → Compare` 或并排打开。

**大贴图 / 大缓存用 Git LFS**（已装好）：

```bash
git lfs track "*.exr"      # 让 exr 走 LFS
git lfs track "*.vdb"
git add .gitattributes
git commit -m "用 Git LFS 管理大体积资源"
```

## 四、当前仓库已配置的忽略规则

见 [.gitignore](../.gitignore)，已排除：

- `SideFXLabs*/`、`SideFXLabs*.zip` —— 第三方工具包（237 MB 的 zip 不进库）
- `backup/` —— 自动备份目录
- `untitled.hipnc`、`HDA.hipnc` —— 练习草稿
- `*.picnc`、`houdini_temp/`、`*.bak`、`*.$*` —— Houdini 运行时产物
- `_tools/` —— 本地安装包等临时文件

## 五、遇到问题

```bash
git status              # 任何异常先看这个
git diff                # 看具体改了什么
git log --oneline -10   # 看最近 10 个版本
```

`.git/index.lock` 报错通常是上次 Git 异常中断残留，确认没有 Git 进程在跑后可删除：

```bash
rm -f .git/index.lock
```
