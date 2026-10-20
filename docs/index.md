---
layout: default
title: 用户指南
---

# Eddy 用户指南

## 下载与安装

在 [Releases](https://github.com/RyaoVen/Eddy/releases) 选择系统和芯片对应安装包。macOS 的 Intel 芯片选择 amd64，Apple Silicon 选择 arm64；Windows 按“设置 → 系统 → 系统信息”所示架构选择。不能把模拟运行视为原生架构验收。

使用 SHA256SUMS 校验下载。macOS：`shasum -a 256 -c SHA256SUMS`；Windows PowerShell：`Get-FileHash .\Eddy-安装文件.exe -Algorithm SHA256`，与清单对应条目比较。

macOS 打开 DMG，将 Eddy 拖到 Applications 后启动。Windows 运行 setup.exe，安装器在本机离线准备 Python、LibreOffice 和 WebView2；安装失败会停止并显示错误。

这是内部测试版：macOS 尚未公证，Windows 尚未正式代码签名，系统可能显示安全提示。仅使用来自本仓库且校验一致的包。

## 本地与远程

首次启动选择“本地使用”或“连接服务器”。设置 → 通用 → 运行模式与服务器可以测试连接和切换；登录前或连接中断时，也可使用窗口底部的模式入口。

本地模式使用此电脑的数据；远程模式使用服务器授权工作区，应用不会把本机目录同步到服务器。切换前须完成或取消运行、排队及待处理任务。远程断连不会切到本地，也不会自动重发任务。

远程默认要求有效 HTTPS 证书。回环 HTTP 可用于本机测试；私有 IP 的 HTTP 还需明确勾选内网测试选项，凭据与业务内容不加密。应用不提供忽略 TLS 证书错误的开关。远程连接重启后需要重新登录。

## 配置模型

本地模式将 `AUTOMIND_LLM_BASE`、`AUTOMIND_LLM_KEY`、`AUTOMIND_LLM_MODEL` 写入用户数据目录的 `llm.env`，重启应用。请勿把含密钥的文件上传到 GitHub。macOS 数据目录为 `~/Library/Application Support/Eddy`，Windows 为 `%APPDATA%\Eddy`。

企业服务器通过 model.env 注入模型密钥，并由管理员在管理端配置路由和预算。模型未配置时会显示提示；离线办公依赖完整不代表可以离线调用云端模型。

## 工作区、PDF 与数据

PDF 导出使用安装包内的 LibreOffice 和中文字体，无需另外安装办公软件。远程模式的导出在服务器执行。预览与下载遵守账号的工作区授权。

应用更新覆盖程序资源，保留用户数据；Windows 卸载默认保留数据。请在退出后备份数据目录。服务端请使用部署包的停机备份与恢复流程。

## Linux 快速部署

下载对应架构镜像归档与 deploy.tar.gz，校验后解压部署包。执行 `bash manage.sh init`，修改 .env 域名、镜像与证书路径；放入匹配域名的有效 TLS 证书。使用 `bash manage.sh load 镜像.tar.gz` 导入离线镜像，或使用已发布的 GHCR 镜像。

`bash manage.sh account` 交互初始化管理员，`bash manage.sh start` 启动。默认仅绑定 `127.0.0.1:8443`，通过部署包中的 Nginx HTTPS 反向代理示例提供外部入口，SSE 不缓冲。不要复制示例域名作为实际域名。

`bash manage.sh stop` 停止；`bash manage.sh logs` 查看日志；`bash manage.sh backup 文件.tar.gz` 停机备份，连同匹配镜像、.env、model.env 和 TLS 配置安全保管。恢复使用匹配版本和空数据卷；回滚保留原卷，使用新的 Compose 项目名恢复，避免覆盖现有数据。不能跨版本直接复用事务数据。

## 故障诊断与反馈

启动失败先检查下载校验、系统架构与运行环境错误。桌面“帮助 → 诊断信息／打开日志目录”可查看版本和本地日志。远程登录失败检查服务地址、证书、账号状态与网络；PDF 失败检查完整包是否损坏及临时目录是否可写。

反馈请填写系统、芯片、应用版本、操作步骤和脱敏错误；不上传模型密钥、密码、原始业务文件或完整个人日志。测试矩阵中的构建通过与安装运行通过是独立状态。
