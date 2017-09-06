
# Contract Deployment

## Getting Started

These instructions will allow you to deploy contract on Ethereum network. 
* **[Pre-requisite](#pre-requisite)**
* **[Contract deploy](#using-web3js)**
* **[Contract interaction](#contract-interaction)**
* **[Contract events](#contract-events)**
* **[Filters](#filters)**



# Pre-requisite

Install nodeJS
```sh
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Install web3 npm package
```sh
npm install web3
```
# Using browser
Compile Deploy contracts using browser 
browser-solidity:  <http://ethereum.github.io/browser-solidity/>

# Using Web3.js
```javascript
var Web3 = require('web3');
var web3 = new Web3();

web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

console.log('RPC connection:',web3.isConnected());
var coinbase = web3.eth.coinbase;
console.log(coinbase);

var balance= web3.eth.getBalance(coinbase);

console.log('Balance in WEI:',balance.toString());

var ether= web3.fromWei(balance,'ether');
console.log('Balance in Ethers',ether.toString());
```

## Deploy

```javascript
var Web3 = require('web3');
var web3 = new Web3();

web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

console.log('RPC connection:',web3.isConnected());

// Bytecode
var bytecode ='0x6060604052600a600055341561001457600080fd5b5b610123806100246000396000f30060606040526000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff168063165c4a1614604757806320965255146070575b600080fd5b3415605157600080fd5b606e60048080359060200190919080359060200190919050506096565b005b3415607a57600080fd5b608060ed565b6040518082815260200191505060405180910390f35b8082026000819055507f676e649ee7d1ec678c25095d6a620d0e336437caf612d66e1b0c0630e1cfce74828260005460405180848152602001838152602001828152602001935050505060405180910390a15b5050565b6000805490505b905600a165627a7a72305820883a1f72d954e4d67880359f5ee6f4860415ac6cfc516bbde49122c66bdb04760029';

// Application Binary Interface (ABI)
var ABI = [{"constant":false,"inputs":[{"name":"a","type":"uint256"},{"name":"b","type":"uint256"}],"name":"multiply","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValue","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":false,"name":"a","type":"uint256"},{"indexed":false,"name":"b","type":"uint256"},{"indexed":false,"name":"c","type":"uint256"}],"name":"MulEvent","type":"event"}];

var testContract = web3.eth.contract(ABI); //Contract Object


// Contract Instance
var contract = testContract.new(
   {
     from: web3.eth.accounts[0],                                        // Ethereum account 
     data: bytecode,                                                    //Bytecode
     gas: '4300000'                                                     //Total gas required
   }, function (e, contract){  //Callback function
    if (typeof contract.address !== 'undefined') {
         console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
    }
 })

```

## Contract interaction

```javascript
var Web3 = require('web3');
var web3 = new Web3();

web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

console.log('RPC connection:',web3.isConnected());

// Application Binary Interface (ABI)
var ABI = [{"constant":false,"inputs":[{"name":"a","type":"uint256"},{"name":"b","type":"uint256"}],"name":"multiply","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValue","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":false,"name":"a","type":"uint256"},{"indexed":false,"name":"b","type":"uint256"},{"indexed":false,"name":"c","type":"uint256"}],"name":"MulEvent","type":"event"}];



var contractInstance= web3.eth.contract(ABI).at("0x7255a8408f9a7cae231de9a4706cf1728e0cfdb3") 

console.log('Get state:',contractInstance.getValue().toString());// Query state


 var txId=contractInstance.multiply.sendTransaction(10,50,{from:web3.eth.accounts[0]}); // contract interaction transaction

 console.log('tx id: ',txId );




```

## Contract events
```javascript
var Web3 = require('web3');
var web3 = new Web3();

web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

console.log('RPC connection:',web3.isConnected());

// Application Binary Interface (ABI)
var ABI = [{"constant":false,"inputs":[{"name":"a","type":"uint256"},{"name":"b","type":"uint256"}],"name":"multiply","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValue","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":false,"name":"a","type":"uint256"},{"indexed":false,"name":"b","type":"uint256"},{"indexed":false,"name":"c","type":"uint256"}],"name":"MulEvent","type":"event"}];



var contractInstance= web3.eth.contract(ABI).at("0x7255a8408f9a7cae231de9a4706cf1728e0cfdb3") 


var myEvent = contractInstance.MulEvent({a:10}, {fromBlock: 0, toBlock: 'latest'});

// would get all past logs again.
var myResults = myEvent.get(function(error, logs){ 
//console.log(logs);

for(i=0;i<logs.length;i++)
{
console.log(logs[i]);
}

 });
```
## Filters

```javascript
var Web3 = require('web3');
var web3 = new Web3();

web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));

console.log('RPC connection:',web3.isConnected());

// Application Binary Interface (ABI)
var ABI = [{"constant":false,"inputs":[{"name":"a","type":"uint256"},{"name":"b","type":"uint256"}],"name":"multiply","outputs":[],"payable":false,"type":"function"},{"constant":true,"inputs":[],"name":"getValue","outputs":[{"name":"","type":"uint256"}],"payable":false,"type":"function"},{"anonymous":false,"inputs":[{"indexed":false,"name":"a","type":"uint256"},{"indexed":false,"name":"b","type":"uint256"},{"indexed":false,"name":"c","type":"uint256"}],"name":"MulEvent","type":"event"}];



var contractInstance= web3.eth.contract(ABI).at("0x7255a8408f9a7cae231de9a4706cf1728e0cfdb3") 


//using filter All events 
 var filter=web3.eth.filter({fromBlock:1,toBlock:'latest',address:"0x7255a8408f9a7cae231de9a4706cf1728e0cfdb3"});

 filter.get(function(error,result){
     console.log("all events")
 						if(error)
 						{
 						console.log(error);
 						}
 							console.log("\n\n"+JSON.stringify(result)+"\n");
 });
```
