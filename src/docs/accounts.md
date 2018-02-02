# Account
Niminq supports three types of accounts: basic account, vesting contract, and hashed time-locked contract. 

Among account share the following fields:

| Element | Data type | size [bytes] | Description
|---------|-----------|--------------|-------
| type    | Uint8     | 1            | 0..2: basic, [vesting](#vesting-contract), [HTLC](#hashed-time-locked-contract-HTLC)
| balance | Uint64    | 8            | in Satoshi

# Basic account
An account with balance, the type field is set to `0`.

# Pruned account
A prunded account is composed of an account of any type with an address:

| Element | Data type    | size [bytes] |
|---------|--------------|--------------|
| address | Address      | 20           | 
| account | Account      | variable     | Can be a basic account, vesting contract, or HTLC

This type is used in the body of a block to communicate the accounts to be pruned with this block.

# Vesting contract
A vesting contract allows to spend money in scheduled way. 
An initial amount can be spend immediately followed by a scheduled distribution of the remaining amout 
until the funds are used up.

| Element              | Data type | size [bytes] | Description
|----------------------|-----------|--------------| -----------
| type                 | Uint8     | 1            | Set to `1`
| balance              | Uint64    | 8            | The total, must be >= `vesting total amount`.
| vesting start        | Uint32    | 4            | The block height to start with. The amount will be made available at `vesting start` + `vesting step blocks`
| vesting step blocks  | Uint32    | 4            | After which number of blocks the next `vesting step amount` should be made available
| vesting step amount  | Uint64    | 8            | The amount made to be made available at each step
| vesting total amount | Uint64    | 8            | The total amount of all steps added up. If <= `vesting total amount`, the difference will be made available immediately.

# Hashed time-locked contract HTLC
Used for conditional payments and for cross-chain atomic swaps. All HTLC are time limited.

| Element              | Data type | size [bytes] | Description
|----------------------|-----------|--------------| -----------
| type                 | Uint8     | 1            | Set to `2`
| balance              | Uint64    | 8            | 
| sender               | Address   | 20           | 
| recipient            | Address   | 20           | 
| hash algorithm       | Uint8     | 1            | 1..3, for Blake2b, Argon2s, SHA256
| hash root            | Hash      | 32           | 
| hash count           | Uint8     | 1            | 
| timeout              | Uint32    | 4            | 
| total amount         | Uint64    | 8            | 
