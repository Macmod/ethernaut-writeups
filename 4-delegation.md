# Delegation
> The goal of this level is for you claim ownership of the instance you are given.

> Things that might help
> * Look into Solidity's documentation on the delegatecall low level function, how it works, how it can be used to delegate operations to on-chain libraries, and what implications it has on execution scope.
> * Fallback methods
> * Method ids

```solidity
pragma solidity ^0.4.18;

contract Delegate {

  address public owner;

  function Delegate(address _owner) {
    owner = _owner;
  }

  function pwn() {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  function Delegation(address _delegateAddress) {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  function() {
    if(delegate.delegatecall(msg.data)) {
      this;
    }
  }
}
```

Well, this contract has a bunch of functions we're not familiar with. Let's [read the docs](https://solidity.readthedocs.io/en/develop/search.html?q=delegatecall&check_keywords=yes&area=default):

> There exists a special variant of a message call, named delegatecall which is identical to a message call apart from the fact that the code at the target address is executed in the context of the calling contract and msg.sender and msg.value do not change their values.
> This means that a contract can dynamically load code from a different address at runtime. Storage, current address and balance still refer to the calling contract, only the code is taken from the called address.

I'd say `dynamically load code from a different address at runtime` doesn't seem like a good idea. This means that using `delegatecall` allows the called contract to hack the calling contract in any way it wants.

The `Delegation` contract has a fallback which delegates a call to any function we want from the `Delegate` contract. A delegatecall to `pwn` will effectively change the owner of the `Delegation` contract, instead of the owner of `Delegate`. Let's try it then:

```javascript
contract.sendTransaction({data: 'pwn'})
```

This doesn't seem to work, though. Apparently we can't just use a string with the method name in a delegatecall.
The `Things that might help` tells us about `method IDs`. It seems Solidity contracts use IDs instead of the actual names internally.

According to the [ABI](http://solidity.readthedocs.io/en/develop/abi-spec.html):
> 0xcdcd77c0: the Method ID. This is derived as the first 4 bytes of the Keccak hash of the ASCII form of the signature baz(uint32,bool).

It makes sense for the method ID to have a fixed size, since this naturally limits the size of the methods' identification in compiled code while also allowing arbitrarily long method names, as long as their IDs don't collide with others from the same contract (Solidity compilers check this). There are 256^4 possibilities for this number, which is unique for methods in the same contract, so you'd have to be very unlucky or [this guy](https://www.reddit.com/r/ethdev/comments/6n6t9w/function_hash_collision_potential_misuse/) to have a problem like this.

Let's try using the ID for the `pwn()` signature then:

```javascript
contract.sendTransaction({data: web3.sha3('pwn()').slice(0, 10)})
```

Now check with `await contract.owner() == player` as usual and submit the instance.
