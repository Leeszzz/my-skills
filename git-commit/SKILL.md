---
name: git-commit
description: '执行 git commit，并支持 conventional commit message 分析、智能暂存、拆分提交建议与提交信息生成。用户要求提交更改、创建 git commit，或提到 "/git-commit"、"提交更改"、"版本发布" 等时使用。支持：(1) 根据变更自动检测 type 和 scope，(2) 识别需要拆分提交的情况并说明拆分方式、生成待审核草稿，(3) 统一使用中文生成 subject/body/footer，(4) 在用户确认后直接使用草稿执行 git commit，(5) 自动版本管理（VERSION 文件、CHANGELOG.md 更新、Git 标签创建）'
---
# 使用 Conventional Commits 的 Git 提交

## 概览

使用 Conventional Commits 规范创建标准化、语义化的 git 提交。通过分析实际 diff、暂存状态和近期提交历史，确定合适的 type、scope、language 和 message。

如果检测到当前改动包含多个独立逻辑变更，先说明应该如何拆分，再为每个提交生成草稿供用户审核；不要直接把多类改动压成一个 commit。

## 触发方式

本 skill 支持以下触发方式：

- **命令触发**：`/git-commit`
- **自然语言触发**：
  - "提交更改"
  - "创建 commit"
  - "版本发布"
  - "更新版本"
  - "发布新版本"
  - "commit 代码"
  - "提交代码"

## 版本管理

本 skill 支持自动版本管理，在提交前更新版本文件，在提交后创建 Git 标签：

**提交前**：
1. 检测 `VERSION` 和 `CHANGELOG.md` 文件，如不存在则从 Git 标签推断或询问用户是否创建
2. 根据提交类型计算新版本号
3. 更新 VERSION 文件
4. 更新 CHANGELOG.md（从 Git 历史提取，按类型分类）

**提交后**：
5. 创建 Git 标签（格式：`v1.0.0`）

### 版本号规则

| 提交类型 | 版本号变化 |
|---------|-----------|
| `feat` | 次版本 +1 |
| `fix` | 修订号 +1 |
| `BREAKING CHANGE` | 主版本 +1 |
| 其他 | 不变 |

**拆分提交**：取最大变化（如有 feat 则次版本 +1，如有 BREAKING 则主版本 +1）。

## Conventional Commit 格式

```
<type>[optional scope]: <description>

- <imperative bullet 1>
- <imperative bullet 2>
- <imperative bullet 3>

[optional footer(s)]
```

## Commit 类型

| Type         | 用途                        |
| ------------ | --------------------------- |
| `feat`     | 新功能                      |
| `fix`      | Bug 修复                    |
| `docs`     | 仅文档变更                  |
| `style`    | 格式/样式调整（无逻辑变更） |
| `refactor` | 代码重构（非功能/修复）     |
| `perf`     | 性能优化                    |
| `test`     | 新增/更新测试               |
| `build`    | 构建系统/依赖变更           |
| `ci`       | CI/配置变更                 |
| `chore`    | 维护/杂项                   |
| `revert`   | 回滚提交                    |

## 提交消息生成规则

### Subject

- 根据实际 diff 选择最合适的 `type`
- 仅在能明确定位影响范围时添加 `scope`
- `description` 必须使用祈使语气、当前时态，尽量控制在 72 个字符以内
- `description` 应直接说明本次提交最核心的变更，不要写成背景说明

### Body

- 必须在 `subject` 之后空一行
- 必须使用列表格式，每项以 `-` 开头
- 每项必须使用动词开头的祈使句，例如 `add ...`、`fix ...`、`update ...`
- 禁止使用冒号分隔的格式，例如 `Feature: ...`、`Impl: ...`
- 说明变更动机、实现要点或影响范围
- 以 1-3 项为宜，避免冗长重复

### Footer

- 如有 footer，必须在 `body` 之后空一行
- 若存在破坏性变更，必须包含 `BREAKING CHANGE: <description>`，或在类型后添加感叹号，例如 `feat!:`
- 其它 footer 必须使用 git trailer 格式，例如 `Closes #123`、`Refs: #456`、`Reviewed-by: Name`

### 提交信息语言

