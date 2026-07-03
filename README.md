# Coplay Unity AI 开发完整指南

## 🔗 最新安装链接（快速入口）

- Coplay Unity Plugin（beta）：`https://github.com/CoplayDev/coplay-unity-plugin.git#beta`
- MCP for Unity（main）：`https://github.com/CoplayDev/unity-mcp.git?path=/MCPForUnity#main`

> **CoplayDev** 旗下目前有**两套独立的 Unity AI 工具**，安装地址相似，但定位完全不同。  
> 本文档覆盖两者的完整安装流程、核心区别对比与最佳实践。

---

## 📌 快速导航

| 我想用…… | 跳转 |
|----------|------|
| 外部 AI（Claude/Cursor/VS Code）通过 MCP 控制 Unity | [➜ 方案一：MCP for Unity](#方案一mcp-for-unity-外部-ai-控制-unity) |
| Unity 内嵌 AI 对话窗口（Coplay/Aura 插件） | [➜ 方案二：Coplay Unity Plugin](#方案二coplay-unity-plugin-内嵌-ai-助手) |
| 两套方案的优缺点对比 | [➜ 对比选择指南](#-两套方案对比与选择指南) |

---

## 🔍 两套方案对比与选择指南

### 一眼看懂：核心区别

```
方案一：MCP for Unity
你 → 在 Cursor/Claude 里打字 → MCP Server → Unity 执行

方案二：Coplay Unity Plugin（现已更名 Aura）
你 → 在 Unity 窗口内打字（Ctrl+G） → AI 云端 → Unity 执行
```

### 详细对比表

| 对比维度 | 🔧 方案一：MCP for Unity | 🤖 方案二：Coplay Plugin（Aura） |
|----------|--------------------------|----------------------------------|
| **Git 安装 URL** | `unity-mcp.git?path=/MCPForUnity#main` | `coplay-unity-plugin.git#beta` |
| **Unity 包名** | `com.coplaydev.unity-mcp` | `com.coplaydev.coplay` |
| **当前版本** | v10.0.0（2026-06-30） | v8.20.3 |
| **工作方式** | AI 客户端 ↔ MCP Server ↔ Unity 插件 | Unity 内嵌窗口 ↔ Coplay/Aura 云端 |
| **AI 模型** | 取决于你用哪个 MCP 客户端 | GPT-4.1 / Gemini 2.5 Pro / Claude 4-Sonnet / Grok 3 |
| **使用入口** | Cursor、Claude Desktop、VS Code 等 | Unity 菜单 `Coplay → Toggle Window`（`Ctrl+G`） |
| **是否需要账号** | ❌ 无需注册，本地运行 | ✅ 需要注册登录 Coplay/Aura 账号 |
| **是否需要 Python** | ✅ 需要 Python 3.10+ + uv | ❌ 不需要 |
| **联网要求** | 仅 AI 客户端需要联网（MCP Server 本地运行） | 必须联网（AI 推理在云端） |
| **Unity 版本** | 2021.3 LTS → 6.x | 2022.3 或更高 |
| **开源协议** | MIT 开源免费 | 商业产品（部分功能付费） |
| **Stars** | ⭐ 11,000+（社区热门） | ⭐ 141 |
| **当前状态** | ✅ 活跃维护 | ⚠️ Coplay 已被 Ramen 收购，品牌改为 **Aura** |
| **迁移说明** | 无需迁移 | 建议迁移至 [tryaura.dev](https://www.tryaura.dev/) |

### ✅ 优缺点一览

#### 方案一：MCP for Unity

| | 详情 |
|---|---|
| ✅ **优点** | 完全免费开源（MIT），无需账号；支持任意 MCP 兼容客户端（Claude/Cursor/VS Code/Gemini 等）；本地运行，数据不出外网；47 个细粒度 MCP 工[...] |
| ❌ **缺点** | 需要额外安装 Python 3.10+ 和 uv；需要配置 AI 客户端 JSON；首次配置略复杂；操作窗口在外部 AI 工具里，不在 Unity 内 |
| 🎯 **适合谁** | 已在用 Cursor/Claude/VS Code 的开发者；希望用自己的 AI 账号；注重数据安全或离线场景；喜欢 DIY 配置的开发者 |

#### 方案二：Coplay Unity Plugin（Aura）

| | 详情 |
|---|---|
| ✅ **优点** | 安装极简，无需 Python/uv；AI 窗口内嵌 Unity，操作不切换软件；多模型支持（GPT-4.1/Gemini/Claude/Grok）；支持 Pipelines（录制回放操作序列��[...] |
| ❌ **缺点** | 需要注册账号；AI 推理依赖云端，必须联网；Coplay 品牌已被收购，正在向 Aura 迁移中，稳定性存在不确定性；功能控制权在平台方 |
| 🎯 **适合谁** | 不想折腾配置的开发者；习惯在 Unity 内操作；需要多模型灵活切换；可以接受云端服务和账号体系 |

---

## 方案一：MCP for Unity（外部 AI 控制 Unity）

> **仓库**：[CoplayDev/unity-mcp](https://github.com/CoplayDev/unity-mcp)  
> **许可证**：MIT 免费开源 | **最新版本**：v10.0.0（2026-06-30）

### 前置环境要求

| 依赖项 | 要求版本 | 说明 |
|--------|----------|------|
| **Unity Editor** | 2021.3 LTS → 6.x | 推荐 2022+ LTS |
| **Git** | 任意版本 | 用于通过 URL 拉取 Unity 包 |
| **Python** | **3.10 或更高**（推荐 3.11+） | 运行 MCP Server 必须 |
| **uv / uvx** | 最新版 | Python 包管理工具 |
| **AI 客户端** | 任意 MCP 兼容客户端 | 见下方支持列表 |

**已支持的 AI 客户端（v10.0.0）：**
Claude Code CLI · Claude Desktop · Cursor · VS Code / GitHub Copilot · Cline · Windsurf · Gemini CLI · Qwen Code · Kimi Code · OpenCode · OpenClaw

### 架构示意图

![Coplay 系统架构示意图](images/coplay_architecture.png)

### 第一步：在 Unity 中安装插件

#### 方式 A：Git URL 安装（推荐）

1. 打开 Unity 项目 → **Window > Package Manager**
2. 点击左上角 **`+`** → **Add package from git URL...**
3. 粘贴以下链接，点击 **Add**：

```
https://github.com/CoplayDev/unity-mcp.git?path=/MCPForUnity#main
```

> 💡 **版本锁定**：将 `#main` 替换为 `#v10.0.0` 可锁定到指定版本。

#### 方式 B：OpenUPM 安装

```bash
openupm add com.coplaydev.unity-mcp
```

#### 方式 C：Unity Asset Store

访问 [MCP for Unity on Asset Store](https://assetstore.unity.com/packages/tools/generative-ai/mcp-for-unity-ai-driven-development-329908)。

### 第二步：配置并启动桥接服务

1. Unity 菜单 → **Window > MCP for Unity**
2. 点击 **Configure All Detected Clients**（自动检测并配置所有 MCP 客户端）
3. 如提示缺少 Python / uv，按提示安装后重试
4. 点击 **Start Bridge** → 服务在本地 `localhost:8080` 启动

### 第三步：配置 AI 客户端

#### Claude Code CLI（推荐）

```bash
# 安装 Claude Code CLI
npm install -g @anthropic-ai/claude-code

# 注册 MCP 服务器
claude mcp add --scope user --transport stdio coplay-mcp \
  --env MCP_TOOL_TIMEOUT=720000 \
  -- uvx --python ">=3.10" coplay-mcp-server@latest

# 验证
claude mcp list
```

#### Claude Desktop

编辑 `claude_desktop_config.json`：

```json
{
  "mcpServers": {
    "coplay-mcp": {
      "command": "uvx",
      "args": ["--python", ">=3.10", "coplay-mcp-server@latest"],
      "env": {
        "MCP_TOOL_TIMEOUT": "720000"
      }
    }
  }
}
```

#### Cursor / Cline / Windsurf

```json
{
  "mcpServers": {
    "coplay-mcp": {
      "autoApprove": [],
      "disabled": false,
      "timeout": 720,
      "type": "stdio",
      "command": "uvx",
      "args": ["--python", ">=3.10", "coplay-mcp-server@latest"]
    }
  }
}
```

#### VS Code / GitHub Copilot

1. `Ctrl/Cmd + Shift + P` → **MCP: Add Server**
2. 传输方式选 **stdio**
3. 启动命令：`uvx --python >=3.10 coplay-mcp-server@latest`
4. 标识名填：`coplay-mcp`

#### Kimi Code CLI（v9.7.3+）

```json
{
  "mcpServers": {
    "coplay-mcp": {
      "command": "uvx",
      "args": ["--python", ">=3.10", "coplay-mcp-server@latest"],
      "env": { "MCP_TOOL_TIMEOUT": "720000" }
    }
  }
}
```

### 第四步：验证连接

在 AI 客户端中输入：

```
List all of the open unity editors using Coplay MCP
```

AI 能返回当前 Unity 项目名称 → 安装成功 ✅

---

## 方案二：Coplay Unity Plugin（内嵌 AI 助手）

> **仓库**：[CoplayDev/coplay-unity-plugin](https://github.com/CoplayDev/coplay-unity-plugin)  
> **包名**：`com.coplaydev.coplay` v8.20.3  
> ⚠️ **重要**：Coplay 已被 Ramen 收购，品牌更名为 **[Aura](https://www.tryaura.dev/)**。  
> 现有插件仍可使用，但官方推荐迁移至新版 Aura。

### 前置环境要求

| 依赖项 | 要求 |
|--------|------|
| **Unity Editor** | **2022.3 或更高** |
| **Git** | 任意版本 |
| **网络** | 必须联网（AI 推理在云端） |
| **账号** | 需注册 Coplay / Aura 账号 |

> ✅ **无需** Python、uv 或任何额外运行时。

### 安装步骤

#### 方式 A：Git URL 安装（推荐）

1. 打开 Unity 项目 → **Window > Package Manager**
2. 点击左上角 **`+`** → **Add package from git URL...**
3. 粘贴以下链接，点击 **Add**：

```
https://github.com/CoplayDev/coplay-unity-plugin.git#beta
```

4. 等待 Unity 下载并编译包（约 1-2 分钟）

#### 方式 B：迁移到 Aura（官方推荐）

如需使用最新功能和长期支持，访问 [tryaura.dev](https://www.tryaura.dev/) 按指引安装最新版 Aura for Unity。

### 使用方法

1. 安装完成后，Unity 菜单出现 **Coplay** 选项
2. 选择 **Coplay → Toggle Window**，或按快捷键：
   - Windows / Linux：`Ctrl + G`
   - macOS：`Cmd + G`
3. 首次打开需要登录（点击 **Login** 按钮，浏览器跳转登录页）
4. 登录后即可在 Unity 窗口内用自然语言与 AI 对话

### 核心功能亮点

| 功能 | 说明 |
|------|------|
| **自然语言控制** | 在窗口内用中文/英文描述需求，AI 直接在 Unity 执行 |
| **Context-Aware** | 自动理解你的项目结构、Asset、场景和脚本 |
| **多模型支持** | GPT-4.1 / Gemini 2.5 Pro / Claude 4-Sonnet / Grok 3 可切换 |
| **Pipelines** | 录制操作序列，下次一键回放，减少重复劳动 |
| **代码生成与调试** | 生成脚本、定位错误、提供上下文相关建议 |

---

## ⚠️ 常见问题与排错

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| Git URL 安装失败 | URL 格式有误或 Git 未安装 | 确认 Git 已安装；优先用 `unity-mcp.git?path=/MCPForUnity#main` |
| AI 连不上 Unity（方案一） | 忘记启动 Bridge | 打开 **Window > MCP for Unity**，确认 Bridge 已启动 |
| `claude` 命令找不到 | 环境变量未刷新 | 重启终端，或手动将 npm 全局包路径加入 PATH |
| Python / uv 缺失（方案一） | 未安装必要依赖 | 安装 Python 3.10+ 和 uv |
| Unity 2021 不支持（方案二） | 插件最低要求 2022.3 | 方案一支持 2021.3+；方案二需升级到 2022.3+ |
| 任务超时（方案一） | 默认超时时间过短 | 设置 `MCP_TOOL_TIMEOUT=720000`（12 分钟） |
| Coplay 账号登录失败（方案二） | 账号体系已迁移 | 访问 [tryaura.dev](https://www.tryaura.dev/) 注册新账号 |
| macOS pyenv 路径问题（方案一） | pyenv shims 不在 PATH | v9.4.8+ 已修复，升级插件即可 |
| VisionOS 编译错误（方案一） | 枚举兼容问题 | v9.7.3 已修复 |

---

## 🚀 方案一：MCP 工具能力全览（v10.0.0 共 47 个工具）

| 工具分类 | 代表工具 | 能做什么 |
|----------|----------|----------|
| **场景管理** | `manage_scene`, `manage_gameobject` | 创建/删除/查找 GameObject，多场景编辑 |
| **脚本操作** | `create_script`, `manage_script`, `validate_script` | 生成 C# 脚本、修改代码、Roslyn 静态验证 |
| **代码执行** | `execute_code` ⭐ v9.6.5+ | 在 Unity Editor 中运行任意 C# 代码片段 |
| **物理系统** | `manage_physics` ⭐ v9.6.4+ | 配置刚体、碰撞体、关节，执行射线检测 |
| **动画系统** | `manage_animation` | 管理 Animator Controller 和动画片段 |
| **渲染与材质** | `manage_material`, `manage_graphics`, `manage_shader` | 修改材质、调整后处理、URP 设置 |
| **摄像机** | `manage_camera` ⭐ v9.5.2+ | 管理摄像机，支持 Cinemachine，Scene View 截图 |
| **UI 系统** | `manage_ui` | 创建和管理 Canvas、UI 元素 |
| **Prefab** | `manage_prefabs` | 完整 Prefab Stage 工作流 |
| **项目构建** | `manage_build` | 多平台打包，配置 Player Settings |
| **性能分析** | `manage_profiler` ⭐ v9.6.4+ | 启动 Profiler，获取内存快照、帧时间 |
| **包管理** | `manage_packages` ⭐ v9.5.3+ | 安装/删除 Unity 包，管理依赖 |
| **AI 资产生成** | `asset_generation` 🆕 v10.0.0 | AI 生成 3D 模型（导入）+ 2D 图像 |
| **文档与反射** | `unity_docs`, `unity_reflect` | 查询 Unity 官方文档，反射检查 C# API |

### 实战提示词示例

```
# 场景操作
在场景中创建一个红色的立方体，并给它添加一个 Rigidbody 组件。

# 脚本生成
帮我写一个 FPS 玩家控制器脚本，实现 WASD 移动、鼠标视角控制和空格键跳跃，
并挂载到名为 'Player' 的对象上。

# 性能优化
启动 Profiler，运�� 5 秒后获取内存快照，告诉我哪些对象占用内存最多。

# 项目构建
切换到 Android 平台，将 Bundle Identifier 设置为 com.mycompany.mygame，然后触发构建。

# 代码执行（v9.6.5+）
在 Editor 中执行代码：统计场景内所有 MeshRenderer 的三角面总数，打印到 Console。

# AI 资产生成（v10.0.0+）
用 AI 生成一个低多边形风格的树模型并导入到项目中。
```

---

## 📅 版本更新历史（方案一 近期重要版本）

| 版本 | 发布日期 | 重点更新 |
|------|----------|----------|
| **[v10.0.0](https://github.com/CoplayDev/unity-mcp/releases/tag/v10.0.0)** | 2026-06-30 | 🆕 AI 资产生成（3D/2D）、品牌重塑 |
| [v9.7.3](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.7.3) | 2026-06-15 | Kimi Code CLI 支持、VisionOS 修复 |
| [v9.7.0](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.7.0) | 2026-05-22 | 一键客户端连接、UI Toolkit 截图修复 |
| [v9.6.5](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.6.5) | 2026-04-03 | `execute_code` 工具（在 Editor 执行任意 C#） |
| [v9.6.4](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.6.4) | 2026-03-31 | `manage_profiler`、Prefab Stage、物理管理 |
| [v9.5.3](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.5.3) | 2026-03-09 | `manage_graphics`、`manage_packages`、OpenClaw |
| [v9.4.8](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.4.8) | 2026-03-06 | 全新 UI、Gemini CLI/Qwen Code/GitHub Copilot 支持 |

---

## 进阶功能（方案一）

- **多 Unity 实例路由**：同时控制多个项目，[Multi-Instance Routing](https://coplaydev.github.io/unity-mcp/guides/multi-instance)
- **工具分组**：按需启用/禁用工具（vfx / animation / ui / testing 等），[Tool Groups](https://coplaydev.github.io/unity-mcp/guides/tool-groups)
- **远程服务器鉴权**：团队共享部署，[Remote Server Auth](https://coplaydev.github.io/unity-mcp/guides/remote-server-auth)
- **Docker MCP 网关**：适合 CI/CD 场景，参考 [v9.1.0 发布说明](https://github.com/CoplayDev/unity-mcp/releases/tag/v9.1.0)
- **Roslyn 脚本验证**：提前发现 C# 错误，[Roslyn Validation](https://coplaydev.github.io/unity-mcp/guides/roslyn)
- **v10 升级迁移**：[v10 Migration Guide](https://coplaydev.github.io/unity-mcp/migrations/v10)

---

## 📚 参考资料

- [MCP for Unity 官方文档](https://coplaydev.github.io/unity-mcp/)
- [CoplayDev/unity-mcp GitHub](https://github.com/CoplayDev/unity-mcp)
- [CoplayDev/coplay-unity-plugin GitHub](https://github.com/CoplayDev/coplay-unity-plugin)
- [完整工具目录](https://coplaydev.github.io/unity-mcp/reference/tools/)
- [Release Notes（完整版本历史）](https://coplaydev.github.io/unity-mcp/releases)
- [MCP for Unity on Unity Asset Store](https://assetstore.unity.com/packages/tools/generative-ai/mcp-for-unity-ai-driven-development-329908)
- [Aura for Unity（Coplay 继任者）](https://www.tryaura.dev/)
- [Model Context Protocol 官方规范](https://modelcontextprotocol.io)
- [Discord 社区](https://discord.gg/y4p8KfzrN4)

---

*本文档基于 CoplayDev 官方仓库整理。方案一当前版本 **v10.0.0**（2026-06-30），方案二当前版本 **v8.20.3**。如有更新请参考官方文档。*
