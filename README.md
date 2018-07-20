# ethereum-docker-network

Create and run a private ethereum network in Docker. This uses a development version
of geth that is created locally so that we can test those changes in a private network
environment.

## Getting Started

To get started just clone the repo and got into the project root:

```
$ git clone https://github.com/cpurta/ethereum-docker-network
$ cd ethereum-docker-network
```

This project requires that you have a tagged geth docker image using the `development`
tag. This can be done by cloning `https://github.com/ethereum/go-ethereum` and then
running the following command:

```
$ docker build -t ethereum/client-go:development .
```

Once you have the `development` image tagged you can run your private network by
running the following command:

```
$ docker-compose up
```

### What now?

This will by default make one bootstrap node, a ethereum node, and also run a netstats
container so that you can monitor your network. From here you can monitor your network
or deploy your own smart contracts to the network.

#### Netstats

You can monitor your private network by using `http://127.0.0.1:3000` and you will see
all the metrics about your network.

#### Scaling

By default the `docker-compose.yml` will only start one node that is mining. If you
wish to scale more mining nodes you can do so by using the `--scale` flag when starting
the network.

Example:
`N = number of nodes you want to run`
```
$ docker-compose up --scale eth=N
```

## Deploying and accessing smart contracts

What is the point of having a private network without being able to deploy you own
smart contracts to it and test them out?

### Prerequisites

It is highly recommended that you have `truffle` installed to make deployment and
interacting with your contracts easier. If you do not have `truffle` you can install
it via `npm install -g truffle`.

You will need to have at least 3 terminal windows open to perform this and we will
go through the following steps.

 - Start the network
 - Unlock the coinbase account on the bootstap node
 - Use truffle to migrate and deploy a smart contract

#### Start the network

You must have the private network running and instructions for getting that started
can be found above. Hint: In one terminal run: `docker-compose up`

#### Unlock coinbase account

In another terminal you must access the bootstrap node and this can be done by attaching
to the bootstrap node container:

```
$ docker exec -it bootstrap_node geth attach /root/.ethereum/devchain/geth.ipc
```

You are now attached to the bootstrap node and using the geth javascript console. To
unlock the account you can use the following command in the console:

```
> personal.unlockAccount(web3.eth.coinbase, "", 15000)
```

Now that the account is unlocked we can then go about deploying a contract to the network.

#### Deploy smart contract

To deploy a smart contract to the private network it is recommended that the project
use truffle and has a network that points to `localhost:8545`. If you do not have such
a configuration more info can be found [here](https://truffleframework.com/docs/advanced/configuration).

In your third terminal you should be in a smart contracts repo that uses truffle and
has a network configured to use `localhost:8545`. You can then run:

```
$ truffle migrate --network private-network-rpc
```

If everything worked correctly your smart contract should have been deployed and you
can use the truffle console to interact with your contract.

#### Troubleshooting

There are sometimes a few issues when deploying contracts that can be remedied:

1. Deployments hanging on migration
 - More than likely due to a DAG being built, so you will need to wait a few minutes sometimes
2. Getting a `Error: authentication needed: password or unlock` when deploying
 - Either the coinbase account on the bootstrap node was never unlocked or you need to unlock again

Other Issue?

Feel free to make a **DETAILED** issue explaining what the expected behavior is and
what occurred that was unexpected.

## LICENSE

MIT
