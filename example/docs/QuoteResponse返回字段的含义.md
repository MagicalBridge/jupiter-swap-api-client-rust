## 打印出来的字段：

```json
{
  "input_mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
  "in_amount": 1000000,
  "output_mint": "So11111111111111111111111111111111111111112",
  "out_amount": 5623786,
  "other_amount_threshold": 5595668,
  "swap_mode": "ExactIn",
  "slippage_bps": 50,
  "computed_auto_slippage": null,
  "uses_quote_minimizing_slippage": null,
  "platform_fee": null,
  "price_impact_pct": 0,
  "route_plan": [
    {
      "swap_info": {
          "amm_key": "3NeUgARDmFgnKtkJLqUcEUNCfknFCcGsFfMJCtx6bAgx",
          "label": "Raydium CLMM",
          "input_mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
          "output_mint": "Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB",
          "in_amount": 1000000,
          "out_amount": 999828,
          "fee_amount": 84,
          "fee_mint": "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v"
      },
      "percent": 100
    },
    {
      "swap_info": {
          "amm_key": "ExcBWu8fGPdJiaF1b1z3iEef38sjQJks8xvj6M85pPY6",
          "label": "Raydium CLMM",
          "input_mint": "Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB",
          "output_mint": "So11111111111111111111111111111111111111112",
          "in_amount": 999828,
          "out_amount": 5623786,
          "fee_amount": 168,
          "fee_mint": "Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB"
      },
      "percent": 100
    }
  ],
  "context_slot": 361333157,
  "time_taken": 0.000950047
}
```

## 基本交易信息

### 交易对信息
- **`input_mint`**: `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` - 输入代币的 mint 地址（USDC）
- **`in_amount`**: `1000000` - 输入数量（1 USDC，考虑了 6 位小数）
- **`output_mint`**: `So11111111111111111111111111111111111111112` - 输出代币的 mint 地址（SOL）  
- **`out_amount`**: `5623786` - 预期输出数量（约 0.005623786 SOL，考虑了 9 位小数）

### 滑点和阈值
- **`other_amount_threshold`**: `5595668` - 考虑滑点后的最小接受数量
- **`swap_mode`**: `ExactIn` - 交易模式（精确输入，即指定输入数量）
- **`slippage_bps`**: `50` - 滑点容忍度（50 个基点 = 0.5%）

### 费用和影响
- **`platform_fee`**: `None` - 平台费用（此次交易无平台费用）
- **`price_impact_pct`**: `0` - 价格影响百分比（0% 表示对市场价格影响很小）

## 路由计划 (`route_plan`)

您的交易需要经过 2 个步骤（多跳交易）：

### 第一步：USDC → USDT

```
SwapInfo {
  amm_key: 3NeUgARDmFgnKtkJLqUcEUNCfknFCcGsFfMJCtx6bAgx,  // AMM 池子地址
  label: "Raydium CLMM",                                  // 使用 Raydium CLMM DEX
  input_mint: EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v, // USDC
  output_mint: Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB, // USDT
  in_amount: 1000000,   // 输入 1 USDC
  out_amount: 999828,   // 输出约 0.999828 USDT  
  fee_amount: 84,       // 手续费 84（以 USDC 计价）
  fee_mint: EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v, // 手续费代币（USDC）
},
percent: 100,  // 100% 的资金走这条路径
```

### 第二步：USDT → SOL  
```
SwapInfo {
  amm_key: ExcBWu8fGPdJiaF1b1z3iEef38sjQJks8xvj6M85pPY6,  // 另一个 AMM 池子地址
  label: "Raydium CLMM",                                    // 同样使用 Raydium CLMM
  input_mint: Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB, // USDT（上一步的输出）
  output_mint: So11111111111111111111111111111111111111112, // SOL
  in_amount: 999828,    // 输入约 0.999828 USDT
  out_amount: 5623786,  // 最终输出约 0.005623786 SOL
  fee_amount: 168,      // 手续费 168（以 USDT 计价）
  fee_mint: Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB, // 手续费代币（USDT）
},
percent: 100,  // 100% 的资金走这条路径
```

## 系统信息
- **`context_slot`**: `361333157` - 区块链上下文槽位（用于确保报价时效性）
- **`time_taken`**: `0.000950047` - API 处理时间（约 0.95 毫秒）

## 总结

这是一个 **USDC → SOL** 的多跳交易：
1. **路径**: USDC → USDT → SOL（通过 USDT 作为中间代币）
2. **总费用**: 84 + 168 = 252 个单位的手续费
3. **汇率**: 1 USDC ≈ 0.005623786 SOL
4. **滑点保护**: 最少能获得 0.005595668 SOL

这种多跳设计是因为直接的 USDC/SOL 交易对可能流动性不足或价格不优，通过 USDT 中转能获得更好的价格。