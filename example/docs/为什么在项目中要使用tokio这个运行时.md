## 为什么要使用 Tokio？

**Tokio** 是 Rust 生态系统中最流行的异步运行时（async runtime）。在这个项目中使用 tokio 的原因包括：

### 1. **网络 I/O 操作**
从代码中可以看到，这个程序需要进行多个网络请求：
- 调用 Jupiter API 获取报价 (`jupiter_swap_api_client.quote()`)
- 调用 Jupiter API 进行交换 (`jupiter_swap_api_client.swap()`)
- 与 Solana RPC 节点通信 (`rpc_client.send_and_confirm_transaction()`)

这些网络操作都是 I/O 密集型的，使用异步编程可以避免阻塞线程，提高程序效率。

### 2. **依赖库的要求**
项目使用的库都是基于异步的：
- `reqwest::Client` - HTTP 客户端库，方法都是异步的
- `solana_client::nonblocking::rpc_client::RpcClient` - Solana 的非阻塞 RPC 客户端

### 3. **性能优势**
异步编程允许在等待网络响应时执行其他任务，而不是阻塞整个线程。

## `#[tokio::main]` 语法含义

`#[tokio::main]` 是一个**过程宏（procedural macro）**，它的作用是：

### 1. **宏展开**
```rust
#[tokio::main]
async fn main() {
    // 你的异步代码
}
```

实际上会被展开为类似这样的代码：
```rust
fn main() {
    let rt = tokio::runtime::Runtime::new().unwrap();
    rt.block_on(async {
        // 你的异步代码
    })
}
```

### 2. **具体功能**
- **创建 tokio 运行时**：自动创建一个 tokio 运行时环境
- **启动异步执行器**：提供执行异步任务所需的调度器
- **阻塞等待完成**：`block_on` 会阻塞当前线程直到异步 main 函数完成

### 3. **为什么需要这个宏**
在 Rust 中，`main` 函数必须是同步的，但我们的代码中有很多 `async` 函数调用（如 `.await`）。`#[tokio::main]` 宏解决了这个矛盾：
- 它让我们可以写 `async fn main()`
- 内部自动处理异步运行时的创建和管理

### 4. **配置选项**
在 `Cargo.toml` 中可以看到：
```toml
tokio = { version = "1", features = ["full"] }
```
`features = ["full"]` 启用了 tokio 的所有功能，包括：
- 多线程调度器
- 网络 I/O
- 文件系统 I/O
- 定时器等

## 代码中的异步调用示例

在你的代码中，可以看到多个 `.await` 调用：
```rust
let quote_response = jupiter_swap_api_client.quote(&quote_request).await.unwrap();
let swap_response = jupiter_swap_api_client.swap(...).await.unwrap();
let error = rpc_client.send_and_confirm_transaction(...).await.unwrap_err();
```

这些都需要在异步环境中运行，而 `#[tokio::main]` 就提供了这个环境。

总结来说，`#[tokio::main]` 是使用 tokio 异步运行时的标准方式，它让你可以在 main 函数中直接使用异步代码，非常适合需要进行网络通信的应用程序。