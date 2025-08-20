
## `pubkey!` 宏的作用

`pubkey!` 是 Solana SDK 提供的一个**宏**（macro），用于在编译时将字符串形式的公钥转换为 `Pubkey` 类型。

### 语法解释

```rust
const USDC_MINT: Pubkey = pubkey!("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v");
```

这行代码的含义是：
- `pubkey!()` 是一个宏调用，感叹号 `!` 表示这是宏而不是函数
- 括号内的字符串 `"EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"` 是 Base58 编码的公钥地址
- 这个宏会在**编译时**将字符串转换为 `Pubkey` 类型

### 为什么使用宏而不是函数？

1. **编译时验证**：宏会在编译时检查公钥字符串的格式是否正确，如果格式错误会导致编译失败
2. **性能优化**：转换在编译时完成，运行时不需要额外的解析开销
3. **类型安全**：确保创建的是有效的 `Pubkey` 对象

### 等价的运行时写法

如果不使用宏，您需要这样写：

```rust
use solana_sdk::pubkey::Pubkey;
use std::str::FromStr;

// 运行时解析，可能失败
let usdc_mint = Pubkey::from_str("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v").unwrap();
```

### 在代码中的应用

从您的代码可以看到，这些公钥代表：
- `USDC_MINT`: USDC 代币的 mint 地址
- `NATIVE_MINT`: SOL 原生代币的 mint 地址  
- `TEST_WALLET`: 测试钱包地址

这种方式让代码更清晰，避免了运行时的字符串解析错误。