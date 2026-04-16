# 编码与乱码处理

## 本次定位结论

当前仓库里，已跟踪的源码文件在抽样扫描下没有发现明显的“非法 UTF-8 文件”。

这次看到的很多乱码，更像是“终端显示乱码”而不是“源码本身损坏”。

现场环境里已经确认到以下现象：

- Windows 代码页仍是 `936`
- PowerShell 的 `$OutputEncoding` 是 `us-ascii`
- `ConsoleInputEncoding` 是 `gb2312`

这会导致同一份 UTF-8 源码在不同工具里出现不同表现：

- 编辑器里正常
- 终端里乱码
- `Get-Content`、`Select-String` 输出中文异常

## 仓库约束

为了降低后续乱码风险，仓库新增了以下配置：

- 根目录 `.editorconfig`
  - 文本源码统一按 `utf-8`
  - 行尾统一按 `lf`
- 根目录 `.vscode/settings.json`
  - VS Code 工作区默认按 `utf8`
  - 关闭自动猜编码，减少误判

## PowerShell 建议

进入仓库前，先执行：

```powershell
chcp 65001
[Console]::InputEncoding  = [System.Text.UTF8Encoding]::new($false)
[Console]::OutputEncoding = [System.Text.UTF8Encoding]::new($false)
$OutputEncoding = [System.Text.UTF8Encoding]::new($false)
```

执行后再查看文件、跑脚本、做批量替换，能明显减少中文乱码。

## 编辑器建议

### VS Code

- 保持工作区配置生效
- 不要开启自动猜编码保存
- 打开历史文件时，如果发现显示异常，先检查右下角编码，不要直接保存

### IntelliJ IDEA

- `Global Encoding = UTF-8`
- `Project Encoding = UTF-8`
- `Default encoding for properties files = UTF-8`

## 日常规则

1. 新增文件一律使用 UTF-8。
2. 发现历史文件在终端里乱码时，先判断是“显示问题”还是“文件本体问题”。
3. 如果编辑器里正常、编译正常、Git diff 也正常，就不要仅因为终端乱码去重写中文内容。
4. 只有在确认文件保存后真的被破坏时，才做转码修复。
