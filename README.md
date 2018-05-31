# Ethernaut Writeups

This repository is a collection of writeups for [Ethernaut](https://ethernaut.zeppelin.solutions/) challenges.

[Ethernaut](https://ethernaut.zeppelin.solutions/) is a small wargame consisting of many challenges regarding smart contract security, specifically focused on the Ethereum blockchain. While some of these challenges are mostly teasers to improve the player's knowledge of smart contracts, some are based on very real security issues which caused lots of money to be stolen.

It's highly recommended to try the challenges if you're just now learning about smart contracts and intend to get a deeper understanding of potential problems with their development.

The game runs on the Ropsten testnet (so you don't need actual money to play) and you interact with it through Web3js and Metamask.

[Metamask](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn) is a browser-based Ethereum wallet. [Web3js](https://github.com/ethereum/web3.js/) is a Javascript API for the Ethereum blockchain, allowing the player to interact with the blockchain via the browser console.

Further instructions on how to play can be found [here](https://ethernaut.zeppelin.solutions/help). I'm not aware of whether the Zeppelin team plans on keeping the game online indefinitely, but decided to write these writeups anyway, mainly because I'm bored and want to share my solutions and thoughts with the community. **Update**: the game can also be [played locally](https://github.com/OpenZeppelin/ethernaut), since it's open source.

Feel free to ask me for help if you need. My telegram handle is [@marzanol](tg://resolve?domain=marzanol). Also be aware I'm in no way an expert in this field and cannot provide any valuable insight into real life projects other than what can be learned from this game itself.

# Index
0. [Hello Ethernaut](0-hello.md)
1. [Fallback](1-fallback.md)
2. [Fallout](2-fallout.md)
3. [Token](3-token.md)
4. [Delegation](4-delegation.md)
5. [Force](5-force.md)
6. [King](6-king.md)
7. [Reentrancy](7-reentrancy.md)
8. [Elevator](8-elevator.md)
9. [Telephone](9-telephone.md)
10. [Vault](10-vault.md)
11. [CoinFlip](11-coinflip.md)
12. [Privacy](12-privacy.md) (todo)
13. [Gatekeeper One](13-gatekeeper-1.md) (todo)
14. [Gatekeeper Two](14-gatekeeper-2.md) (todo)
15. [Naught Coin](15-naughtcoin.md) (todo)


PS: New challenges were added by the Zeppelin team and the order of the challenges was changed, so the order here doesn't match theirs anymore.

# Resources
* [Solidity Documentation](https://solidity.readthedocs.io/en/develop/):
The official docs is very well written and should be with you at all times. It's a great resource to those looking to learn about smart contracts for the first time, if you have any previous programming skills.
* [Web3 Javascript API](https://github.com/ethereum/wiki/wiki/JavaScript-API):
A must-read reference on how to programatically interact with contracts through javascript.
* [Solidity Cheat Sheet and Best Practices](https://github.com/manojpramesh/solidity-cheatsheet): Small cheat sheet for the lazy ones.
* [EthList: The Crowdsourced Ethereum Reading List](https://github.com/Scanate/EthList): Further references with many useful links and videos for complete beginners.
* [Pet-shop-box](https://github.com/truffle-box/pet-shop-box): Truffle tutorial that teaches you how to build a complete smart contracts application.
* [Learning Solidity](https://www.youtube.com/watch?v=v_hU0jPtLto&list=PL16WqdAj66SCOdL6XIFbke-XQg2GW_Avg): Great Youtube playlist with Solidity tutorials for beginners.
