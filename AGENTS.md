# AGENTS.md

## Cursor Cloud specific instructions

### Project Overview

This is a Solidity smart contract project — **Uniswap Calibur** — a singleton wallet contract supporting batched transactions, alternative signers, and native ETH `transferFrom` via EIP-7702 delegation. The main contract code lives at `eva-swap/CaliburEntry/`.

### Build Tooling

- **Framework**: Foundry (`forge`, `anvil`, `cast`, `chisel`)
- **Solidity version**: 0.8.29 (Cancun EVM)
- **Config**: `eva-swap/CaliburEntry/foundry.toml` (created from `settings.json` remappings)
- Foundry is installed via `foundryup` at `~/.foundry/bin` — ensure this is on `PATH`.

### Key Commands

All commands run from `eva-swap/CaliburEntry/`:

| Action | Command |
|--------|---------|
| Build | `forge build` |
| Test | `forge test` |
| Lint | `forge lint` |
| Start local node | `anvil --hardfork cancun` |
| Deploy locally | `forge create src/CaliburEntry.sol:CaliburEntry --rpc-url http://127.0.0.1:8545 --private-key <anvil_key> --broadcast` |

### Non-obvious Notes

- **No existing tests**: The repo has no `test/` directory. `forge test` returns successfully but finds no tests.
- **forge-std missing from repo**: The `forge-std` library is listed in remappings but not committed to the repo. It must be cloned into `lib/forge-std` before building.
- **EIP-7702 singleton pattern**: Most CaliburEntry functions revert with `OnlySelf()` (0x3c10b94e) when called directly. This is expected — the contract is designed to be delegated to from an EOA via EIP-7702, not called directly. Functions like `eip712Domain()` and `receive()` (accepting ETH) work without delegation.
- **Duplicate source tree**: `eva-swap/Bscscan/CaliburEntry/` is an identical copy of the source for BSC block explorer verification. Build/develop against the primary `eva-swap/CaliburEntry/` only.
- **viaIR compilation**: The project uses `via_ir = true`, which means compilation is slower than standard but produces more optimized bytecode.
