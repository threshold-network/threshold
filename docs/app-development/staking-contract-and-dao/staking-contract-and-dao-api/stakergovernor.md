# StakerGovernor

## StakerGovernor

### VETO\_POWER

```solidity
bytes32 VETO_POWER
```

### manager

```solidity
address manager
```

### constructor

```solidity
constructor(contract IVotesHistory _staking, contract TimelockController _timelock, contract TokenholderGovernor tokenholderGovernor, address vetoer) public
```

### cancel

```solidity
function cancel(address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) external returns (uint256)
```

### propose

```solidity
function propose(address[] targets, uint256[] values, bytes[] calldatas, string description) public returns (uint256)
```

### quorum

```solidity
function quorum(uint256 blockNumber) public view returns (uint256)
```

### proposalThreshold

```solidity
function proposalThreshold() public view returns (uint256)
```

### getVotes

```solidity
function getVotes(address account, uint256 blockNumber) public view returns (uint256)
```

### state

```solidity
function state(uint256 proposalId) public view returns (enum IGovernor.ProposalState)
```

### supportsInterface

```solidity
function supportsInterface(bytes4 interfaceId) public view returns (bool)
```

### \_execute

```solidity
function _execute(uint256 proposalId, address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) internal
```

### \_cancel

```solidity
function _cancel(address[] targets, uint256[] values, bytes[] calldatas, bytes32 descriptionHash) internal returns (uint256)
```

### \_executor

```solidity
function _executor() internal view returns (address)
```

Returns the address of the entity that acts as governance for this contract.

By default, Governor assumes this is either the Governor contract itself, or a timelock if there's one configured. We override this here for the StakerGovernor contract so it's the Tokenholder DAO's Timelock, which we obtain at constructor time.
