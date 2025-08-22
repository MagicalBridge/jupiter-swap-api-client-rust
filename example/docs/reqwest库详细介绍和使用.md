## 📖 库简介

`reqwest` 是Rust生态系统中最受欢迎的HTTP客户端库，提供了简洁且强大的API来处理HTTP请求。它是基于`hyper`构建的高级HTTP客户端，专注于易用性和人体工程学。

### 核心特性

- ✅ **异步优先**：基于`tokio`的异步HTTP客户端
- ✅ **JSON支持**：内置JSON序列化/反序列化
- ✅ **HTTPS支持**：默认支持TLS/SSL
- ✅ **Cookie管理**：自动处理Cookie
- ✅ **代理支持**：HTTP/HTTPS代理
- ✅ **重定向处理**：自动跟踪重定向
- ✅ **流式处理**：支持大文件上传/下载
- ✅ **中间件支持**：可扩展的请求/响应处理

## 🎯 主要使用场景

### 1. **RESTful API客户端**
- 与Web API进行交互
- 微服务之间的通信
- 第三方服务集成

### 2. **Web爬虫和数据采集**
- 网页内容抓取
- 数据挖掘和分析
- 监控和健康检查

### 3. **文件上传/下载**
- 大文件传输
- 图片/视频处理
- 备份和同步

### 4. **测试和调试**
- API测试
- 负载测试
- 集成测试

## 💡 基础使用示例

### 示例1：简单的GET请求

```rust
use reqwest;
use serde::Deserialize;

#[derive(Deserialize)]
struct ApiResponse {
    message: String,
    status: String,
}

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    // 发送GET请求
    let response = reqwest::get("https://httpbin.org/json")
        .await?
        .json::<ApiResponse>()
        .await?;
    
    println!("响应: {}", response.message);
    Ok(())
}
```

### 示例2：POST请求发送JSON数据

```rust
use reqwest::Client;
use serde::{Deserialize, Serialize};
use serde_json::json;

#[derive(Serialize)]
struct LoginRequest {
    username: String,
    password: String,
}

#[derive(Deserialize)]
struct LoginResponse {
    token: String,
    user_id: u64,
}

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();
    
    let login_data = LoginRequest {
        username: "user123".to_string(),
        password: "password123".to_string(),
    };
    
    let response = client
        .post("https://api.example.com/login")
        .json(&login_data)  // 自动设置Content-Type: application/json
        .send()
        .await?;
    
    if response.status().is_success() {
        let login_result: LoginResponse = response.json().await?;
        println!("登录成功，Token: {}", login_result.token);
    } else {
        println!("登录失败，状态码: {}", response.status());
    }
    
    Ok(())
}
```

### 示例3：带查询参数的GET请求

```rust
use reqwest::Client;
use serde::Deserialize;
use std::collections::HashMap;

#[derive(Deserialize)]
struct SearchResult {
    results: Vec<String>,
    total: u32,
}

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();
    
    // 方法1：使用query方法
    let response1 = client
        .get("https://api.example.com/search")
        .query(&[("q", "rust"), ("limit", "10")])
        .send()
        .await?;
    
    // 方法2：使用HashMap
    let mut params = HashMap::new();
    params.insert("q", "reqwest");
    params.insert("sort", "date");
    
    let response2 = client
        .get("https://api.example.com/search")
        .query(&params)
        .send()
        .await?;
    
    // 方法3：直接在URL中包含参数
    let response3 = client
        .get("https://api.example.com/search?q=http&limit=5")
        .send()
        .await?;
    
    Ok(())
}
```

### 示例4：错误处理和状态码检查

```rust
use reqwest::{Client, StatusCode};
use serde::Deserialize;

#[derive(Deserialize)]
struct ErrorResponse {
    error: String,
    code: u32,
}

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();
    
    let response = client
        .get("https://api.example.com/data")
        .send()
        .await?;
    
    match response.status() {
        StatusCode::OK => {
            let data: serde_json::Value = response.json().await?;
            println!("成功获取数据: {:#?}", data);
        }
        StatusCode::NOT_FOUND => {
            println!("资源未找到");
        }
        StatusCode::UNAUTHORIZED => {
            println!("未授权访问");
        }
        status => {
            let error_body = response.text().await?;
            println!("请求失败，状态码: {}, 错误信息: {}", status, error_body);
        }
    }
    
    Ok(())
}
```

### 示例5：自定义Headers和认证

```rust
use reqwest::{Client, header};

#[tokio::main]
async fn main() -> Result<(), reqwest::Error> {
    let client = Client::new();
    
    let response = client
        .get("https://api.example.com/protected")
        .header(header::AUTHORIZATION, "Bearer your-token-here")
        .header(header::USER_AGENT, "MyApp/1.0")
        .header("X-API-Key", "your-api-key")
        .send()
        .await?;
    
    println!("状态码: {}", response.status());
    println!("响应体: {}", response.text().await?);
    
    Ok(())
}
```

## 🚀 在当前Jupiter项目中的使用

在你的Jupiter Swap API客户端项目中，`reqwest`被用作核心的HTTP客户端来与Jupiter API进行通信。

### 配置信息

```toml
# jupiter-swap-api-client/Cargo.toml
reqwest = { version = "0.12", features = ["json"] }
```

