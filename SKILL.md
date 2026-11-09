---
name: install-eslint-stylistic
description: 在 JavaScript 项目中安装并接入 Shiroha 的 ESLint 语义规则与 Stylistic 风格规则；用户要求“安装 Shiroha 的 ESLint 和 Stylistic 规则”时使用。
---

# 安装 Shiroha ESLint 与 Stylistic 规则

将 [assets/eslint.shiroha-rules.mjs](assets/eslint.shiroha-rules.mjs) 复制进目标仓库，由仓库自己的 Flat Config 引用。资源固定了 Shiroha 已验证提交 `55a29b2` 的 `baseRules`、`semanticRules`、`styleRules` 和 `@stylistic` 插件注册；`baseRules` 中的 `@eslint/js` 10.0.1 recommended 已展开，避免目标机器安装时因推荐集版本变化而漂移。源项目验证组合为 Node ≥24、ESLint 10.13.0、`@stylistic/eslint-plugin` 5.10.0；复制资源本身只要求 Stylistic 插件，不要求 `@eslint/js`。不要引用源仓库、个人绝对路径或 Skill 目录。源项目的忽略目录、全局变量、Playwright 白名单、历史行内豁免和数量上限均不随规则复制。

## 安装

1. 阅读目标项目规范、目录结构、锁文件、`package.json`、Node/ESLint 版本、ESLint 配置和 lint 脚本；在 monorepo 中找出配置所在包及实际管理其依赖和命令的包。优先沿用锁文件对应的包管理器。检查现有开发进程后再做会重建依赖目录的安装。只增加缺少且兼容的 devDependencies，更新相应锁文件。
2. 若已有 Flat Config，保持其解析器、插件、忽略项、文件范围、框架预设和脚本；将三组规则合入经确认适用的 JavaScript 配置，并仅在尚未注册时注册 `@stylistic`。同名规则先比较严重级别和选项：保留明确更严格的现有规则，采用 Shiroha 规则替换较弱者；无法比较或与项目风格冲突时列出具体冲突和可选处理方式，等用户选择。检查后续配置块是否覆盖目标规则。不要追加重复插件或重复配置块。
3. 若无配置，按实际源码创建最小 Flat Config、只读 lint 脚本及必要 devDependencies。将复制的规则文件放在 ESLint 配置同一依赖解析边界内。Node 与浏览器全局变量分别限定到对应文件；`.mjs` 使用 ESM，`.cjs` 使用 CommonJS，`.js` 按最近的包 `type` 和实际构建方式判定。仅忽略目标项目确有的产物。现有 lint 脚本若已运行 ESLint，只需确保它覆盖目标文件；否则在保留原命令行为的前提下接入一次检查，重复执行不再改动。
4. 对 TypeScript、Vue、React 等文件，沿用项目现有解析器、插件和框架配置；只在真实 `--print-config` 与 lint 探针证明兼容的文件范围启用适用规则。不要把 JS 的 `no-undef`、`no-unused-vars` 等规则直接覆盖类型或框架专属规则。遇到旧版 ESLint、传统 `.eslintrc`、Stylistic peer/Node 不兼容或 Prettier/现有格式规则冲突，说明具体规则或版本及处理选项；主版本升级和风格迁移由用户决定。不要偷偷降级规则集以求通过。

## 验证与边界

- 用目标包的本地 ESLint `--print-config` 检查每类代表性文件的最终规则、插件、解析器、模块类型与全局变量。运行真实 ESLint 验证一个语义违规、一个 `@stylistic` 违规均报错，`value == null` 与参数对象属性修改等有意例外可通过；同时检查不适用文件没有被错误覆盖。运行目标项目适用的 typecheck、lint、test、build，区分既有源码违规与安装故障。
- 默认 lint 只读，不使用 `--fix`，不批量改动既有源码。只有用户明确要求迁移源码时才分批处理；自增表达式的返回值、对象拼接的类型转换，以及循环回调读取变量的时机需保留并验证。
- 再执行一次相同安装流程，对比配置、脚本、依赖与锁文件均无新增差异。交付时说明实际启用的文件范围、版本、验证结果、既有违规和待用户决定的冲突。
