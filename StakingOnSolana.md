# Intro
A staker is someone that has SOL and wants to put them to work to collect rewards. They can do this by staking SOL on a validator. A validator, on the other hand, needs SOL staked on it to earn rewards for itself (or its' owner, to be precise). More stake equals more rewards, because more stake means a validator is chosen to write new transactions to the ledger more often. This means that stakers and validators can benefit from each other. You can read more about staking in the docs.

This guide is only intended for people operating in Solana devnet or testnet. Although the procedure will be somewhat similar for mainnet, my experience and knowledge of mainnet operations is too limited to say.

The process of staking involves creating a stake account, transferring SOL to it and delegating it to a validator. SOL placed in a stake account is simply referred to as "stake".

- Staker's SOL on Validator
    To become a Solana validator, one must deposit/lock-up some amount of SOL in a contract.
    This SOL will not be accessible for a specific time period
    The precise duration of the staking lockup period has not been determined. However we can consider three phases of this time for which specific parameters will be necessary
        Warm-up period: which SOL is deposited and inaccessible to the node, however PoH transaction validation has not begun. Most likely on the order of days to weeks

        Validation period: a minimum duration for which the deposited SOL will be inaccessible, at risk of slashing (see slashing rules below) and earning rewards for the validator participation. Likely duration of months to a year.

        Cool-down period: a duration of time following the submission of a 'withdrawal' transaction. During this period validation responsibilities have been removed and the funds continue to be inaccessible. Accumulated rewards should be delivered at the end of this period, along with the return of the initial deposit.
- SOL on Validator - more SOL more reward


**You can stake SOL by moving your tokens into a wallet that supports staking. The wallet provides steps to create a stake account and do the delegation.**


## SOL Staking CLI Commands.

```
# 1. Create Wallet and airdrop
solana config set --url https://api.devnet.solana.com
solana-keygen new --no-passphrase -o main-account.json
solana address -k main-account.json
solana airdrop 1 <MAIN_ACCOUNT_ADDRESS>

# 2. Create second wallet and transfer
solana-keygen new --no-passphrase -o receiver-account.json
solana transfer --from main-account.json <RECIPIENT_ACCOUNT_ADDRESS> 0.5 --allow-unfunded-recipient --fee-payer main-account.json

# 3. Create stake account
solana-keygen new --no-passphrase -o stake-account.json
solana create-stake-account --from main-account.json stake-account.json 0.2 --stake-authority main-account.json --withdraw-authority main-account.json --fee-payer main-account.json
solana stake-account $(solana address -k ./stake-account.json )

# 4. Set stake and withdraw authorities (Optional)
solana stake-authorize <STAKE_ACCOUNT_ADDRESS> --stake-authority <KEYPAIR> --new-stake-authority <PUBKEY> --fee-payer <KEYPAIR>

# 5. Delegate stake
solana validators # get a validator in here
solana delegate-stake --fee-payer main-account.json --stake-authority main-account.json stake-account.json i7NyKBMJCA9bLM2nsGyAGCKHECuR2L5eh4GqFciuwNT
solana stake-account $(solana address -k ./stake-account.json )

# 6. Un-Delegate stake
solana deactivate-stake --stake-authority  main-account.json $(solana address -k ./stake-account.json ) --fee-payer  main-account.json

# 7. Withdraw stake
solana withdraw-stake --withdraw-authority  main-account.json $(solana address -k ./stake-account.json) $(solana address -k ./main-account.json) 0.2 --fee-payer  main-account.json
```

## Staking Related Links
- How to create own validators
https://github.com/agjell/sol-tutorials/blob/master/solana-validator-faq.md

- How to setup devnet validators
https://github.com/agjell/sol-tutorials/blob/master/setting-up-a-solana-devnet-validator.md