- **版本**: 0.12（最新稳定版）
- **特性**: `["json"]` - 启用JSON序列化/反序列化支持

### 具体使用场景

#### 1. **导入和类型定义**

```rust
// lib.rs 第4行
use reqwest::{Client, Response};

// 错误处理中使用reqwest类型
#[derive(Debug, Error)]
pub enum ClientError {
    #[error("Request failed with status {status}: {body}")]
    RequestFailed {
        status: reqwest::StatusCode,  // 使用reqwest的状态码类型
        body: String,
    },
    #[error("Failed to deserialize response: {0}")]
    DeserializationError(#[from] reqwest::Error),  // 使用reqwest的错误类型
}
```

#### 2. **GET请求 - 获取价格报价**

```rust
// lib.rs 第55-66行
pub async fn quote(&self, quote_request: &QuoteRequest) -> Result<QuoteResponse, ClientError> {
    let url = format!("{}/quote", self.base_path);
    let extra_args = quote_request.quote_args.clone();
    let internal_quote_request = InternalQuoteRequest::from(quote_request.clone());
    
    let response = Client::new()           // 创建HTTP客户端
        .get(url)                         // 发送GET请求
        .query(&internal_quote_request)   // 添加查询参数
        .query(&extra_args)               // 添加额外参数
        .send()                           // 发送请求
        .await?;                          // 等待响应
        
    check_status_code_and_deserialize(response).await
}
```

**功能说明**:
- 向Jupiter API的`/quote`端点发送GET请求
- 使用`.query()`方法添加查询参数
- 自动序列化Rust结构体为URL查询参数

#### 3. **POST请求 - 执行交换**

```rust
// lib.rs 第68-80行
pub async fn swap(
    &self,
    swap_request: &SwapRequest,
    extra_args: Option<HashMap<String, String>>,
) -> Result<SwapResponse, ClientError> {
    let response = Client::new()
        .post(format!("{}/swap", self.base_path))  // 发送POST请求
        .query(&extra_args)                        // URL查询参数
        .json(swap_request)                        // JSON请求体
        .send()                                    // 发送请求
        .await?;
        
    check_status_code_and_deserialize(response).await
}
```

**功能说明**:
- 向Jupiter API的`/swap`端点发送POST请求
- 使用`.json()`方法自动将Rust结构体序列化为JSON
- 自动设置`Content-Type: application/json`头

#### 4. **POST请求 - 获取交换指令**

```rust
// lib.rs 第82-94行
pub async fn swap_instructions(
    &self,
    swap_request: &SwapRequest,
) -> Result<SwapInstructionsResponse, ClientError> {
    let response = Client::new()
        .post(format!("{}/swap-instructions", self.base_path))
        .json(swap_request)
        .send()
        .await?;
        
    check_status_code_and_deserialize::<SwapInstructionsResponseInternal>(response)
        .await
        .map(Into::into)
}
```

#### 5. **响应处理和错误检查**

```rust
// lib.rs 第31-48行
async fn check_is_success(response: Response) -> Result<Response, ClientError> {
    if !response.status().is_success() {
        let status = response.status();
        let body = response.text().await.unwrap_or_default();  // 获取错误响应体
        return Err(ClientError::RequestFailed { status, body });
    }
    Ok(response)
}

async fn check_status_code_and_deserialize<T: DeserializeOwned>(
    response: Response,
) -> Result<T, ClientError> {
    let response = check_is_success(response).await?;
    response
        .json::<T>()  // 自动反序列化JSON为Rust类型
        .await
        .map_err(ClientError::DeserializationError)
}
```

### 项目中reqwest的优势

1. **类型安全**: 与serde结合，提供类型安全的JSON处理
2. **异步支持**: 完美配合tokio异步运行时
3. **错误处理**: 提供详细的错误信息，便于调试
4. **简洁API**: 链式调用风格，代码简洁易读
5. **性能优化**: 支持连接池和HTTP/2

### 实际使用流程

```rust
// example/src/main.rs 中的使用示例
let jupiter_swap_api_client = JupiterSwapApiClient::new(api_base_url);

// 1. 获取价格报价
let quote_response = jupiter_swap_api_client.quote(&quote_request).await.unwrap();

// 2. 执行交换
let swap_response = jupiter_swap_api_client
    .swap(&SwapRequest {
        user_public_key: TEST_WALLET,
        quote_response: quote_response.clone(),
        config: TransactionConfig::default(),
    }, None)
    .await
    .unwrap();

// 3. 获取交换指令
let swap_instructions = jupiter_swap_api_client
    .swap_instructions(&SwapRequest {
        user_public_key: TEST_WALLET,
        quote_response,
        config: TransactionConfig::default(),
    })
    .await
    .unwrap();
```

## 📋 总结

`reqwest`在Jupiter项目中扮演着关键角色：

1. **核心通信工具**: 负责与Jupiter API的所有HTTP通信
2. **数据序列化**: 自动处理Rust结构体与JSON之间的转换
3. **错误处理**: 提供详细的HTTP错误信息
4. **异步支持**: 配合tokio提供高性能的异步网络操作
5. **类型安全**: 确保API调用的类型安全性

通过reqwest，项目能够以类型安全、高性能的方式与Jupiter DEX API进行交互，实现代币交换、价格查询等核心功能。
