# 引言

DEX, Decentralized Exchange

- 订单簿模式：dydx, ZigZag
- 自动做市商（AMM, Auto Market Maker）：UniSwap
- 固定利率市场：Contango

Lend: AAVE、Maker、Compound

## Yield Famming

流动性提供者（LP, Liquidity Provider）将资产存入 AMM 的流动性池（Liquidity Pool）或者抵押到借贷平台，都会获得一个存款凭证（LP Token），以此赚取收益（交易手续费、平台代币等激励），这个过程通常叫做流动性挖矿（Yield Famming）

- 锁仓量 TVL, Total Value Locked 流动性总和，`TVL = 抵押或存入资产数量 * 资产价格`
- 年化收益率
    - APR, Annual Percentage Rate 不考虑复利，`APR = 日利率 * 投资天数`
    - APY, Annual Percentage Yield 复利，$APY = (1+日利率)^{投资天数-1}$