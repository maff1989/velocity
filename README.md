# Velocity, for cryptocurrencies
spend cryptocurrency inputs in chains to simulate cash velocity

## DISCLAIMER
**I TAKE NO RESPONSIBILITY FOR ANY PAIN/SUFFERING/MISFORTUNE CAUSED BY THE MISUSE OF MY TOOL**

**THIS TOOL DOES NOT WORK WITH ETHEREUM; ONLY BITCOIN DERIVATIVES SUCH AS BITCOIN CASH, LITECOIN, DASH, ZCASH, DIGIBYTE, SMARTCASH, ZCOIN, ETC. (i.e. anything forked from a recent Bitcoin Core codebase). ENVDEAVORS TO RUN THIS SCRIPT AS-IS ON ETH/ETC/ELLA/ETC. NETWORKS WILL LIKELY END IN DISASTER**

## Elevator Pitch
Velocity is a PHP script written as a tool to test the resiliency of cryptocurrency networks under heavy transaction loads. It connects via RPC to the daemon of choice to gather and spend each unspent input to a single, new address, thereby creating the first *link* in a transaction *chain*. Each chain is extended with new links as the program continues operation. Inputs are never merged, or spent together; each chain remains independent from one another as links are added to them. Chains are discarded as new blocks are found; each new block starts a new round of chains.

## Setup
1. As of v0.1, Velocity requires an RPC connection to a *fully-validating daemon* (with a `host:port`) to function. This may change in the future as the project matures. Refer to the developer's website of any particular coin if you wish to run their fully-validating daemon with Velocity.
2. Once you finish setting up the daemon, be sure that you allow the daemon to fully synchronize with the blockchain. Transactions crafted by Velocity will be rejected by the daemon if it knows that it is not fully sync'd with the network. Some daemons take days to sync depending on the hardware you are using; please be patient.
3. Also be sure that the RPC configuration you created for your daemon is reflected in your copy of the `velocity` script. Please only edit the `$config` options near the top.
3. The script was written and tested in `PHP 7.0.30` on the `Ubuntu 16.04.5` operating system. *Theoretically*, the PHP script will work on Windows. I do not plan on providing support for the Windows platform, so if any functions do not work on Windows, feel free to make a pull request. :)
4. I recommend Ubuntu as the OS base. You will need a copy of the `php7.0` and `php7.0-cli` packages; install them via `apt install php7.0 php7.0-cli`. If you don't use Ubuntu or another Debian derivative with the `apt` package management system, please refer to your ditribution's package manager documentation on how to install the required php7.0 packages.

## Running velocity
Assuming all above is already completed, and you are at a terminal in the directory in which you've downloaded and modified the `velocity` script per your configuration, you are ready run Velocity.

In your terminal, run the following command: `./velocity`

* If you receive the error `./velocity: Permission denied` you will need to `chmod +x ./velocity` first before running `./velocity`

Velocity will immediately begin polling your coin daemon via RPC to check for unspent inputs and beging creating transaction chains.

### What you will see
* If for any reason the daemon connection fails, you will see an `[error]` stating so and the reason why. Please verify your Velocity and crypto daemon config and try again.
* If the connection is successful, the following is an example of what you will see:

```	
  
  Velocity, for cryptocurrencies (v0.1)
	running since 08/21/2018 22:54:19

[main] round 1: { block:10100  chains:0  links:0  links-confirmed:0 }
[main] mempool: { tx-count:0  total-size:0 bytes  ram:0 bytes }
```

* `round 1` begins the first set of `chains` by creating `links` that spend transaction inputs. As `block`s are found on the network, Velocity moves to the next round, and the number of total links (out of all chains) created for the round are then added to the `links-confirmed` stat (this stat, effectively, is the total number of transactions that Velocity has sent to, and had accepted by, the network).
* Chains are emptied after each round because inputs are "fresh" to start brand new chains having received their first confirmation; as an example, some mempool restrictions, e.g. the [25-descendant limit][1], are reset for inputs once they are confirmed.
* `mempool` conveniently displays important stats gauged when stressing a cryptocurrency network: number of unconfirmed transactions in the mempool, aggregate size of unconfirmed transactions in bytes, and total RAM usage by the mempool
* If any chain runs into error during operation (i.e. receives an error from the RPC daemon) then the chain will be disabled and its tip will not be extended until the next round begins.
* When a new block is found, the `(!)` indicator will be displayed to the right of the block number for a short period of time (currently 5% of the avg block time; for BTC/BCH this is 30s)
* If the average block time for the coin passes and a block hasn't been found, the `(?)` indicator will be displayed to the right of the block number

## Stopping Velocity
If you wish to shut down Velocity, all you need to do is close the terminal window in which it is running, or send a SIGINT signal (i.e. `CTRL+C`) to the process.

[1]: https://jasonc.me/blog/chained-0-conf-transactions-memo
