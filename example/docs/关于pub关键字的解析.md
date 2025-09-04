## `pub` 关键字的作用和区别

`pub` 是 Rust 中的**可见性修饰符**，用于控制代码的**访问权限**。让我详细解释：

### 1. **没有 `pub` 的情况（私有）**

```rust
const USDC_MINT: Pubkey = pubkey!("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v");
const NATIVE_MINT: Pubkey = pubkey!("So11111111111111111111111111111111111111112");
```

- 这些常量是**私有的**（private）
- 只能在**当前模块**内部访问
- 其他模块无法使用这些常量

### 2. **有 `pub` 的情况（公开）**

```rust
pub const TEST_WALLET: Pubkey = pubkey!("2AQdpHJ2JpcEgPiATUXjQxA8QmafFegfQwSLWSprPicm");
```

- 这个常量是**公开的**（public）
- 可以被**其他模块**访问和使用
- 成为模块的**公共 API** 的一部分

### 3. **实际使用场景的区别**

#### **私有常量** (没有 `pub`)
```rust
// 在 example/src/main.rs 中
const USDC_MINT: Pubkey = pubkey!("...");  // 只在 main.rs 内部使用
const NATIVE_MINT: Pubkey = pubkey!("..."); // 只在 main.rs 内部使用
```

这些常量：
- 只是为了在 `main.rs` 文件内部使用
- 不需要被其他文件或模块访问
- 属于**内部实现细节**

#### **公开常量** (有 `pub`)
```rust
pub const TEST_WALLET: Pubkey = pubkey!("...");  // 可以被外部访问
```

这个常量：
- 可能需要被其他模块使用
- 是**公共 API** 的一部分
- 其他人可以通过 `example::TEST_WALLET` 访问

### 4. **在库文件中的应用**

从您的项目中可以看到：

```rust
// 在 lib.rs 中
pub mod quote;           // 公开 quote 模块
pub mod swap;            // 公开 swap 模块
pub struct JupiterSwapApiClient { ... }  // 公开结构体
```

这意味着：
- 其他项目可以使用 `jupiter_swap_api_client::quote`
- 其他项目可以创建 `JupiterSwapApiClient` 实例
- 这些是库的**公共接口**

### 5. **访问级别总结**

| 修饰符 | 可见性 | 访问范围 |
|--------|--------|----------|
| (无) | 私有 | 仅当前模块 |
| `pub` | 公开 | 整个 crate 和外部 crate |
| `pub(crate)` | crate 内公开 | 仅当前 crate |
| `pub(super)` | 父模块公开 | 父模块及其子模块 |

### 6. **设计原则**

- **默认私有**：Rust 默认所有内容都是私有的
- **最小暴露**：只公开必要的 API
- **封装性**：隐藏内部实现细节

在您的例子中：
- `USDC_MINT` 和 `NATIVE_MINT` 是内部使用的常量，不需要外部访问
- `TEST_WALLET` 可能需要在测试或其他地方使用，所以标记为 `pub`

这种设计让代码更安全、更易维护，因为你可以自由修改私有部分而不影响外部使用者。