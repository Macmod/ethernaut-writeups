# Token

> The goal of this level is for you to hack the basic token contract below.

> You are given 20 tokens to start with and you will beat the level if you somehow manage to get your hands on any additional tokens. Preferably a very large amount of tokens.
> Things that might help:
> * What is an odometer?

```solidity
pragma solidity ^0.4.18;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  function Token(uint _initialSupply) {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public constant returns (uint balance) {
    return balances[_owner];
  }
}
```

I did not get the odometer reference.

This seems like a straightforward token contract with no apparent logic flaws, so we must return to the basics. `totalSupply` is an `uint`, which means we can possibly overflow it. `Preferably a large amount of tokens` seems like a hint: if we subtract a larger uint from a smaller one, we'll cause an overflow and get a large uint. There's one such subtraction there at `require(balances[msg.sender] - _value >= 0);`, so our task is to call `transfer` with some `_value` where `_value > balances[msg.sender]`. The result will be a large uint, bypassing the requirement.

First let's check our balance:
```javascript
(await contract.balanceOf(player)).toNumber()
```

We really have `20` tokens, indicating the owner of the contract called `contract.transfer(player, 20)`.
Let's transfer `21` tokens to someone then, like the level itself. At `balances[msg.sender] -= _value` our balance is also going to overflow, since we only have 20 tokens, giving us an insane amount of tokens.

```javascript
contract.transfer(level, 21)
```

We check the balance again to be sure it went through and submit our instance.
