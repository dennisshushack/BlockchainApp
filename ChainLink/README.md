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
1. Go to https://remix.ethereum.org/
2. Create the following contract:
```Solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.6.6;
import "@chainlink/contracts/src/v0.6/Oracle.sol";
```
4. Deploy the contract with the given Link Token Contract Address (0x48120Eb14AB6EBe2C4F937c3c4915ae1DaF96736). Use injected Provider of MetaMask.
5. When deployed go to the ChainLink node frontend and grab the key of the node -> Settings -> Key Management -> EVM Chain Accounts -> Copy the address.
6. Go back to your deployed contract on Metamask and go to setFullfillmentPermissions and add nodekey, true i.e `0x62A78B6C989678Ba393662810176Cb4a7F96D951, true`
7. Save the Oracle contract address i.e. 0xA67d3d9e86E932D29217dB73Fb269c753748aF31

## Setting up a ChainLink Job:
1. Go to the Node frontend -> Jobs -> New Jobs
1. Take the following code and change the YOUR_OPERATOR_CONTRACT_ADDRESS with your deployed Oracle.sol contract address. Save this as a new job.
```
# THIS IS EXAMPLE CODE THAT USES HARDCODED VALUES FOR CLARITY.
# THIS IS EXAMPLE CODE THAT USES UN-AUDITED CODE.
# DO NOT USE THIS CODE IN PRODUCTION.

name = "Get > Uint256 - (TOML)"
schemaVersion = 1
type = "directrequest"
# Optional External Job ID: Automatically generated if unspecified
# externalJobID = "b1d42cd5-4a3a-4200-b1f7-25a68e48aad8"
contractAddress = "YOUR_OPERATOR_CONTRACT_ADDRESS"
maxTaskDuration = "0s"
minIncomingConfirmations = 0
observationSource = """
    decode_log   [type="ethabidecodelog"
                  abi="OracleRequest(bytes32 indexed specId, address requester, bytes32 requestId, uint256 payment, address callbackAddr, bytes4 callbackFunctionId, uint256 cancelExpiration, uint256 dataVersion, bytes data)"
                  data="$(jobRun.logData)"
                  topics="$(jobRun.logTopics)"]

    decode_cbor  [type="cborparse" data="$(decode_log.data)"]
    fetch        [type="http" method=GET url="$(decode_cbor.get)" allowUnrestrictedNetworkAccess="true"]
    parse        [type="jsonparse" path="$(decode_cbor.path)" data="$(fetch)"]

    multiply     [type="multiply" input="$(parse)" times="$(decode_cbor.times)"]

    encode_data  [type="ethabiencode" abi="(bytes32 requestId, uint256 value)" data="{ \\"requestId\\": $(decode_log.requestId), \\"value\\": $(multiply) }"]
    encode_tx    [type="ethabiencode"
                  abi="fulfillOracleRequest2(bytes32 requestId, uint256 payment, address callbackAddress, bytes4 callbackFunctionId, uint256 expiration, bytes calldata data)"
                  data="{\\"requestId\\": $(decode_log.requestId), \\"payment\\":   $(decode_log.payment), \\"callbackAddress\\": $(decode_log.callbackAddr), \\"callbackFunctionId\\": $(decode_log.callbackFunctionId), \\"expiration\\": $(decode_log.cancelExpiration), \\"data\\": $(encode_data)}"
                  ]
    submit_tx    [type="ethtx" to="YOUR_OPERATOR_CONTRACT_ADDRESS" data="$(encode_tx)"]

    decode_log -> decode_cbor -> fetch -> parse -> multiply -> encode_data -> encode_tx -> submit_tx
"""
```
