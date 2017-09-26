# Currency Contract

EOS comes with example contracts that can be uploaded and run for testing purposes. We demonstrate how to upload and interact with the sample contract "currency". 

* Let the EOS node be running:
```bash
cd ${EOS_PROGRAMS}/eosd/ && ./eosd
```

* Set up a wallet and importing account key. As the `config.ini` has an entry reading `plugin = eos::wallet_api_plugin`, EOS wallet is running as a part of `eosd` process. Every contract requires an associated account, so create a wallet. </br> 
Start a new bash (Ctr + Shift + \`), and input there:

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc wallet create 
# response is
# Creating wallet: default
# Save password to use in the future to unlock this wallet.
# Without password imported keys will not be retrievable.
# "PW5Jt75WX3P82TjCLtgKaR1js9yxHxmAwXoc8c2jZBQKeMvaqNJX1"
```

* For the purpose of this walkthrough, import the private key of the `inita` account, a test account included within genesis.json, so that you're able to issue API commands under authority of an existing account. The private key referenced below is found within your `config.ini` and is provided to you for testing purposes. 

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc wallet import \
5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
# response is
# imported private key for: 
# EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
```

* Create accounts for sample "currency" contract. Generate some public/private key pairs that will be later assigned as `owner_key` and `active_key`.

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc create key # owner_key
# response is
# Private key: 5KKLojCoiy9TFDuM6oZ4Qeymkkwh8E2GV8rYMtg3qbkBLQKNQ6m
# Public key:  EOS6EPtUUWh8X9vaaVj8GrLPuN1G3NmyXbT9PzyaEqz2eWXRZKjDM

cd ${EOS_PROGRAMS}/eosc/ && ./eosc create key # active_key
# response is
# Private key: 5KXrX8VkVqjvN3zxMma1RqDuSk6ojZd9tpkjf6gLnvTWu11mWyE
# Public key:  EOS6MEWsw4HWK5B7ThU8k9S1yAeRG2rRxNMLZZDczyNAxEuiLX1Li
```

* Run the `create` command where `inita` is the account authorizing the creation of the `currency` account and arguments are the public owner_key and the public active_key. </br>
```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc create account inita currency \
EOS6JWgw2m6CYuqNH9dBjxknJr4j7A4qEyX42vwbAstMxCsrkjg91 \
EOS5s1s1cQhdv6BLhfdj4XVy96pgYyQCpNJZJkJitmpf4QPjKQaTP 
# response is
```  
```json
{
  "transaction_id": "3c5c7bb041024a12afe43a57aab30845f433818a09c7ad47aed4ccf9a5aa0097",
  "processed": {
    "refBlockNum": 161,
    "refBlockPrefix": 3590202471,
    "expiration": "2017-09-25T13:10:51",
    "scope": [
      "eos",
      "inita"
    ],
    "signatures": [
      "20271a8d67fd31427338121cda887b2e58ac1c45c2d8ae73a49664f23d76a8556214558e1b351004612edde1be4fd990f9ee147f00e0cb0498501fa76476ee630c"
    ],
    "messages": [{
        "code": "eos",
```


* Go ahead and check that the account was successfully created

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc get account currency
# response is
```
```json
{
  "name": "currency",
  "eos_balance": 0,
  "staked_balance": 1,
  "unstaking_balance": 0,
  "last_unstaking_time": "2106-02-07T06:28:15"
}
```

* Now import the private active_key generated previously in the wallet:

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc wallet import \
5KXrX8VkVqjvN3zxMma1RqDuSk6ojZd9tpkjf6gLnvTWu11mWyE
```

* Before uploading a contract to blockchain, verify that there is no current contract:

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc get code currency 
# response is
# code hash: 0000000000000000000000000000000000000000000000000000000000000000
```

* With an account for a contract created, upload a sample contract:

```bash
cd ${EOS_PROGRAMS}/eosc/ && ./eosc set contract currency \
../../contracts/currency/currency.wast \
../../contracts/currency/currency.abi
```

As a response you should get a json with a `transaction_id` field. Your contract was successfully uploaded!

You can also verify that the code has been set with the following command

```bash
./eosc get code currency
```

It will return something like
```bash
code hash: 9b9db1a7940503a88535517049e64467a6e8f4e9e03af15e9968ec89dd794975
```

Next verify the currency contract has the proper initial balance:

```bash
./eosc get table currency currency account
{
  "rows": [{
     "account": "account",
     "balance": 1000000000
     }
  ],
  "more": false
}
```

<a name="pushamessage"></a>
### Transfering funds with the sample "currency" contract 

Anyone can send any message to any contract at any time, but the contracts may reject messages which are not given necessary permission. Messages are not
sent "from" anyone, they are sent "with permission of" one or more accounts and permission levels. The following commands shows a "transfer" message being
sent to the "currency" contact.  

The content of the message is `'{"from":"currency","to":"inita","amount":50}'`. In this case we are asking the currency contract to transfer funds from itself to
someone else.  This requires the permission of the currency contract.


```bash
./eosc push message currency transfer '{"from":"currency","to":"inita","amount":50}' --scope currency,inita --permission currency@active
```

Below is a generalization that shows the `currency` account is only referenced once, to specify which contact to deliver the `transfer` message to.

```bash
./eosc push message currency transfer '{"from":"${usera}","to":"${userb}","amount":50}' --scope ${usera},${userb} --permission ${usera}@active
```

We specify the `--scope ...` argument to give the currency contract read/write permission to those users so it can modify their balances.  In a future release scope
will be determined automatically.

As a confirmation of a successfully submitted transaction you will receive json output that includes a `transaction_id` field.

<a name="readingcontract"></a>
### Reading sample "currency" contract balance

So now check the state of both of the accounts involved in the previous transaction. 

```bash
./eosc get table inita currency account
{
  "rows": [{
      "account": "account",
      "balance": 50 
       }
    ],
  "more": false
}
./eosc get table currency currency account
{
  "rows": [{
      "account": "account",
      "balance": 999999950
    }
  ],
  "more": false
}
```

As expected, the receiving account **inita** now has a balance of **50** tokens, and the sending account now has **50** less tokens than its initial supply. 
