# 图吧工具箱社区插件库

这是[图吧工具箱 WinUI 3](https://github.com/luolangaga/Tubas)的社区资源仓库，用于收集和分发实用的 Windows 工具，并展示社区成员提交的电脑性能报告。

## 仓库内容

| 路径 | 说明 |
| --- | --- |
| [`plugins/`](plugins/) | 社区工具。每个工具使用独立目录保存 `plugin.json`、压缩包和图标等文件 |
| [`reports/`](reports/) | 社区成员提交的性能报告及延迟测试图片 |
| [`rules/malware.yar`](rules/malware.yar) | 插件安全扫描使用的 YARA 规则 |
| [`scripts/`](scripts/) | 元数据、压缩包可用性和资源下载校验脚本 |
| [`.github/workflows/`](.github/workflows/) | 插件审核、病毒扫描和排行榜生成流程 |

排行榜和社区工具索引由 GitHub Actions 手动生成，生成文件为仓库根目录下的 `leaderboard.json`、`leaderboard/details/` 和 `plugins-index.json`。

## 提交社区工具

### 1. 准备文件

在 `plugins/<分类>/<工具目录>/` 下创建工具目录。目录名建议使用小写英文、数字和连字符，避免空格和特殊字符。通常至少包含：

```text
plugins/
└── 其他工具/
    └── example-tool/
        ├── plugin.json
        ├── ExampleTool.zip
        └── icon.png
```

压缩包应包含可运行的工具文件，不要提交无关的安装器、个人文件或敏感信息。请确认工具来源可靠，并在提交前自行检查文件安全性。

### 2. 填写 `plugin.json`

最小示例：

```json
{
  "id": "example-tool",
  "name": "Example Tool",
  "version": "1.0.0",
  "description": "工具简介",
  "category": "其他工具",
  "tags": ["Windows"],
  "launchTarget": "ExampleTool.exe",
  "author": "你的 GitHub 用户名",
  "homepage": "https://example.com",
  "file": "ExampleTool.zip",
  "icon": "icon.png"
}
```

必填字段为 `id`、`name` 和 `category`。常用字段说明：

| 字段 | 说明 |
| --- | --- |
| `id` | 全局唯一标识，只能使用字母、数字、下划线和连字符 |
| `name` | 工具显示名称 |
| `version` | 工具版本，建议使用 `主版本.次版本.修订版本` 格式 |
| `description` | 简短、准确的功能介绍 |
| `category` | 工具分类，须与现有分类保持一致 |
| `tags` | 便于搜索的标签数组 |
| `launchTarget` | 压缩包内要启动的文件；包含多个可执行文件时建议填写 |
| `file` | 同目录下的 ZIP 文件名；也可以使用 `downloadUrl` 指向外部下载地址 |
| `icon` | 压缩包内的图标文件名，支持 PNG、ICO、JPG、JPEG 和 SVG |
| `archVariants` | 可选的架构变体数组，架构值支持 `x86`、`x64` 和 `ARM64` |

请不要重复使用已有的 `id`，并确保 `file`、`icon` 和 `launchTarget` 的路径与实际压缩包内容一致。

### 3. 提交 Pull Request

1. 从本仓库创建分支，并添加工具文件。
2. 在本地运行校验脚本（见下文）。
3. 提交 Pull Request，并说明工具用途、来源和测试情况。
4. 在 Pull Request 评论区发送 `@tubabot review`，触发自动病毒扫描。
5. 等待维护者审核。扫描通过不代表一定会合并，维护者仍会进行人工复核。

## 本地校验

校验元数据：

```bash
python3 scripts/validate-metadata.py plugins/其他工具/example-tool/plugin.json
```

校验 ZIP 内容、启动文件和图标：

```bash
python3 scripts/validate-usability.py \
  plugins/其他工具/example-tool/plugin.json \
  plugins/其他工具/example-tool
```

两个脚本都会输出 JSON 结果。`status` 为 `pass` 表示未发现阻止提交的问题；`warnings` 仍建议在创建 Pull Request 前处理。

## 提交性能报告

性能报告应包含真实硬件和测试结果，图片放在 `reports/latency-images/` 或对应报告目录中。请勿提交姓名、序列号、激活码、账号信息等隐私或敏感数据。报告合入后，维护者可以在 Actions 页面手动运行 **Update leaderboard & community index**，更新排行榜和工具索引。

## 安全说明

社区工具来自第三方，使用前请自行确认来源和用途。仓库的自动流程会使用 ClamAV、YARA 和可选的 VirusTotal 对 Pull Request 中的文件进行扫描；任何扫描结果都不能替代用户对软件的最终判断。
