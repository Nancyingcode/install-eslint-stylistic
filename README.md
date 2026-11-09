# install-eslint-stylistic

一个可复用的 Codex Skill：当你要求“安装 Shiroha 的 ESLint 和 Stylistic 规则”时，指导 Codex 根据目标项目结构接入规则、开发依赖与只读 lint 命令，并用真实 ESLint 验证规则生效。

规则取自 `shiroha-quiz-desktop` 已验证的本地提交 `55a29b2`。`assets/eslint.shiroha-rules.mjs` 包含完整的 `baseRules`、`semanticRules`、`styleRules` 和 `@stylistic` 插件注册。`baseRules` 中的 `@eslint/js` 10.0.1 recommended 规则已展开，因此目标项目不会因推荐集升级而改变规则内容，也无需为这份资源单独安装 `@eslint/js`。

## 安装到 Codex

将仓库克隆到 Codex 的 `skills` 目录。PowerShell 示例：

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME '.codex' }
$skillsRoot = Join-Path $codexRoot 'skills'
New-Item -ItemType Directory -Path $skillsRoot -Force | Out-Null
git clone git@github.com:Nancyingcode/install-eslint-stylistic.git (Join-Path $skillsRoot 'install-eslint-stylistic')
```

macOS / Linux 示例：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone git@github.com:Nancyingcode/install-eslint-stylistic.git "${CODEX_HOME:-$HOME/.codex}/skills/install-eslint-stylistic"
```

然后在目标项目的 Codex 对话中调用：

```text
$install-eslint-stylistic 请在当前项目安装 Shiroha 的 ESLint 和 Stylistic 规则。
```

## 接入原则

- 先读取项目规范、锁文件、Node/ESLint 版本、现有配置、lint 脚本和 monorepo 的实际包边界；沿用已有包管理器，只安装必要的开发依赖。
- 将规则资源复制到目标仓库，由目标仓库的 Flat Config 引用。提交后的 lint 命令不依赖本仓库、Shiroha 源仓库或 Codex 技能目录。
- 已有 Flat Config 时保留解析器、插件、忽略项、框架配置和更严格的规则；无配置时建立最小可运行配置。Node、浏览器、ESM、CommonJS 按目标文件分别配置。
- 遇到旧版 ESLint、`.eslintrc` 或 Prettier 风格冲突时列出具体冲突与处理方案；主版本升级和风格迁移由项目使用者决定。TypeScript、Vue、React 等文件只在实际验证兼容后纳入。
- 默认只读运行 lint，汇报现有违规，不自动修复源码。重复接入不应重复注册插件、追加配置块或覆盖原 lint 脚本。

Shiroha 原项目的目录、全局变量、Playwright 白名单及历史例外没有包含在可移植资源中；目标项目按自身运行环境配置这些内容。

## 已验证范围

使用 Node 24.22.2、npm 11.19.1、ESLint 10.13.0 和 `@stylistic/eslint-plugin` 5.10.0，在两个隔离样例中验证了无配置项目及已有 Flat Config 和 lint 脚本的项目。两种场景的真实 ESLint 和 `npm run lint` 都能拒绝语义与 Stylistic 违规，接受 `== null` 和参数对象属性修改的例外；第二次接入无文件差异。`skill-creator` 的 `quick_validate.py` 也已通过。

## 文件

- `SKILL.md`：Codex 的安装与验收指令。
- `assets/eslint.shiroha-rules.mjs`：可复制进目标仓库的固定规则集。
