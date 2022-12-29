# ChainLink Node 

## Prerequisites:
* Create a folder called data, if it is not created yet
* In the file `docker-compose.yml` change the lines 9 & 20 with your own path
  * Change on line 9: `/Users/dennisshushack/Documents/GitHub/BlockchainApp/ChainLink/data`to the path to the data folder.
  * Change on line 20: `/Users/dennisshushack/Documents/GitHub/BlockchainApp/ChainLink/`to your path wherer the chainlink folder is.

## How to run a node:
1. Install docker desktop and run docker desktop
2. Run `docker compose up`
3. Open `localhost:6688` in a browser
4. Login with the credentials given under apicrendentials.txt in the chainlink-volume folder
5. Add Link and Ethereum to the node
6. Follow the instructions on how to run jobs on the Chainlink website:
https://docs.chain.link/chainlink-nodes/v1/fulfilling-requests/

## Deploy Operator Contract:
1. Go to https://remix.ethereum.org/#url=https://docs.chain.link/samples/ChainlinkNodes/Operator.sol&optimize=false&runs=200&evmVersion=null&version=soljson-v0.8.7+commit.e28d00a7.js
2. Deploy the contract with the given Link Token Contract Address (0x48120Eb14AB6EBe2C4F937c3c4915ae1DaF96736). Use injected Provider of MetaMask.
