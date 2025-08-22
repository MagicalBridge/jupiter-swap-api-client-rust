## 概述

Rust生态系统提供了一套完整的代码格式化和质量检查工具，主要包括：

1. **rustfmt** - 官方代码格式化工具
2. **clippy** - 官方代码检查工具（linter）
3. **rust-analyzer** - 语言服务器，提供IDE支持

## 配置文件

### 1. rustfmt.toml

项目根目录的 `rustfmt.toml` 文件用于配置代码格式化规则：

```toml
# 基本设置
edition = "2021"
max_width = 100          # 每行最大字符数
hard_tabs = false        # 使用空格而不是制表符
tab_spaces = 4           # 缩进空格数

# 函数参数布局
fn_params_layout = "Tall"  # 函数参数垂直排列

# 其他设置
newline_style = "Unix"           # 使用Unix换行符
remove_nested_parens = true     # 移除不必要的嵌套括号
use_small_heuristics = "Default" # 使用默认的小型启发式规则

# 链式调用和数组设置
chain_width = 60                    # 链式调用宽度
single_line_if_else_max_width = 50  # 单行if-else最大宽度
array_width = 60                    # 数组宽度
```

### 2. clippy.toml

项目根目录的 `clippy.toml` 文件用于配置代码检查规则：

```toml
# 函数参数和复杂度阈值
too-many-arguments-threshold = 7      # 函数参数过多的阈值
type-complexity-threshold = 250       # 类型复杂度阈值
cognitive-complexity-threshold = 30   # 认知复杂度阈值
too-many-lines-threshold = 100        # 函数行数过多的阈值

# 命名相关
enum-variant-name-threshold = 3              # 枚举变体名称阈值
single-char-binding-names-threshold = 4      # 单字符变量名阈值

# 其他设置
missing-docs-in-crate-items = false    # 是否要求公共项目有文档
avoid-breaking-exported-api = false    # 是否避免破坏导出的API
```

## 常用命令

### 格式化相关

```bash
# 格式化所有代码
cargo fmt

# 检查代码格式是否正确（不修改文件）
cargo fmt --check

# 格式化指定文件
rustfmt src/main.rs
```

### 代码检查相关

```bash
# 运行Clippy检查
cargo clippy

# 检查所有目标和特性
cargo clippy --all-targets --all-features

# 将警告视为错误
cargo clippy -- -D warnings

# 修复可以自动修复的问题
cargo clippy --fix
```

## 在IDE中的集成

### VS Code

安装 `rust-analyzer` 扩展后，可以在设置中配置：

```json
{
  "rust-analyzer.rustfmt.rangeFormatting.enable": true,
  "rust-analyzer.check.command": "clippy",
  "editor.formatOnSave": true
}
```

### 其他IDE

大多数现代IDE都支持rust-analyzer，可以实现：
- 保存时自动格式化
- 实时代码检查
- 自动导入整理

## Git钩子集成

可以在 `.git/hooks/pre-commit` 中添加：

```bash
#!/bin/sh
# 在提交前运行格式化检查
cargo fmt --check
if [ $? -ne 0 ]; then
    echo "代码格式不正确，请运行 cargo fmt"
    exit 1
fi

# 运行Clippy检查
cargo clippy -- -D warnings
if [ $? -ne 0 ]; then
    echo "代码检查失败，请修复Clippy警告"
    exit 1
fi
```

## CI/CD集成

在GitHub Actions中的示例：

```yaml
- name: 检查代码格式
  run: cargo fmt --check

- name: 运行Clippy
  run: cargo clippy --all-targets --all-features -- -D warnings
```

## 最佳实践

1. **保持一致性**：整个项目使用相同的格式化配置
2. **自动化**：在IDE中启用保存时格式化
3. **CI检查**：在持续集成中加入格式化和代码检查
4. **渐进式**：对于大型项目，可以逐步引入严格的检查规则
5. **团队约定**：确保团队成员都使用相同的配置

## 高级特性（需要nightly版本）

如果使用nightly版本的Rust，可以启用更多格式化选项：

```toml
# 导入整理
imports_granularity = "Crate"
group_imports = "StdExternalCrate"

# 括号样式
brace_style = "SameLineWhere"
control_brace_style = "AlwaysSameLine"

# 注释格式化
normalize_comments = true
wrap_comments = true
comment_width = 80

# 宏格式化
format_code_in_doc_comments = true
format_macro_matchers = true
```

## 总结

通过合理配置rustfmt和clippy，可以确保代码风格的一致性和质量。这些工具是Rust生态系统的重要组成部分，建议在所有Rust项目中使用。
