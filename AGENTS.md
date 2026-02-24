# AGENTS.md

## Cursor Cloud specific instructions

### Codebase overview

This repository contains two components:
1. **Root level** (`README.md`, `LICENSE`, `Tr`): Conceptual manifesto for the Fi-- (Fibonacci Programming Language) project. No executable code.
2. **`eva-swap/CaliburEntry/`**: Uniswap's Calibur smart contract wallet — a Solidity project (ERC-4337, EIP-7702 compatible). This is the only buildable/testable component. The `eva-swap/Bscscan/CaliburEntry/` directory is a duplicate for BSC chain verification.

### Foundry (Solidity toolchain)

Foundry (`forge`, `anvil`, `cast`, `chisel`) is the development framework. It is installed via `foundryup` and lives at `~/.foundry/bin/`. Ensure `source ~/.bashrc` is run before invoking Foundry tools in shell sessions.

### Building

```sh
cd eva-swap/CaliburEntry && forge build
```

The `foundry.toml` was created from `settings.json` and configures: Solc 0.8.29, Cancun EVM, `via_ir = true`, optimizer with 10000 runs, and remappings for vendored libraries in `lib/`.

### Testing

No test files exist in the repository. `forge test` completes with "No tests found in project!". Future tests should go in a `test/` directory under `eva-swap/CaliburEntry/`.

### Linting

`forge build` outputs lint warnings/notes inline (unsafe-typecast, asm-keccak256, naming conventions). There is no separate linting config. `forge lint` can be run but may crash (known instability in Foundry 1.5.x).

### Local deployment

Start a local EVM node with `anvil` and deploy with:
```sh
anvil --host 127.0.0.1 --port 8545 &
cd eva-swap/CaliburEntry && forge create --rpc-url http://127.0.0.1:8545 \
  --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80 \
  --broadcast src/CaliburEntry.sol:CaliburEntry
```

### Gotchas

- CaliburEntry is an EIP-7702 singleton wallet. Most view functions (like `name()`, `version()`) revert with `FnSelectorNotRecognized` when called directly on the deployed singleton. Use `eip712Domain()` and `ENTRY_POINT()` to verify deployment.
- The `settings.json` file is a reference for Solc settings, not consumed by Foundry. `foundry.toml` is the authoritative build config.
- Libraries are vendored in `lib/` (not git submodules). The `forge-std` library is not present since there are no tests.
- The `metadata` section in `foundry.toml` produces a warning (`Found unknown metadata config`); this is cosmetic and does not affect compilation.