- 提交信息（包括 subject、body、footer 中的描述性内容）统一使用中文
- `type`、`scope`、`BREAKING CHANGE` 和标准 git trailer（如 `Closes #123`、`Refs: #456`）保持 Conventional Commits / git 约定格式，使用英文
- 示例：`feat(auth): 添加登录表单验证`，而不是 `feat(auth): add login form validation`

## 破坏性变更

```
# 在 type/scope 后添加感叹号
feat!: 删除已弃用的端点

# 使用 BREAKING CHANGE footer
feat: 允许配置扩展其他配置

- 添加对扩展基础配置的支持
- 更新继承选项的合并逻辑

BREAKING CHANGE: `extends` key 行为发生变化
```

## 拆分提交规则

当检测到以下情况时，应建议拆分提交：

- 同时包含多个独立目的的改动
- 同时混合功能、修复、重构、格式化或测试等不同类型的改动
- 不同文件组之间缺少明确的单一逻辑主题
- 某些文件变更可以独立提交和回滚

如果需要拆分提交，必须：

1. 明确说明建议拆分成几个 commit
2. 描述每个 commit 应包含哪些文件或变更块
3. 说明这样拆分的理由
4. 为每个 commit 生成完整提交消息草稿供用户审核
5. 在用户审核前，不要直接执行这些 commit

### 拆分提交示例

#### 示例：功能 + 测试

```text
建议拆分为 2 个提交：

1. feat(auth): add login form validation
   - 包含：src/auth/form.ts、src/auth/form.test.ts
   - 原因：表单校验属于独立功能变更

   草稿：
   feat(auth): add login form validation

   - add client-side validation for required auth fields
   - update submit flow to block invalid requests
   - add tests for invalid input scenarios

2. refactor(ui): simplify auth input state handling
   - 包含：src/ui/input.ts、src/ui/useInput.ts
   - 原因：状态整理属于独立重构，不应与功能提交混合

   草稿：
   refactor(ui): simplify auth input state handling

   - refactor shared input state updates to remove duplication
   - update auth field bindings to reuse the simplified flow
```

## 工作流

### 1. 分析变更与提交历史

```
# 检查状态
git status --porcelain

# 如果已有已暂存文件，使用 staged diff
git diff --staged

# 如果没有已暂存内容，使用 working tree diff
git diff

# 检查最近提交主题，用于了解项目风格
git log -n 50 --pretty=%s
```

### 2. 判断是否需要拆分提交

使用以下决策树判断是否需要拆分：

```text
开始
  ↓
检查变更类型
  ↓
是否包含多种类型？（功能、修复、重构、格式化、测试等）
  ↓
是 → 建议拆分
  ↓
检查文件关联性
  ↓
文件之间是否有明确的单一逻辑主题？
  ↓
否 → 建议拆分
  ↓
检查变更规模
  ↓
变更是否过大（超过 500 行）？
  ↓
是 → 考虑拆分
  ↓
检查回滚需求
  ↓
是否需要独立回滚某些变更？
  ↓
是 → 建议拆分
  ↓
否 → 单一提交
```

- 先判断当前改动是否属于单一逻辑变更
- 如果不是，先输出拆分方案和每个 commit 的草稿，等待用户审核
- 只有在用户确认后，才按拆分方案分别暂存和提交

### 3. 版本管理（可选）

如果项目启用了版本管理，在提交前执行以下步骤：

1. **检测版本文件**：检查项目根目录的 `VERSION` 和 `CHANGELOG.md` 文件，如不存在则从 Git 标签推断或询问用户是否创建
2. **计算新版本号**：根据提交类型自动计算
3. **更新 VERSION 文件**：写入新版本号
4. **更新 CHANGELOG.md**：从 Git 历史提取提交记录，按类型分类

#### CHANGELOG 格式

遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 格式：

```markdown
# Changelog

## [Unreleased]

### Added
- 新增功能

### Changed
- 功能变更

### Fixed
- 缺陷修复

## [1.0.0] - 2026-06-12

### Added
- feat(auth): add login validation (abc1234)
```

**提交类型映射**：
- `feat` → Added
- `fix` → Fixed
- `refactor`、`docs`、`perf`、`test` → Changed

