# SVPChain demo — TypeScript

Minimal TypeScript example. **HTTP / JSON-RPC calls go through [axios](https://axios-http.com/).** [viem](https://viem.sh) is used only for local transaction signing — no HTTP transport from viem.

Queries the current block height via `eth_blockNumber`; if `PRIVATE_KEY` is set, signs an EIP-1559 transfer of 0.01 SVP locally and broadcasts it via `eth_sendRawTransaction`.

## Prereqs

- Node.js 18+
- A funded testnet address (claim from https://www.svpchain.org/faucet)

## Run

```sh
npm install

# block height only
npm start

# block height + transfer
export PRIVATE_KEY=0xyour_testnet_private_key
export TO_ADDRESS=0xrecipient_address      # optional; defaults to 0x...dEaD
npm start
```

## How it works

| Step | Method | Layer |
| --- | --- | --- |
| Read current head | `eth_blockNumber` | axios POST → RPC |
| Read nonce | `eth_getTransactionCount` | axios POST → RPC |
| Read base fee | `eth_getBlockByNumber("latest", false)` | axios POST → RPC |
| Suggest tip | `eth_maxPriorityFeePerGas` | axios POST → RPC |
| Build & sign tx | EIP-1559 (`type: "eip1559"`) | viem `account.signTransaction` (offline) |
| Broadcast | `eth_sendRawTransaction` | axios POST → RPC |
| Wait | `eth_getTransactionReceipt` (1s poll) | axios POST → RPC |

## Network

| Field | Value |
| --- | --- |
| RPC | `https://svp-dataseed1-testnet.svpchain.org` |
| EVM chain ID | `2517` |
| Currency | `SVP` (18 decimals) |

Switch to mainnet by changing `RPC_URL` and `CHAIN_ID` in `src/main.ts` — see the SVPChain docs Networks page for canonical values.
