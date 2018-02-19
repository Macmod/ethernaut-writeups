# Force

> Some contracts will simply not take your money ¯\_(ツ)_/¯
> The goal of this level is to make the balance of the contract greater than zero.

> Things that might help:
> * Fallback methods
> * Sometimes the best way to attack a contract is with another contract.
> * See the Help page above, section "Beyond the console"

```solidity
pragma solidity ^0.4.18;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

Since this contract has no payable fallback or any other method and the challenge requires you to pay it somehow, it must be possible to send funds to a contract by some other means.

Reading the docs, we find this quote at [Security Considerations](https://solidity.readthedocs.io/en/develop/security-considerations.html):

> Neither contracts nor “external accounts” are currently able to prevent that someone sends them Ether. Contracts can react on and reject a regular transfer, but there are ways to move Ether without creating a message call. One way is to simply “mine to” the contract address and the second way is using selfdestruct(x).

Since we haven't read about mining so far, `selfdestruct` seems like a better alternative. Let's [read about it](https://solidity.readthedocs.io/en/develop/introduction-to-smart-contracts.html?highlight=selfdestruct):

> The only possibility that code is removed from the blockchain is when a contract at that address performs the selfdestruct operation. The remaining Ether stored at that address is sent to a designated target and then the storage and code is removed from the state.

This means we have to create a contract on the blockchain, send some funds to it, and then make it call `selfdestruct` with the instance address.

Let's do it:
```solidity
pragma solidity ^0.4.18;

contract ForceWriteup {

  function ForceWriteup(address _address) payable public {
    selfdestruct(_address);
  }
}
```

1. Paste the code in [Remix](https://remix.ethereum.org/);
2. Go to the `Run` tab;
3. Make sure you have `Injected Web3` as `Environment` and `Metamask` is on the Ropsten testnet;
4. Set `Value` to whatever nonzero amount you want;
5. Set `Address` to your `Instance Address`;
6. Click `Create`.

Then check whether it went through:

```javascript
(await getBalance(instance)).toNumber()
```

And finally submit the instance back to the level.
