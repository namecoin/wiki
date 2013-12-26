# Bitcoin vs. Namecoin

## What is the relationship of this project to Bitcoin?
The code is based on Bitcoin. The block chain is separate, starting from a different genesis block and therefore creating a separate currency - Namecoin. This approach was recommended during the BitDNS discussion on the forum.
## What are the differences with Bitcoin?
* There are additional RPC commands which allow you to send special transactions which contain data.
* Those operations are name_new, name_firstupdate and name_update.
* Cost of name_firstupdate is sent to nowhere, coins are lost.
* Those special transactions contain data: a pair of name/value.
* Those special transactions have an expire time of 36,000 blocks.
* The "d/" prefix is used to register a domain name, without the .bit TLD:
`{
    "name" : "d/opennic",
    "value" : "what you want",
    "expires_in" : 10227
},`

## What are the similarities with Bitcoin?
* 21 millions namecoins maximum, minus the lost coins.
* 50 coins are generated each block, each 210000 blocks (around 4 years), divide the reward by two.

# General

## How does Namecoin work?
The Namecoin software is used to register names and store associated values in the blockchain, a shared database distributed by p2p in a secure way. The software can then be used to query the database and retrieve data.
## How much does it cost to register a domain (a.k.a. a name)?
The cost includes a network fee and a transaction fee. The fees are denominated in namecoins (NMC). Initially, the network fee is 50 NMC. The network fee decreases 2x every 2 months. Starting from block 24,000, the decline in network fees will speed up by a factor of 4. You can check the current cost here.
## How do I obtain namecoins?
You can get them in the usual Bitcoin way, by mining or by obtaining them from someone else, trading.
## Who gets the network fee?
The network fees are destroyed by the transaction. Nobody gets them.
## Who gets the transaction fee?
The miners do, just like in Bitcoin. Initially a reasonable transaction fee is either 0.00 or 0.01 NMC. You can choose this based on how fast you want your transaction to be processed.
## How long are names good for?
You have to execute an update on a name every 36,000 blocks (approx. ~250 days) or it expires. There are no network fee for updates.
## Do I have to pay renewal fees?
No, only Bitcoin-like transaction fees, currently 0.00 or 0.01 NMC.
## Can Namecoin be used to register other data types?
Namecoin can be used to register any other data types. They will all follow the same rules (expire time, data size limit, etc).

# Design

## Why is there a separate name_new step?
This is to prevent others from stealing your new name by registering it quickly themselves when they see your transaction. The name is not broadcasted, only an encrypted version of it. There is a mandatory 12 block wait that gives you enough time to broadcast your name with name_firstupdate, reducing the chance that someone will get in a name_firstupdate ahead of you.
## Why is there a network fee?
The network fee is initially high, but will be negligible after a couple of years. It is used to slow down the initial registration rate so that plenty of desirable names are left for late adopters.
## How are names represented?
Names and values are attached to special coins with a value of 0.01 NMC. Updates are performed by creating a transaction with the name's previous coin as input.
## What if I spend that special coin by mistake?
The code prevents those coins from being used for normal payments.

# Mining

## Can I use existing Bitcoin miners?
Yes.
## I have to choose whether to mine Namecoin or Bitcoin?
No, you can mine both now. See Merged_Mining.