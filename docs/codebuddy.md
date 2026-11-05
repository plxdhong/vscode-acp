# CodeBuddy CLI 使用说明

本扩展通过 CodeBuddy 官方提供的 ACP stdio 接口 `codebuddy --acp` 连接本机 CLI，无需协议转换器。内置代理列表中的名称为 **CodeBuddy CLI**。

## 安装与连接

1. 按照 [CodeBuddy 安装指南](https://www.codebuddy.cn/docs/cli/installation)安装 CLI。npm 安装方式为：

   ```sh
   npm install -g @tencent-ai/codebuddy-code
   codebuddy --version
   ```

2. 在终端运行 `codebuddy`，按 CLI 提示完成账号登录，然后退出 CLI。扩展启动的进程会复用本机 CodeBuddy 配置。npm 版本所需 Node.js 版本以 CodeBuddy 官方说明为准。
3. 安装本分支构建的 VSIX，重新加载 VS Code，打开一个项目文件夹。
4. 打开 ACP Client 面板，点击 **CodeBuddy CLI**，开始聊天。

CLI 原生安装和 Homebrew 安装同样适用，只要 VS Code 启动的进程能找到 `codebuddy`。本分支的 VSIX 保留上游扩展 ID `formulahendry.acp-client`，安装时会替换同 ID 的扩展；如需持续使用此分支，请关闭该扩展的 Marketplace 自动更新。

## 自定义配置

默认配置为：

```json
{
  "acp.agents": {
    "CodeBuddy CLI": {
      "command": "codebuddy",
      "args": ["--acp"],
      "env": {}
    }
  }
}
```

已有自定义 `acp.agents` 时，在现有对象中加入 `CodeBuddy CLI` 条目，保留自己配置的其他代理。默认不指定模型、权限模式或区域，沿用本机 CLI 配置；模型与模式选择器根据服务端实际返回的 ACP 能力显示。

如果找不到命令，把 `command` 改成可执行文件的绝对路径。Windows npm 安装可通过 `where.exe codebuddy` 查找 `.cmd` 路径；macOS/Linux 使用 `command -v codebuddy`。Windows JSON 路径中的反斜杠需要写成 `\\`。

需要 API Key 时，在启动 VS Code 的环境中配置，避免将密钥放进项目的 `.vscode/settings.json`。按照 [CodeBuddy ACP 文档](https://www.codebuddy.cn/docs/cli/acp)，使用 `CODEBUDDY_API_KEY` 时应同时选择对应区域：

| 版本 | `CODEBUDDY_INTERNET_ENVIRONMENT` |
| --- | --- |
| 中国版 | `internal` |
| 海外版 | 不设置 |
| iOA 版 | `ioa` |

也可仅在代理的 `env` 中设置区域，例如 `"env": { "CODEBUDDY_INTERNET_ENVIRONMENT": "internal" }`。账号登录方式按 CLI 指引配置即可。

## 故障排查与验收

- **列表中没有 CodeBuddy CLI**：确认安装的是本分支 VSIX，并重新加载窗口；检查用户及工作区的 `acp.agents` 自定义值。
- **命令不存在或立即退出**：在 VS Code 集成终端运行 `codebuddy --version`；必要时配置绝对路径，然后执行 `ACP: Restart Agent`。
- **认证失败**：先在终端运行 `codebuddy` 完成登录；API Key 用户检查区域是否匹配。扩展的通用 ACP 认证流程仍然可用，具体交互由 CLI 提供。
- **连接后没有响应**：确认参数为 `["--acp"]`，不要使用 `--serve` HTTP 服务；执行 `ACP: Show Log` 和 `ACP: Show Protocol Traffic` 查看初始化错误。
- **基础验收**：连接后发送一句问候；请求只读查看一个项目文件；检查工具调用展示和权限确认；测试取消响应、新建会话，以及服务端提供时的模型切换、历史会话恢复和斜杠命令。

权限策略保持扩展默认的 `ask`，每项能力以已安装 CLI 宣告的 ACP 能力为准。
