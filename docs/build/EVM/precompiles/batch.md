# Batch Precompile

The batch precompiled contract enables developers to consolidate several EVM calls into a single action. Previously, if users need to engage with multiple contracts, they would need to confirm multiple transactions in their wallet. For instance, this could involve approving a token's access for a smart contract and then performing a transfer or claiming staking rewards on DappsStaking for every era.Through the batch precompile feature, developers can enhance the user experience by condensing these transactions into a batch, thereby reducing the number of confirmations needed to just one. This approach also holds the potential to lower gas fees, as batching avoids incurring multiple base gas fees.

The precompile directly interacts with Substrate's EVM pallet. The caller of the batch function will have their address serve as the `msg.sender` for all subtransactions. However, unlike delegate calls, the target contract will still impact its own storage. Essentially, this process mirrors the effect of a user signing multiple transactions, but with the requirement for only a single confirmation.

| Precompile | Address |
| -------- | -------- |
| `Batch` | 0x0000000000000000000000000000000000005006 |

## The Batch Solidity Interface

Batch.sol is a Solidity interface that allows developers to interact with the precompile's three methods.

The interface includes the following functions:

* **batchSome(address[] to, uint256[] value, bytes[] callData, uint64[] gasLimit)** — performs multiple calls, where the same index of each array combine into the information required for a single subcall. If a subcall reverts, following subcalls will still be attempted

* **batchSomeUntilFailure(address[] to, uint256[] value, bytes[] callData, uint64[] gasLimit)** — performs multiple calls, where the same index of each array combine into the information required for a single subcall. If a subcall reverts, no following subcalls will be executed

* **batchAll(address[] to, uint256[] value, bytes[] callData, uint64[] gasLimit)** — performs multiple calls atomically, where the same index of each array combine into the information required for a single subcall. If a subcall reverts, all subcalls will revert

### Each of these functions have the following parameters:

* **address[] to** - an array of addresses to direct subtransactions to, where each entry is a subtransaction

* **uint256[] value** - an array of native currency values to send in the subtransactions, where the index corresponds to the subtransaction of the same index in the to array. If this array is shorter than the to array, all the following subtransactions will default to a value of 0

* **bytes[] callData** - an array of call data to include in the subtransactions, where the index corresponds to the subtransaction of the same index in the to array. If this array is shorter than the to array, all of the following subtransactions will include no call data

* **uint64[] gasLimit** - an array of gas limits in the subtransactions, where the index corresponds to the subtransaction of the same index in the to array. Values of 0 are interpreted as unlimited and will have all remaining gas of the batch transaction forwarded. If this array is shorter than the to array, all of the following subtransactions will have all remaining gas forwarded

For more information checkout [batch](https://github.com/AstarNetwork/Astar/blob/master/precompiles/batch/Batch.sol) on [astar](https://github.com/AstarNetwork/Astar)