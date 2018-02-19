# King

> The contract below represents a very simple game: whoever sends it an amount of ether that is larger than the current prize becomes the new king. On such an event, the overthrown king gets payed the new prize, making a bit of ether in the process! As ponzi as it gets xD
>
> Such a fun game. Your goal is to break it.
>
> When you submit the instance back to the level, the level is going to reclaim kingship. You will beat the level if you can avoid such a self proclamation.

```solidity
pragma solidity ^0.4.18;

import 'zeppelin-solidity/contracts/ownership/Ownable.sol';

contract King is Ownable {

  address public king;
  uint public prize;

  function King() public payable {
    king = msg.sender;
    prize = msg.value;
  }

  function() external payable {
    require(msg.value >= prize || msg.sender == owner);
    king.transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }
}
```

The contract shows a classic ponzi scheme: you invest some money rewarding the previous guy and wait for the next one to be dumb enough to pay back your investment. It is also slightly backdoored: the owner can reset the pyramid whenever he wants, which the level does when we submit the instance. We are asked to prevent the level from doing that.

To do so, let's examine the fallback function. The only nontrivial action it does is a `transfer` call. According to the [docs](http://solidity.readthedocs.io/en/develop/miscellaneous.html?highlight=transfer):

> <address>.transfer(uint256 amount): send given amount of Wei to Address, throws on failure

Interesting. Since the transfer is not the last operation in this contract, the king and prize won't be changed if the transfer fails, since it's going to throw an exception. There is also no error handling to make sure the previous king gets rewarded eventually if that happens. So how do we get this transfer to throw an exception?

From the docs we get that:

> A require-style exception is generated in the following situations:
>
> [...]
> 
> * If you call a function via a message call but it does not finish properly (i.e. it runs out of gas, has no matching function, or throws an exception itself), except when a low level operation call, send, delegatecall or callcode is used. The low level operations never throw exceptions but indicate failures by returning false.
>
> [...]
>
> * If .transfer() fails

Well, `king.transfer(msg.value)` triggers a message call to the `king` contract, and we can force an exception in a message call just setting a low gas limit on the initial transaction responsible for that message call.

Let's first check the initial prize:

```javascript
fromWei((await contract.prize()).toNumber())
```

The prize is 1 ether, so to become king first we need to begin a transaction for 1 ether:

```javascript
contract.sendTransaction({value: toWei(1.01)})
```

Check with `await contract.king() == player`.

Now we submit the instance once setting a low gas limit, like 30000, and then submit it again to have the level process the fact that we bypassed the last submission.
