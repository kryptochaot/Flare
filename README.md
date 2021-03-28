# Flare

Flare is a next-generation blockchain which enables smart contracts with multiple non-Turing complete assets that settle on their native chain.

Forked from Flare Network: https://gitlab.com/flarenetwork/flare

## Features

- Federated Byzantine Agreement based Avalanche consensus. Control over the Flare network is proportionally given to the miners that contribute the most to the safety of underlying blockchains on Flare, weighted by market cap.
- State-connector system to observe the state of underlying chains. State-connector proofs can be submitted by anyone, and all nodes independently compare this proof to their own view of an underlying chain before permitting the proof to be finalised onto Flare.

## Documentation & FAQ's

Information about how Flare works at the network-level is available at https://docs.flare.network/en/.

Join the Flare community on [Discord](https://discord.gg/XqNa7Rq) for FAQ's and if you have any other questions or feedback.

## Dependencies

- Hardware per Flare node: 64-bit architecture, 2 GHz or faster CPU, 4 GB RAM, 2 GB hard disk.
- OS: Ubuntu >= 18.04 or Mac OS X >= Catalina.
- Flare validator software: [Go](https://golang.org/doc/install) version 1.14.13
    - Ensure that you set up [`$GOPATH`](https://github.com/golang/go/wiki/SettingGOPATH).
- State-connector software: [NodeJS](https://nodejs.org/en/download/package-manager/) version 10.24.0.
- NodeJS dependency management: [Yarn](https://classic.yarnpkg.com/en/docs/install) version 1.22.10.
- Commandline JSON parser: [jq](https://stedolan.github.io/jq/download/)

Clone Flare:
```
git clone https://gitlab.com/flarenetwork/flare
```

## Deploy a Local Network

Configure and launch a 4-node network

First, set an environment variable listing your preferred XRP Ledger APIs for use with the state connector system. Each successive URL included works as a backup to its preceeding URL in case the preceeding URL fails.

```
export XRP_APIs="https://xrpl.flare.network:443, https://xrplcluster.com"
```

Then, launch the network:

```
./local.sh
```

To restart a previously stopped network without resetting it, use the launch command above with the `--existing` flag.

## State-Connector System: Proving the state of any underlying chain for all smart contracts on Flare

The state connector system is a competitive approach for proving the state of an underlying chain to a smart contract, and it has the following advantages:

1. **Transaction validity references back to an underlying chain's genesis block**: Other approaches like the SPV proof do not check the validity of a transaction.

2. **Safety only depends on an underlying chain's validators**: There is no trusted third-party service that has its own set of economic incentives and risks. Trust is minimized by leveraging the guarantee that safety can only be lost in the state connector if an underlying chain's validators encounter a Byzantine fault.

3. **No cooperation needed from an underlying chain's validators**: Validators from an underlying chain do not need to modify their chain's codebase to permit Flare to interpret their network. An underlying chain's validators do not even need to be aware that Flare exists in order for the state connector system to operate.

4. **Can read the state of any blockchain**: The state connector can operate on any possible Sybil-resistance technique of an underlying chain. For example: proof-of-work, proof-of-stake and even federated byzantine agreement where there is not global agreement on the set of validators in control of a network.

5. **No encoding of the current validators in control of an underlying chain to a smart contract on Flare**: This requirement of other state-relay approaches such as the SPV proof leads to the hazardous scenario where the enforcement of bad behavior in relaying state needs to be conducted by the same set of operators that have performed the bad behavior.

6. **Constant-sized proofs**: both the data availability proof and the payment proof are constant-sized, independent of the number of other payments in the data availability period being considered.

7. **Every Flare validator independently verifies an underlying chain's state**: If your own Flare validator observes the canonical state of an underlying chain, then you will not lose safety against that chain.

In a new terminal window, the following command launches a web3 service that continually competes to prove data availability from the XRP Ledger to the Flare Network. The system submits a constant-sized data availability proof for each range of ledgers on the underlying chain, and the state connector system on Flare rewards the first account to successfully do so for each range of ledgers. This allows one to then prove that a payment exists on an underlying chain to any contract on the Flare Network, such as the F-asset contract.

```
cd client
yarn
./bridge.sh xrp
```

## Verify an Underlying Chain Payment on Flare

Once the first data availability proof has been finalised, you can then submit a payment proof regarding this XRP transaction: https://livenet.xrpl.org/transactions/C242BEA7C3BF291AEE0F56A4915421A1916DEADE6208E6696A4BDE3F7238953F. Run the following command in a separate terminal window:

```
node prove xrp C242BEA7C3BF291AEE0F56A4915421A1916DEADE6208E6696A4BDE3F7238953F
```

## Connect a Node to the Coston Testnet

The Coston testnet has a public RPC endpoint at:

- Chain ID: [16](https://github.com/ethereum-lists/chains/blob/master/_data/chains/eip155-16.json)
- Asset Code: CFLR
- Public RPC Endpoint: https://coston.flare.network/ext/bc/C/rpc

that you can use with MetaMask, Web3 JS (https://web3js.readthedocs.io/en/v1.3.4/), etc right away without running your own Flare node. However, you can also run your own Flare node and peer it with the Coston testnet using the following command:

```
./coston.sh
```

Once launched, your Flare node may take several minutes to bootstrap to the Coston testnet history. Once bootstrapped, you can send Web3 RPC requests to the Coston testnet through your node's RPC endpoint at: `http://127.0.0.1:9650/ext/bc/C/rpc`.

The Coston testnet also has a block scanner at https://flarescan.org and a CFLR faucet at https://faucet.towolabs.com/.


## License: MIT

Copyright 2021 Flare Foundation

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
