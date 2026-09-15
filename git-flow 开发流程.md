下面内容可直接转发给协作者。

# Nova 项目协作开发规范

## 1. 分支职责

```
main
└── 已验证、可正式发布的代码

develop
└── 日常集成分支，对应自动部署的测试环境

feature/*、fix/*、refactor/*、docs/*、chore/*
└── 每项独立工作从 develop 创建，完成后通过 PR 合并回 develop
```

规则：

- 禁止直接推送 `develop`、`main`。
- 禁止强制推送、改写或删除共享分支历史。
- 一个分支只处理一个明确主题。
- Gitee 是只读镜像，禁止直接向 Gitee 开发或提交。

## 2. 开始开发前：拉取最新 develop

```
git fetch origin --prune

git switch develop
git pull --ff-only origin develop

git switch -c feature/功能名称
```

缺陷使用：

```
git switch -c fix/问题名称
```

示例：

```
feature/teacher-analytics
fix/websocket-reconnect
docs/deployment-guide
```

## 3. 开发要求：代码必须配套测试

任何可观察行为变更都必须同时提交代码与测试：

- 新功能：新增对应单元测试、接口测试或前端测试。

- 修复 Bug：必须增加能复现该 Bug 的回归测试。

- 前端调用后端接口：优先补充真实 API 客户端 → FastAPI 路由的集成测试。

- WebSocket、重连、发送/确认等链路变更：必须覆盖异常场景。

- 不允许为了让 CI 通过而删除、跳过、弱化测试。

- 所有新增或修改的测试必须能被现有 CI 自动执行；不得只在本地保留、但未被 pytest、Vitest 或 CI 工作流发现的测试文件。

- 后端测试应放入 pytest 可发现的 tests/ 或 scripts/ 路径；前端测试应放入 Vitest 可发现的 webui/src/**/*.test.ts(x) 路径。新增测试后，必须确认其已包含在 GitHub Actions 的 CI 执行结果中。

- 提交 PR时：除了说明“新增了哪些测试”，还应说明：

  CI 覆盖：Backend tests and static checks / Frontend tests, lint and build

测试位置遵循现有项目结构：

```
tests/
  └── test_*.py              # Python、FastAPI、后端测试

webui/src/
  └── *.test.ts / *.test.tsx # 前端单元与集成测试
```

## 4. 本地提交前必须检查

后端：

```
uv sync --frozen --group dev
uv run ruff check configs core gateway server tests scripts
uv run pytest
```

前端：

```
cd webui
npm ci
npm run typecheck
npm run lint
npm test
npm run build
npm run build:monitor
```

提交前还必须检查差异：

```
git diff --check
git status --short
```

不要提交：

```
.env
密钥、Token、私钥
数据库文件
本地缓存、日志
node_modules
.venv
```

## 5. 提交规范

使用 Conventional Commits：

```
feat(webui): add teacher analytics page
fix(gateway): recover a turn after socket reconnect
test(api): cover teacher catalog import rejection
docs(deploy): clarify production release steps
chore(ci): update test delivery workflow
```

每次提交应聚焦、可读、可回滚。不要使用：

```
update
fix
wip
test
123
```

提交与推送：

```
git add <明确文件路径>
git diff --cached
git commit -m "feat(scope): concise description"
git push -u origin feature/功能名称
```

## 6. feature/fix → develop：开发集成流程

创建 Pull Request：

```
base：develop
compare：feature/* 或 fix/*
```

PR 描述至少说明：

```
1. 改了什么
2. 为什么改
3. 新增或更新了哪些测试
4. 本地执行了哪些验证
5. 是否有配置、迁移或兼容性影响
```

合并前必须通过以下 CI 检查：

```
Backend tests and static checks
Frontend tests, lint and build
Docker build check
```

Review 确认后合并到 `develop`。

## 7. 合并 develop 后：自动测试环境部署

合并到 `develop` 后自动执行：

```
CI
→ 构建 GHCR 测试镜像
→ 推送 ghcr.io/liunor/nlp-agent:develop
→ Deploy Test Environment
→ 测试服务器拉取新镜像
→ 健康检查
```

测试环境持续运行，每次 `develop` 更新会自动替换为新版本。

测试环境验收由负责人/测试人员完成，至少覆盖：

- 页面正常打开；
- 会话创建与普通对话；
- 流式回复、停止、刷新恢复；
- WebSocket 断线重连；
- 教师端核心功能；
- 浏览器控制台无明显错误；
- GitHub Actions 测试部署与健康检查为绿色。

测试环境地址仅在项目内部渠道共享，不要公开传播。

## 8. develop → main：发布候选流程

仅当测试环境人工验收通过后，创建 PR：

```
base：main
compare：develop
```

合并前同样必须通过：

```
Backend tests and static checks
Frontend tests, lint and build
Docker build check
```

`main` 代表可发布代码。合并到 `main` 本身不会自动部署生产环境。

## 9. 正式生产发布：仅由发布负责人操作

正式发布必须人工创建版本 Tag，例如：

```
git switch main
git pull --ff-only origin main

git tag -a v1.0.1 -m "v1.0.1: production release"
git push origin v1.0.1
```

版本规则：

```
PATCH：v1.0.1 兼容性 Bug 修复
MINOR：v1.1.0 新增兼容功能
MAJOR：v2.0.0 有破坏性变更
```

Tag 推送后自动执行：

```
校验 Tag 属于 main
→ 构建 ghcr.io/liunor/nlp-agent:vX.Y.Z
→ 等待 production 人工审批
→ 部署生产环境
→ 健康检查
→ 同步 main 与所有 Tag 到 Gitee
```

生产审批只能由项目发布负责人确认。未完成测试环境验收时，禁止创建生产 Tag。

## 10. GitHub 分支保护

`develop` 与 `main` 都应开启：

- 必须通过 Pull Request 合并；
- 必须通过状态检查；
- 必须解决所有讨论；
- 合并前分支必须为最新；
- 阻止强制推送。

必需状态检查只选择：

```
Backend tests and static checks
Frontend tests, lint and build
Docker build check
```

不要把发布、测试部署、生产部署或 Gitee 同步设为 PR 必需检查。

## 11. 紧急修复流程

```
fix/* → develop → 测试环境验证 → develop → main → 版本 Tag → 生产部署
```

即使是紧急修复，也必须有回归测试和 CI；不能绕过 `main` 的分支保护。