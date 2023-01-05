## Re-Entrancy attack:
---
Re-Entrancy is one of the oldest security vulnerabilities that was discovered in smart contracts. It is the exact vulnerability that caused the infamous 'DAO Hack' of 2016. Over 3.6 million ETH was stolen in the hack, which today is worth billions of dollars. 🤯

``` diff
- The failure was having a negative impact on the Ethereum network,
- and Vitalik Buterin proposed a software fork where the attacker would never be able to transfer out his ETH.
- Some people agreed, some did not. This was a highly controversial event, and one which still is full of controversy.

+ At the end, it led to Ethereum being forked into two - Ethereum Classic, and the Ethereum we know today. 
+ Ethereum Classic's blockchain is the exact same as Ethereum up until the fork, 
+ but then proceeded as if the hack did happen and the attacker still controls the stolen funds. 

- Today's Ethereum implemented the blacklist and it's as if that attack never happened.🤔 
```

#### Re-Entrancy is the vulnerability in which if Contract A calls a function in Contract B, Contract B can then call back into Contract A while Contract A is still processing.

![img](./solidity-reentrancy-attack.jpg)

+ This can lead to some serious vulnerabilities in Smart contracts, often creating the possibility of draining funds from a contract.

---
Let's understand how this works with the example shown in the above diagram. Let's say Contract A has some function - call it f() that does 3 things:

- Checks the balance of ETH deposited into Contract A by Contract B
- Sends the ETH back to Contract B
- Updates the balance of Contract B to 0
  
Since the balance gets updated after the ETH has been sent, Contract B can do some tricky stuff here. If Contract B was to create a fallback() or receive() function in it's contract, which would execute when it received ETH, it could call f() in Contract A again.

Since Contract A hasn't yet updated the balance of Contract B to be 0 at that point, it would send ETH to Contract B again - and herein lies the exploit, and Contract B could keep doing this until Contract A was completely out of ETH.



---
### Prevention:
There are two things that we can do:
- Either, you could recognize that this function was vulnerable to re-entrancy, and make sure you update the user's balance in the withdraw function before you actually send them the ETH, so if they try to callback into withdraw it will fail.

- Alternatively, OpenZeppelin has a ReentrancyGuard library that provides a modifier named nonReentrant which blocks re-entrancy in functions you apply it to. It basically works like the following:
``` js
modifier nonReentrant() {
    require(!locked, "No re-entrancy");
    locked = true;
    _;
    locked = false;
}
```
---
[Reentrancy Guard Library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol) Click here to see the code.