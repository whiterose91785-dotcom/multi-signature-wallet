# Multi-Signature Wallet

My first Solidity project - a multi-signature wallet that requires multiple owners to approve transactions before they can be executed.

## What is this?

This is a smart contract that acts like a shared bank account. Instead of one person controlling all the funds, multiple people (owners) need to agree before any money can be sent. Think of it like needing multiple keys to open a safe.

## Why build this?

I wanted to learn Solidity by building something actually useful. Multi-sig wallets are pretty important in crypto - they're used by DAOs, companies, and anyone who wants extra security for their funds. Plus, the logic is complex enough to be interesting but not so crazy that I'd get lost.

## How it works

- Set up the wallet with multiple owners and decide how many need to approve each transaction
- Any owner can propose a transaction (send ETH somewhere, call another contract, etc.)
- Other owners vote to approve or reject it
- Once enough owners approve, anyone can execute the transaction
- Owners can also change their mind and withdraw their approval before execution

## Features

- **Multiple owners**: Add as many owners as you want
- **Flexible approval threshold**: Decide how many approvals are needed (2 of 3, 5 of 7, etc.)
- **Propose transactions**: Any owner can suggest sending money or calling contracts
- **Approve/reject**: Owners vote on pending transactions
- **Change your mind**: Withdraw approval if you change your mind
- **Execute when ready**: Run approved transactions
- **Track everything**: All actions are logged with events

## Contract Functions

The main functions (with more descriptive names than typical):

- `proposeTransaction()` - Create a new transaction proposal
- `approveTransaction()` - Vote yes on a pending transaction  
- `executeApprovedTransaction()` - Run a transaction that has enough votes
- `withdrawApproval()` - Remove your approval from a transaction
- `getAllOwnerAddresses()` - See who the owners are
- `getTotalTransactionCount()` - Check how many transactions exist
- `getTransactionDetails()` - Get info about a specific transaction

## What I learned

- How to use mappings and structs effectively
- Event logging for transparency
- Access control with modifiers
- Managing complex state (who approved what)
- The importance of input validation
- How multi-sig security actually works

## Usage

Deploy the contract with:
- Array of owner addresses
- Number of required confirmations

Then owners can start proposing and approving transactions.

## Security Notes

This is my first project, so **please don't use it with real money** without getting it audited first. I tried to follow best practices but I'm still learning.

## Next Steps

Things I might add:
- Owner management (add/remove owners)
- Different approval thresholds for different transaction types  
- Time locks for extra security
- Better event indexing
- Comprehensive tests

---

*This was a fun first project! The multi-sig pattern taught me a lot about Solidity state management and security considerations.*
