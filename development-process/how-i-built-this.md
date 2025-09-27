# How I Built This

Just documenting how I thought through this multi-sig wallet thing.

## The Problem

I wanted multiple people to control one wallet. Like a shared bank account but nobody can spend alone - you need others to agree.

## Figuring Out the Hard Part

The tricky bit was tracking approvals. I kept thinking "how do I remember who said yes to what?"

I need to connect:
- Transaction number (0, 1, 2...)  
- Which owner
- Did they approve or not

Tried a few ideas but settled on:
```solidity
mapping(uint256 => mapping(address => bool)) isConfirmed;
```

So `isConfirmed[3][alice] = true` means Alice approved transaction 3. Pretty clean.

## What's in a Transaction

Each transaction needs basic stuff:
```solidity
struct Transaction {
    address to;        // where to send
    uint256 value;     // how much ETH
    bytes data;        // any extra instructions
    bool executed;     // did we do this already?
    uint256 numConfirmations;  // vote count
}
```

Store them in an array. Transaction 0, transaction 1, etc.

## Who Can Do What

Only owners should mess with transactions. Need to track who's an owner:
- `address[] owners` - list everyone
- `mapping(address => bool) isOwner` - quick check
- `modifier onlyOwner()` - block non-owners

Also need to set how many approvals are required. Could be 2 out of 3, or 5 out of 7.

## The Workflow

### Someone proposes a transaction
Create new Transaction, set executed=false and numConfirmations=0. Add to array.

### Others vote on it  
Set `isConfirmed[txIndex][voter] = true` and bump up the confirmation count.

### Execute when ready
Check if we have enough votes. If yes, mark executed=true and actually send the money.

### Change your mind
Set your approval back to false and decrease the count.

## Stuff That Could Go Wrong

### Same transaction executed twice
Easy to mess up and send money twice. Fixed with the `executed` flag.

### Someone votes multiple times  
Could fake extra votes. The `isConfirmed` mapping prevents this.

### Trying to approve transaction #999 when there's only 3
Would break things. Added bounds checking.

### Random people interfering
Non-owners shouldn't touch anything. `onlyOwner` modifier handles this.

## Code Choices

Used modifiers instead of copying the same `require` statements everywhere:
```solidity
modifier onlyOwner() {
    require(isOwner[msg.sender], "not owner");
    _;
}
```

When changing transaction data, need `storage` not `memory`:
```solidity
Transaction storage tx = transactions[_txIndex];
tx.numConfirmations += 1;  // actually updates blockchain
```

Added events so external apps know what's happening.

Renamed functions to be clearer:
- `proposeTransaction` instead of `submitTransaction`  
- `approveTransaction` instead of `confirmTransaction`
- Made the execute function name longer to be obvious

## Testing My Logic

3 owners, need 2 votes:
1. Alice proposes sending 1 ETH somewhere
2. Alice approves her own thing (1/2 votes)
3. Bob approves (2/2 votes)  
4. Anyone executes the transaction
5. Charlie tries to execute again - fails because already executed

Seems right.

## The Key Thing

The nested mapping was when it clicked. I was stuck on tracking approvals until I realized I needed two keys - transaction ID and owner address. Like a grid where each cell is yes/no.

Once I had that, the rest was just validation and execution logic.

## Next Time

Would add:
- Way to add/remove owners
- Different vote requirements for different amounts  
- Time delays for big transactions

But for learning Solidity, this covered the important stuff.
