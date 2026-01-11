# gocov - Go 覆盖率分析工具 (维护分支)

## 项目概述

⚠️ **注意**: 原始仓库 (github.com/axw/gocov) 已不再维护。这是活跃维护的分支，包含对较新 Go 版本的兼容性更新。

gocov 是 Go 编程语言的代码覆盖率分析工具。与标准的 `go test -cover` 命令相比，它提供了增强的覆盖率报告功能。该工具将 Go 的覆盖率配置文件格式转换为 JSON 交换格式，并提供各种命令来分析和报告覆盖率数据。

### 主要特性
- 将 Go 覆盖率配置文件转换为 JSON 格式以便于处理
- 提供详细的函数级覆盖率分析
- 提供源代码注释，显示已覆盖/未覆盖的行
- 生成全面的覆盖率报告
- 与 Go 的测试基础设施无缝集成
- 与较新 Go 版本（最高至 Go 1.25）兼容
- 持续维护和错误修复

### 架构
该项目由几个主要组件组成：
- **主包 (`gocov/`)**：包含 CLI 接口，具有 `test`、`convert`、`report` 和 `annotate` 等命令
- **数据结构 (`gocov.go`)**：定义表示覆盖率数据的核心类型，如 `Package`、`Function` 和 `Statement`
- **转换工具 (`gocov/convert/`)**：处理从 Go 的覆盖率配置文件格式到 JSON 的转换
- **报告工具 (`gocov/report.go`)**：生成文本覆盖率报告
- **注释工具 (`gocov/annotate.go`)**：提供源代码注释功能
- **实用函数 (`gocovutil/`)**：包含用于处理覆盖率数据的辅助函数

## 构建和运行

### 先决条件
- Go 1.25.0 或更高版本
- Git 用于克隆仓库

### 安装
```bash
go install github.com/dreamnear/gocov/gocov@latest
```

### 手动构建
```bash
# 克隆仓库
git clone https://github.com/dreamnear/gocov.git
cd gocov

# 安装依赖项
go mod tidy

# 构建工具
go build ./gocov/

# 或全局安装
go install ./gocov/
```

### 从原始仓库迁移

如果您之前使用的是 `github.com/axw/gocov`，只需将导入路径和安装命令更新为此仓库即可。

## 命令

#### `gocov test`
使用隐式的 `-coverprofile` 标志运行 `go test` 并输出 `gocov convert` 的结果：
```bash
gocov test [go-test-flags-and-packages]
```

#### `gocov convert`
将 `go test -coverprofile` 生成的覆盖率配置文件转换为 gocov 的 JSON 格式：
```bash
go test -coverprofile=c.out
gocov convert c.out
```

#### `gocov report`
从 JSON 格式的覆盖率数据生成文本报告：
```bash
gocov convert c.out | gocov report
# 或
gocov test | gocov report
```

#### `gocov annotate`
生成指定函数的源代码列表，并用覆盖率信息进行注释：
```bash
gocov convert c.out | gocov annotate - 'package.function'
```

## 开发约定

### 代码结构
- 主要数据结构在 `gocov.go` 中定义，包含用于累积覆盖率数据的方法
- 每个命令（test、convert、report、annotate）在 `gocov/` 目录中的单独文件中实现
- 转换逻辑保留在 `gocov/convert/` 子目录中
- 实用函数组织在 `gocovutil/` 目录中

### 测试
项目包括核心功能的单元测试：
```bash
# 运行所有测试
go test ./...

# 运行带覆盖率的测试
go test -v ./...
```

### 数据模型
核心数据模型由三种主要类型组成：
- `Package`：表示具有名称和函数的 Go 包
- `Function`：表示具有名称、文件位置和语句的函数
- `Statement`：表示具有位置和执行次数的代码语句

### 累积逻辑
每种数据类型都实现了 `Accumulate` 方法，该方法将另一个实例的覆盖率数据合并到当前实例中，允许合并多次测试运行的覆盖率结果。

## 已知问题
- 在 `gocovutil/packages.go` 中似乎存在一个错误，其中文件被打开但 `os.Stdin` 被追加到文件切片而不是实际的文件句柄
- 该工具要求在生成覆盖率和注释/报告之间源代码保持不变

## 相关工具
- [GoCovGUI](http://github.com/nsf/gocovgui/)：gocov 的简单 GUI 包装器
- [gocov-html](https://github.com/matm/gocov-html)：从 gocov JSON 生成 HTML 输出
- [gocov-xml](https://github.com/AlekSi/gocov-xml)：以 Cobertura 格式生成 XML 输出，用于 Jenkins 等 CI 系统