## 1. solana-sdk 和 solana-client 的作用

### **solana-sdk**
`solana-sdk` 是 Solana 的核心软件开发工具包，提供了与 Solana 区块链交互的基础数据结构和功能：

**主要功能：**
- **基础数据类型**：如 `Pubkey`（公钥）、`Keypair`（密钥对）
- **交易相关**：`Transaction`、`VersionedTransaction`、`Instruction`
- **签名功能**：`Signer`、`NullSigner` 等
- **程序开发**：用于开发 Solana 程序的基础工具
- **序列化/反序列化**：处理区块链数据格式

**在你的项目中的使用：**
```rust
// 从 example/src/main.rs
use solana_sdk::{pubkey, transaction::VersionedTransaction};
use solana_sdk::{pubkey::Pubkey, signature::NullSigner};

// 从 jupiter-swap-api-client/src/swap.rs
use solana_sdk::{
    instruction::{AccountMeta, Instruction},
    pubkey::Pubkey,
};
```

### **solana-client**
`solana-client` 是 Solana 的客户端库，提供了与 Solana RPC 节点通信的功能：

**主要功能：**
- **RPC 客户端**：`RpcClient` 用于与 Solana 节点通信
- **交易发送**：发送和确认交易
- **账户查询**：查询账户信息、余额等
- **区块链状态查询**：获取区块、槽位信息等
- **非阻塞版本**：`nonblocking::rpc_client::RpcClient`

**在你的项目中的使用：**
```rust
// 从 example/src/main.rs
use solana_client::nonblocking::rpc_client::RpcClient;

// 用于发送交易到 Solana 网络
let rpc_client = RpcClient::new("https://api.mainnet-beta.solana.com".into());
let error = rpc_client
    .send_and_confirm_transaction(&signed_versioned_transaction)
    .await
    .unwrap_err();
```