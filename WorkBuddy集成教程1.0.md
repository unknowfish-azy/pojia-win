# 破甲 WorkBuddy 版 · 部署与集成教程（GitHub 发布版）

本仓库是破甲（pojia）next 的 **Windows 移植版**，复刻了你 DSH 中的两个自定义模式：

- **AI-PI v1.0**：提示词注入（Prompt Injection）领域的技术交流与授权研究模式
- **REA 逆向模式**：逆向工程相关模式

这两个模式在 WorkBuddy 中以「**项目级技能**」机制实现。本文档说明如何把本仓库作为 WorkBuddy 工作区使用、文件应放在什么位置、以及怎么在 WorkBuddy 里调用。

---

## 一、文件应该放在什么位置（核心）

WorkBuddy 识别自定义功能靠的是一个固定路径约定，放错层级不会被识别。

| 内容 | 应放位置 | 作用 |
| --- | --- | --- |
| 整个破甲工程 | 任意本地目录，如 `E:\pojia-workbuddy-win` | 作为 WorkBuddy 工作区根目录 |
| 双模式技能 | `<工作区>\.workbuddy\skills\ai-pi-mode` 与 `.workbuddy\skills\rea-reverse-mode` | 项目级技能：打开工作区后自动加载，可在「技能」菜单选择 / 自动触发 |
| （可选）全局专家 | `%USERPROFILE%\.workbuddy\plugins\marketplaces\my-experts\plugins\ai-pi-mode`（及 `rea-reverse-mode`） | 跨项目可用，左侧「专家」面板调用 |

> 关键约定：项目级技能必须是工作区根目录下的 `.workbuddy/skills/<技能名>/SKILL.md`。
> 放到 `skills/`（顶层）或别的目录都不会被 WorkBuddy 识别为工作区技能。
> 本仓库根目录的 `skills/` 是**已纳入 Git 的可分发副本**；`.workbuddy/skills/` 是**本地生效目录**（默认 git-ignored）。

---

## 二、两种部署方式

### 方式 A：直接把本仓库当工作区（最简单）

1. 把仓库克隆 / 解压到本地，例如 `E:\pojia-workbuddy-win`。
2. 确认目录里有 `.workbuddy\skills\ai-pi-mode` 和 `.workbuddy\skills\rea-reverse-mode`（若从 GitHub 下载后缺失，按方式 B 复制）。
3. 打开 WorkBuddy → 左上角「**选择工作空间**」→ 选择 `E:\pojia-workbuddy-win`。
4. 完成。该工作区即成为破甲专属工作区，提到 AI-PI / 逆向 等关键词会自动触发对应模式。

### 方式 B：把双模式技能带入你已有的工作区

如果你不想用整个破甲工程做工作区，只想在某个自己的项目里用这两个模式：

1. 从本仓库的 `skills/` 目录复制 `ai-pi-mode` 与 `rea-reverse-mode` 两个文件夹。
2. 粘贴到你的工作区根目录下的 `.workbuddy\skills\` 中（没有就新建）。
3. 重新打开该工作区。两个模式即出现在其「技能」菜单。

（可选）安装全局专家：把你本地 `%USERPROFILE%\.workbuddy\plugins\marketplaces\my-experts\plugins\` 下的 `ai-pi-mode`、`rea-reverse-mode` 复制保留即可；注册信息已写入 `my-experts` 的 `marketplace.json`，可在任意项目的左侧「专家」面板调用。

---

## 三、怎么在 WorkBuddy 里调用（给它下什么指令）

有三种入口，效果一致：

1. **输入框打 `/`**：在对话输入框输入 `/`，弹出技能与指令列表 → 选 `ai-pi-mode` / `rea-reverse-mode`。
2. **专家面板**：左侧栏「专家·技能·连接器」→「专家」分类 → 选「AI-PI v1.0」「REA 逆向模式」（带头像的即为本仓库专家）。
3. **自动触发**：把工作空间切到本仓库（方式 A）后，直接对话，提到「AI-PI」「提示词注入」「逆向」等词会自动触发，无需手动选择。

---

## 四、关于「模式」菜单（重要说明）

WorkBuddy 输入框「+」里的「**模式**」子菜单（默认 / 计划 / 仅问答）是**内置的权限模式**，选项写死在客户端，没有配置扩展点，无法把自定义模式加进去。

自定义功能的标准入口是同一个「+」菜单里的「**技能**」子菜单——本仓库的双模式就在那里。这是 WorkBuddy 官方的扩展方式，不是 workaround。

---

## 五、给开发者的提醒（封条机制）

- 主程序 `wb-windows.py` 带**源码级封条**：改动任一字符会拒绝运行（exit code 3）。
- 若你确实需要改主程序，先重跑 `python _build_win_seal.py` 重建封条（幂等，原 macOS 文件从 git 历史提取）。
- `release.py` 重新打包 `dist/`，但 `.gitignore` 已忽略 `.workbuddy/`（本地工作区配置不进仓库）和 `dist/`（可重建产物）。

---

## 六、GitHub 发布建议

- 仓库根 `skills/` 已包含可分发技能（含 `ai-pi-mode`、`rea-reverse-mode`），克隆后按方式 B 即可用。
- `.workbuddy/` 默认 git-ignored，属本地工作区配置，不进库；使用者按本文「方式 B」自行放置即可。
- 若希望「克隆即工作区」（无需手动复制），二选一：
  - 在 `.gitignore` 末尾加例外放行这两个技能目录：
    ```
    !.workbuddy/
    !.workbuddy/skills/
    !.workbuddy/skills/ai-pi-mode/
    !.workbuddy/skills/rea-reverse-mode/
    ```
  - 或在仓库放一个 `setup-workbuddy.ps1` 一键把 `skills/` 下的两个文件夹复制到 `.workbuddy/skills/`。

---

## 七、目录结构示例

```
pojia-workbuddy-win/
├─ wb-windows.py              # 主程序（Windows 版，带源码级封条）
├─ pojia-console.ps1          # 控制台入口
├─ start-console.bat          # Windows 启动脚本（chcp 65001）
├─ _build_win_seal.py         # 封条重建工具
├─ release.py                 # 发布打包助手
├─ skills/                    # 可分发技能（已纳入 Git）
│  ├─ ai-pi-mode/SKILL.md     #   AI-PI v1.0 模式
│  └─ rea-reverse-mode/SKILL.md   # REA 逆向模式
├─ .workbuddy/                # 本地工作区配置（git-ignored）
│  └─ skills/
│     ├─ ai-pi-mode/SKILL.md
│     └─ rea-reverse-mode/SKILL.md
└─ WorkBuddy集成教程.md        # 本文件
```

---

## 八、常见问题

**Q：为什么我在「模式」菜单里找不到 AI-PI？**
A：那是内置权限模式，不可扩展。去同一个「+」菜单的「技能」子菜单，或用 `/` 调用，或左侧「专家」面板。

**Q：克隆下来后对话没有自动触发模式？**
A：检查 `.workbuddy/skills/` 下是否有两个模式文件夹（见方式 B 复制）。若只复制了仓库根 `skills/`，需手动放进 `.workbuddy/skills/`。

**Q：改完 wb-windows.py 跑不起来？**
A：源码级封条生效了。运行 `python _build_win_seal.py` 重建封条后再试。

**Q：两个模式会冲突吗？**
A：不会。各自有触发词与边界规则；明确说「退出 AI-PI 模式 / 切回标准模式」即恢复默认口径。
