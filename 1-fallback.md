# Fallback

> Look carefully at the contract's code below.
> You will beat this level if
> 1. you claim ownership of the contract
> 2. you reduce its balance to 0

> Things that might help
> * How to send ether when interacting with an ABI
> * How to send ether outside of the ABI
> * Converting to and from wei/ether units -see help() command-
> * Fallback methods

```solidity
pragma solidity ^0.4.18;

import 'zeppelin-solidity/contracts/ownership/Ownable.sol';

contract Fallback is Ownable {

  mapping(address => uint) public contributions;

  function Fallback() {
    contributions[msg.sender] = 1000 * (1 ether);
  }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public constant returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() onlyOwner {
    owner.transfer(this.balance);
  }

  function() payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
```

In Solidity contracts, you can often not only call functions, but also call them paying some ether along the process, so the contract can decide what to do with it. Since contracts also have addresses on the blockchain, you can also pay them directly without calling a specific method. If you do the latter, the contract's fallback function is going to be called. There must be either a single fallback function per contract or no fallback function at all (in this case the contract will reject direct transactions sent to it). The fallback function is always the one without a name.

Note that, in this case, the function named Fallback is not the actual fallback function, but the contract's constructor (since it's named after the contract's name). The constructor is called a single time by the contract's creator upon creation.

To claim ownership of this contract, we have to set `owner` to our wallet address. This can be done one of the following ways:

1. Contributing more ether than the owner;
2. Contributing anything and calling the fallback function.

According to the constructor, the owner of the contract donates 1000 ether. The owner may even not donate 1000 ether upon creation of the contract, but it nevertheless stores his contribution as "1000 ether". Since the contribute function only allows the contributor to donate less than 0.001 ether per call, we'd need to call it at least 1000001 times to donate more than the owner's 1000 ether. This isn't practical at all, although possible in theory, ruling out option 1.

Option 2 seems easier, so let's do it. Request a level instance, wait until it's processed and run:
```javascript
contract.contribute({value: toWei(0.0005)})
```

Note we're using toWei because we need to express 0.0005 as wei (an integer unit for ether).
Now call getContribution() to check whether it went through:
```javascript
fromWei((await contract.getContribution()).toNumber())
```

Send some ether to trigger the fallback function and become the owner:
```javascript
contract.sendTransaction({value: toWei(0.01)})
```

Verify you've become the owner:
```javascript
await contract.owner() == player
```

Now steal the funds by running:
```javascript
contract.withdraw()
```
You couldn't call this function before because it's tagged with the `onlyOwner` modifier.

Submit the instance back to the level and you shall see the first "level completed" message after a minute or two.
