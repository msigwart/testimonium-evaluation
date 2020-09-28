# ETH Relay Evaluation
This project contains the evaluation setup for [ETH Relay](https://github.com/pantos-io/ethrelay).
The evaluation is performed using block headers of the main Ethereum network which have been collected between December 2019 - January 2020.

## Prerequisites
* Node.js
* Truffle
* Docker
* Ethereum node (e.g., [Ganache](https://www.trufflesuite.com/ganache) or [Parity Dev Chain](https://wiki.parity.io/Private-development-chain))

Clone the repository and then run the following commands in the project root:
```shell script
$ npm install
$ npm link
```  

## Get Started

### Setup the database
The block headers used for the evaluation need to be stored in a PostgreSQL database (an SQL dump can be found [here](https://drive.google.com/file/d/14kiHDXJgKRogNQw0t78dH5AP399_eu7m/view?usp=sharing)).
Before running the experiments, set the correct ENV variables required for connecting to the database, e.g.,
```shell script
$ export PGUSER=postgres \
    PGHOST=localhost \
    PGPASSWORD=postgres \
    PGDATABASE=blockheader \
    PGPORT=5432 \
    INFURA_ENDPOINT=https://mainnet.infura.io/ \
    GENESIS_BLOCK=9121452 \
    START_BLOCK=9121453 \
    NO_OF_BLOCKS=100
```

### Evaluate on Ganache
Make sure Ganache is running and configured to use port 8545.

TO deploy the smart contracts necessary for the evaluation, run:
```shell script
$ ethrelay-evaluation deploy
```

To setup the smart contracts for the evaluation, simply run:
```shell script
$ ethrelay-evaluation setup
```

To start the evaluation, run:
```
 $ ethrelay-evaluation submission
 $ ethrelay-evaluation dispute
```

You might run into the issue that your account does not have enough funds for the evaluation.
In that case, create a new Ganache workspace pre-funding each account with enough funds (e.g., 100 000 000 ETH).

### Evaluate on Parity Dev Chain
Make sure you have Docker installed, then run:
```shell script
./start-parity.sh
```
This will start a Parity Dev Chain in Docker with a default account already pre-funded with lots of Ether.

Then you can setup and run the evaluation by running the following commands, respectively.
```shell script
$ ethrelay-evaluation deploy parity
$ ethrelay-evaluation setup parity
$ ethrelay-evaluation submission parity
$ ethrelay-evaluation dispute parity
```

### Evaluate on other chains
Setup the Ethereum node and add another configuration with a new network name (e.g., _mynetwork_) in the `truffle-config.js` file.
Then setup and start the evaluation with the following commands.
```shell script
$ ethrelay-evaluation deploy mynetwork
$ ethrelay-evaluation setup mynetwork
$ ethrelay-evaluation submission mynetwork
$ ethrelay-evaluation dispute mynetwork
```

---

## Results
The directory `./results` contains the output of both evaluation scripts (submission and dispute) and a comparison
of the branch junctions logged during submission and the branch junctions of the used dataset. 

## Further info
### Cleanup
Run `npm unlink` to remove the created symlink.

### Generate epoch data
The Ethash smart contract needs to be set up with the epoch data required for performing full block validations.
By default the project contains data for epochs 304-308. 
The data is stored in corresponding JSON files like `./epochs/<epoch>.json`

To generate new epoch JSON files, install the [ETH Relay Go Client](https://github.com/pantos-io/go-ethrelay) 
and run the command `$ go-ethrelay submit epoch <epoch> --json`.

### Generate merkle proof
The gas costs of verifying a transaction are evaluated by verifying the same transaction (0xf04ea290db7113d1cb6d5bd6519140ac9d2c70ec6c13a76b750db749197225af) 
over and over again with an ever growing number of succeeding blocks.

The Merkle proof used for the verification has been pre-generated and can be found in `./merkleproofs/`.
If you want to tweak the evaluation to evaluate another transaction instead, you need to generate the correct Merkle proof of the transaction.

You can do so using the [ETH Relay Go Client](https://github.com/pantos-io/go-ethrelay) 
by running the command `$ go-ethrelay verify transaction <txHash> --json`. 

