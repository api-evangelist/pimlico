---
name: Pay gas in an ERC-20 token via the ERC-20 paymaster
description: >-
  Let a user pay ERC-4337 gas fees in a supported ERC-20 token (e.g. a
  stablecoin) instead of the native gas token, using Pimlico's ERC-20 paymaster.
api: https://docs.pimlico.io/references/paymaster/erc20-paymaster
endpoint: https://api.pimlico.io/v2/{chain}/rpc?apikey=API_KEY
operations:
  - pimlico_getErc20PaymasterQuotes
  - pm_getPaymasterData
  - eth_sendUserOperation
  - eth_getUserOperationReceipt
---

# Pay gas in an ERC-20 token

Use Pimlico's ERC-20 paymaster so users settle gas in a supported token. Calls
are JSON-RPC 2.0 POSTs to `https://api.pimlico.io/v2/{chain}/rpc?apikey=API_KEY`.

## Steps

1. **Check supported tokens** — confirm the token is enabled for the chain
   (dashboard: `https://dashboard.pimlico.io/billing/plan`; reference:
   `https://docs.pimlico.io/references/paymaster/erc20-paymaster/supported-tokens`).
2. **Get token quotes** — call `pimlico_getErc20PaymasterQuotes` to price the
   gas in the chosen ERC-20 token and get the paymaster context.
3. **Ensure allowance** — make sure the smart account has approved the paymaster
   to spend the required token amount.
4. **Get paymaster data** — call `pm_getPaymasterData` with the ERC-20 context to
   obtain signed paymaster fields.
5. **Sign and submit** — sign the user operation and call
   `eth_sendUserOperation`; capture the returned `userOpHash`.
6. **Confirm** — poll `eth_getUserOperationReceipt` until the receipt returns.

## Notes

- The `nonce` on the user operation provides replay protection (see
  `conventions/pimlico-conventions.yml`).
- Insufficient token balance/allowance or an unsupported token surface as
  JSON-RPC errors (see `errors/pimlico-error-codes.yml`).
- Enterprise plans support 300+ tokens (see `plans/pimlico-plans.yml`).
