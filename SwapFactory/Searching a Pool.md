# Searching a Pool

## Introduction
This guide will cover how to get a pool canister for any token pair in ICPSwap.

In this example we will use 
**getPool** to get the pool information of the pair ICP - SNS1. The inputs are the *token0*, the *token1* and the *fee*.

For this guide, the following canister ids are used:
|Name|Principal|
|:-|:-:|
|SwapFactory|4mmnk-kiaaa-aaaag-qbllq-cai|

## Command
When we want to search a pool, The order in which token0 and token1 are passed in does not affect the query results.

```
dfx canister --network=ic call 4mmnk-kiaaa-aaaag-qbllq-cai getPool '(record {token0 = record {address = "ryjl3-tyaaa-aaaaa-aaaba-cai"; standard = "ICP";}; token1 = record {address = "zfcdd-tqaaa-aaaaq-aaaga-cai"; standard = "ICRC1";}; fee = 3000:nat; })'
```
or
```
dfx canister --network=ic call 4mmnk-kiaaa-aaaag-qbllq-cai getPool '(record {token0 = record {address = "zfcdd-tqaaa-aaaaq-aaaga-cai"; standard = "ICRC1";}; token1 = record {address = "ryjl3-tyaaa-aaaaa-aaaba-cai"; standard = "ICP";}; fee = 3000:nat; })'
```

## Detail

did
```
type Token = record { address : text; standard : text };

type GetPoolArgs = record { fee : nat; token0 : Token; token1 : Token };

type PoolData = record {
  fee : nat;
  key : text;
  tickSpacing : int;
  token0 : Token;
  token1 : Token;
  canisterId : principal;
};

type Error = variant {
  CommonError;
  InternalError : text;
  UnsupportedToken : text;
  InsufficientFunds;
};

type Result_3 = variant { ok : PoolData; err : Error };

type SwapFactory = service {
  getPool : (GetPoolArgs) -> (Result_3) query;
};

service : SwapFactory
```

In the input parameters:
+ *token0/token1* is the token information which in one of the pair in the SwapPool.
  + *address* is the canister principal id of particular token.
  + *standard* is the token standard text, it can only be DIP20, EXT, ICRC1, ICRC2, ICRC3 or ICP.
+ *fee* is the fee that is taken from every swap that is executed on the pool in 1 per million - if the fee value of a pool is 3000, 3000/ 1000000 (or 0.3%) of the trade amount is taken as a fee. This fee goes to the liquidity providers of the Pool. And the fee value can only be 500, 3000 or 10000. <font color="#dd0000">Currently, only 3000 is in use.</font>