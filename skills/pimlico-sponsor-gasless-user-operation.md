---
name: Sponsor a gasless user operation with the verifying paymaster
description: >-
  Build, sponsor, submit, and confirm an ERC-4337 user operation so the end
  user pays no gas, using Pimlico's bundler and verifying paymaster.
api: https://docs.pimlico.io/references/paymaster
endpoint: https://api.pimlico.io/v2/{chain}/rpc?apikey=API_KEY
operations:
  - pimlico_getUserOperationGasPrice
  - pm_getPaymasterStubData
  - pm_getPaymasterData
  - eth_sendUserOperation
  - eth_getUserOperationReceipt
---

# Sponsor a gasless user operation

Use the Pimlico bundler + verifying paymaster to sponsor gas so your user
transacts without holding native tokens. All calls are JSON-RPC 2.0 POSTs to the
per-chain endpoint `https://api.pimlico.io/v2/{chain}/rpc?apikey=API_KEY` (see
`authentication/pimlico-authentication.yml`). The SDK path is `permissionless.js`
(see `packages/`).

## Steps

1. **Get gas price** — call `pimlico_getUserOperationGasPrice` to obtain
   `slow`/`standard`/`fast` `maxFeePerGas` and `maxPriorityFeePerGas`.
2. **Build the user operation** — assemble the unsigned user operation (sender,
   nonce, callData) for your smart account. The `nonce` provides ERC-4337 replay
   protection (see `conventions/pimlico-conventions.yml`; there is no HTTP
   idempotency-key header).
3. **Get stub paymaster data** — call `pm_getPaymasterStubData` to fill
   paymaster fields for gas estimation (ERC-7677).
4. **Estimate + finalize sponsorship** — call `pm_getPaymasterData` to obtain the
   signed paymaster data that commits the verifying paymaster to sponsor the op.
5. **Sign and submit** — sign the user operation and call
   `eth_sendUserOperation`, which returns the `userOpHash`.
6. **Confirm** — poll `eth_getUserOperationReceipt` with the `userOpHash` until
   the receipt is returned.

## Error handling

- Sponsorship failures return a JSON-RPC error such as
  `Insufficient Pimlico balance for sponsorship, please top up` — top up at
  `https://dashboard.pimlico.io/billing/plan` (see `errors/pimlico-error-codes.yml`).
- Test for free on a testnet chain segment (e.g. `sepolia`) before mainnet (see
  `sandbox/pimlico-sandbox.yml`).
