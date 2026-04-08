# Coplay (Unity + AI) 完整使用教程与最佳实践

> **Coplay** 是一款专为 Unity 游戏开发者设计的 AI 助手。通过 **MCP（Model Context Protocol，模型上下文协议）**，它能够将你常用的 AI 客户端（如 Claude Code、Cursor、VS Code 等）与 Unity 编辑器直接连接，让 AI 帮你读取场景、修改组件、生成脚本，甚至自动化执行各种 Unity 任务。

- **官方文档**：[docs.coplay.dev](https://docs.coplay.dev)
- **GitHub 仓库**：[CoplayDev/unity-mcp](https://github.com/CoplayDev/unity-mcp)
- **当前版本**：v9.6.6 (beta)

---

## 一、核心概念解析

在开始安装之前，先理清 Coplay 生态中的三个核心角色：

| 角色 | 是什么 | 作用 |
|------|--------|------|
| **Coplay Unity 插件** | 安装在 Unity 编辑器内部的包 | 接收指令，在 Unity 内执行操作（修改场景、创建脚本等） |
| **Coplay MCP 服务器** | 通过 `uvx` 运行的本地协议服务器 | 在 AI 客户端和 Unity 插件之间传递信息 |
| **AI 客户端** | Claude Code / Cursor / VS Code 等 | 你发出自然语言指令的地方 |

整个工作流可以理解为一条链路：

> **你的自然语言指令** → **AI 客户端** → **Coplay MCP 服务器** → **Unity 内部插件** → **Unity 执行操作**

### 系统架构示意图

![Coplay 系统架构示意图](images/coplay_architecture.png)

---

## 二、安装前置准备

在开始安装之前，请确保以下环境已就绪：

| 依赖项 | 要求版本 | 说明 |
|--------|----------|------|
| **Unity Editor** | 2022+ (推荐 LTS) | 2021.3 LTS 也可用，但会有少量 UI 警告 |
| **Git** | 任意版本 | 用于通过 URL 拉取 Unity 包，必须安装 |
| **Python** | 3.11 或更高 | Coplay MCP 服务器的运行环境 |
| **uv / uvx** | 最新版 | Python 包管理工具，用于启动 MCP 服务器 |
| **Node.js + npm** | 可选 | 如果你使用 Claude Code CLI，需要通过 npm 安装 |
| **AI 客户端** | 任意 | Claude Code / Cursor / VS Code / Claude Desktop |

---

## 三、详细安装步骤

### 完整安装流程图

![Coplay 安装流程图](images/coplay_install_flow.png)

---

### 第一步：在 Unity 中安装 Coplay 插件

官方推荐使用 GitHub 上 `unity-mcp` 仓库的 `beta` 分支，这是目前最新且经社区验证最稳定的版本。

1. 打开你的 Unity 项目。
2. 在顶部菜单栏选择 **Window > Package Manager**。
3. 点击左上角的 **`+`** 号图标。
4. 选择 **Add package from git URL...**。
5. 在输入框中粘贴以下链接，然后点击 **Add**：

   ```
   https://github.com/CoplayDev/unity-mcp.git?path=/MCPForUnity#beta
   ```

6. 等待 Unity 下载并编译包（约需 1-2 分钟）。

> **备用安装方式（Unity Asset Store）**：访问 [MCP for Unity on Asset Store](https://assetstore.unity.com/packages/tools/generative-ai/mcp-for-unity-ai-driven-development-329908)，点击 `Add to My Assets`，然后通过 Package Manager 导入。

---

### 第二步：在 Unity 中启动 MCP 桥接服务

安装完包后，**必须手动启动桥接服务**，否则 AI 客户端无法连接到 Unity。这一步是最容易被遗忘的！

1. 在 Unity 顶部菜单栏选择 **Window > MCP for Unity**。
2. 在弹出的窗口中，点击 **Auto-Setup**（自动检测并配置依赖）。
3. 如果系统提示缺少 Python 或 uv 等依赖，请根据提示进行安装后重试。
4. 确认环境无误后，点击 **Start Bridge**（或 Start Server）。
5. 服务启动后，会在本地 `localhost:8080` 端口监听连接。

---

### 第三步：在 AI 客户端中接入 Coplay MCP

根据你使用的 AI 客户端不同，配置方式也有所区别：

#### 方案 A：Claude Code CLI（推荐）

```bash
# 第一步：安装 Claude Code CLI（如果还没装）
npm install -g @anthropic-ai/claude-code

# 第二步：验证安装
claude --version

# 第三步：注册 Coplay MCP 服务器
claude mcp add --scope user --transport stdio coplay-mcp \
  --env MCP_TOOL_TIMEOUT=720000 \
  -- uvx --python ">=3.11" coplay-mcp-server@latest

# 第四步：查看已注册的 MCP 服务器列表
claude mcp list
```

> **注**：`MCP_TOOL_TIMEOUT=720000` 表示超时时间为 12 分钟（720,000 毫秒）。某些 Unity 任务（如光照烘焙、复杂构建）可能需要较长时间，因此官方建议设置较大的超时值。

---

#### 方案 B：Claude Desktop

1. 打开 Claude Desktop 应用。
2. 进入 **Settings > Developer > Edit Config**，打开 `claude_desktop_config.json` 文件。
3. 添加以下配置：

   ```json
   {
     "mcpServers": {
       "coplay-mcp": {
         "command": "uvx",
         "args": ["--python", ">=3.11", "coplay-mcp-server@latest"],
         "env": {
           "MCP_TOOL_TIMEOUT": "720000"
         }
       }
     }
   }
   ```

4. 完全退出并重启 Claude Desktop。

---

#### 方案 C：Cursor / Cline

在 Cursor 的 MCP 配置界面（Settings > Features > MCP）中添加以下 JSON 配置：

```json
{
  "mcpServers": {
    "coplay-mcp": {
      "autoApprove": [],
      "disabled": false,
      "timeout": 720,
      "type": "stdio",
      "command": "uvx",
      "args": ["--python", ">=3.11", "coplay-mcp-server@latest"]
    }
  }
}
```

---

#### 方案 D：VS Code

1. 按下 `Ctrl/Cmd + Shift + P` 打开命令面板。
2. 输入并选择 **MCP: Add Server**。
3. 选择 **stdio** 作为传输方式。
4. 输入启动命令：`uvx --python >=3.11 coplay-mcp-server@latest`
5. 将标识名（Identifier）填写为：`coplay-mcp`

---

## 四、测试与验证

完成所有步骤后，在你的 AI 客户端聊天窗口中输入以下提示词来验证连接：

```
List all of the open unity editors using Coplay MCP
```

如果 AI 能够准确返回你当前正在运行的 Unity 项目名称和状态，说明整个链路已经打通，安装成功！

---

## 五、常见避坑指南

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| Git URL 安装失败 | 官方在不同时期提供过多个 URL | 优先使用 `unity-mcp.git?path=/MCPForUnity#beta` |
| AI 连不上 Unity | 忘记启动 Bridge 服务 | 打开 **Window > MCP for Unity**，确认 Bridge 已启动 |
| `claude` 命令找不到 | 环境变量（PATH）未刷新 | 重启终端，或手动将 npm 全局包路径加入 PATH |
| Python / uv 缺失 | 未安装必要依赖 | 安装 Python 3.11+ 和 uv，确保在终端中可用 |
| Unity 版本太旧 | 2021 以下版本兼容性差 | 升级到 Unity 2022+ LTS 版本 |
| 任务超时 | 默认超时时间过短 | 设置 `MCP_TOOL_TIMEOUT=720000`（12 分钟） |

---

## 六、Coplay 的强大能力与应用场景

连接成功后，你可以用自然语言让 AI 帮你完成各种 Unity 开发任务。以下是 Coplay 支持的主要工具分类：

| 工具分类 | 代表工具 | 能做什么 |
|----------|----------|----------|
| **场景管理** | `manage_scene`, `manage_gameobject` | 创建/删除/查找 GameObject，多场景编辑 |
| **脚本操作** | `create_script`, `manage_script`, `validate_script` | 生成 C# 脚本，修改代码，静态验证 |
| **物理系统** | `manage_physics` | 配置刚体、碰撞体、关节，执行射线检测 |
| **动画系统** | `manage_animation` | 管理 Animator Controller 和动画片段 |
| **渲染与材质** | `manage_material`, `manage_graphics`, `manage_shader` | 修改材质、调整后处理、URP 设置 |
| **UI 系统** | `manage_ui` | 创建和管理 Canvas、UI 元素 |
| **项目构建** | `manage_build` | 多平台打包，配置 Player Settings |
| **性能分析** | `manage_profiler` | 启动 Profiler，获取内存快照和帧时间 |
| **包管理** | `manage_packages` | 安装/删除 Unity 包，管理依赖 |
| **文档与反射** | `unity_docs`, `unity_reflect` | 查询 Unity 官方文档，反射检查 C# API |

### 实战提示词示例

以下是一些可以直接在 AI 客户端中使用的提示词，感受 Coplay 的强大之处：

```
# 场景操作
在场景中创建一个红色的立方体，并给它添加一个 Rigidbody 组件。

# 脚本生成
帮我写一个 FPS 玩家控制器脚本，实现 WASD 移动、鼠标视角控制和空格键跳跃，并挂载到名为 'Player' 的对象上。

# 性能优化
启动 Profiler，运行 5 秒后获取内存快照，告诉我哪些对象占用内存最多。

# 项目构建
切换到 Android 平台，将 Bundle Identifier 设置为 com.mycompany.mygame，然后触发一次构建。

# 场景检查
检查当前场景中所有的 Collider，找出没有分配物理材质（Physics Material）的对象。
```

---

## 七、参考资料

- [Coplay 官方文档](https://docs.coplay.dev)
- [CoplayDev/unity-mcp GitHub 仓库](https://github.com/CoplayDev/unity-mcp)
- [Coplay MCP Quick Start](https://docs.coplay.dev/coplay-mcp/guide)
- [Coplay Installation Guide](https://docs.coplay.dev/getting-started/installation)
- [MCP for Unity on Unity Asset Store](https://assetstore.unity.com/packages/tools/generative-ai/mcp-for-unity-ai-driven-development-329908)
- [Model Context Protocol 官方规范](https://modelcontextprotocol.io)

---

*本文档由 Manus AI 整理，基于 Coplay 官方文档及社区实践经验编写。如有更新，请参考官方最新文档。*