#### 错误处理

**Git 历史提取失败**：提示用户手动创建 CHANGELOG.md

**版本号推断失败**：询问用户选择默认版本号或手动输入

### 4. 暂存文件（如有需要）

如果当前没有内容被暂存，或者你想以不同方式组织变更：

```
# 暂存指定文件
git add path/to/file1 path/to/file2

# 按模式暂存
git add *.test.*
git add src/components/*

# 交互式暂存
git add -p
```

**永远不要提交密钥或敏感信息**（例如 `.env`、`credentials.json`、`auth.json`、私钥）。

### 5. 生成 Commit Message 草稿

分析 diff 以确定：

- **Type**：这次变更属于哪一类
- **Scope**：影响了哪个区域/模块
- **Description**：一行总结本次变更
- **Body**：1-3 条祈使句列表，说明动机、实现要点或影响范围
- **Footer**：如有破坏性变更、issue 或审核信息，按规范追加
- **Language**：统一使用中文

### 6. 执行提交

在用户审核并确认当前草稿后，直接使用该草稿执行 commit。

如果是拆分提交，则按用户确认顺序，逐个使用对应草稿分别执行 commit。

### 7. 创建 Git 标签（提交后，可选）

如果项目启用了版本管理，提交成功后创建 Git 标签：

- **格式**：`v1.0.0`
- **类型**：带注释的标签（annotated tag）
- **注释**：包含变更摘要

## 复杂场景处理

### 合并冲突

当遇到合并冲突时：

1. 先解决冲突：`git mergetool` 或手动编辑冲突文件
2. 暂存解决后的文件：`git add <resolved-files>`
3. 继续合并：`git merge --continue` 或 `git rebase --continue`
4. 生成提交消息时，说明是合并提交或变基提交

### 二进制文件

对于二进制文件（图片、编译文件等）：

- 在提交消息中明确说明二进制文件的变更
- 考虑使用 Git LFS 管理大型二进制文件
- 避免频繁提交大型二进制文件

### 子模块

对于 Git 子模块：

- 在提交消息中说明子模块的更新
- 使用 `git submodule update --init --recursive` 初始化子模块
- 提交子模块变更时，同时提交父仓库的引用更新

### 大型变更

对于大型变更（超过 500 行）：

- 考虑拆分为多个较小的提交
- 使用 `git add -p` 进行交互式暂存
- 在提交消息中说明变更的范围和影响

## 交互式暂存

交互式暂存允许你选择性地暂存文件的部分内容：

### 基本命令

```
# 交互式暂存所有变更
git add -p

# 交互式暂存特定文件
git add -p path/to/file

# 交互式暂存新文件
git add -i
```

### 常用选项

- `y`：暂存当前块
- `n`：跳过当前块
- `s`：分割当前块为更小的块
- `e`：手动编辑当前块
- `q`：退出交互式暂存

### 最佳实践

- 使用 `s` 将大块分割为更小的逻辑单元
- 使用 `e` 精确控制暂存内容
- 在暂存前使用 `git diff` 查看变更

## 最佳实践

- 每次 commit 只包含一个逻辑变更
- 使用现在时：写 `add`，不要写 `added`
- 使用祈使语气：写 `fix bug`，不要写 `fixes bug`
- body 使用精炼列表，避免写成长段落
- 关联 issue：`Closes #123`、`Refs: #456`
- description 保持清晰、具体、可扫描
- 避免提交临时文件或调试代码
- 定期清理历史：使用 `git rebase -i` 整理提交

## Git 安全协议

- **绝不要**修改 git config
- **绝不要**在未明确请求的情况下执行破坏性命令（如 `--force`、`hard reset`）
- **绝不要**在用户未要求时跳过 hooks（`--no-verify`）
- **绝不要**强制推送到 `main/master`
- 如果 commit 因 hooks 失败，应修复问题并创建**新的** commit（不要 amend）

## 性能优化

### 大型仓库

- 使用 `git status --porcelain` 而不是 `git status` 减少输出
- 使用 `git diff --staged` 而不是 `git diff` 减少计算
- 使用 `git log --oneline -n 10` 而不是 `git log` 减少历史加载