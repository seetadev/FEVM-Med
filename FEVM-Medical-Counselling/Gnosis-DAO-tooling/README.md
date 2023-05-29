# Gnosis Dev Tooling and integration with SocialCalc spreadsheet for tabulation, organization and visualization of report data

## Setting Up Forge:

0. Open the terminal of your choice.
1. Download `foundryup` by using the following command:
```shell
curl -L https://foundry.paradigm.xyz | bash
```
2. (Optional) If for some reason, step 1 is not working for you, try building this from source using the instructions provided on [Foundry's official wiki](https://book.getfoundry.sh/getting-started/installation#building-from-source)
3. This will download foundryup. Then install Foundry by running:
```shell
foundryup
```
4. Check whether `forge` has been installed or not by running the following command:
```shell
forge --version
```

## Setting up the project folder

0. Setup the folder that you want to use and initialize forge.
1. Navigate to the location of your choice.
2. Then use the following commands:
```shell
mkdir my-app
cd my-app
forge init
```
3. Now open this folder in VS Code (or any editor of your choice). You should get a file structure looking like this:

<img width="576" alt="Screenshot 2022-10-03 at 12 14 17 PM" src="https://user-images.githubusercontent.com/32522659/193516127-f10d748e-b993-43fc-a5d3-f6ec658d84e4.png">

4. You can run `forge build` and then `forge test` to see whether the defaults are working correctly or not.

<img width="1728" alt="Screenshot 2022-10-05 at 5 40 16 PM" src="https://user-images.githubusercontent.com/32522659/194057375-68d9d541-4b0c-47d9-bbfd-79061c3bf21d.png">

## Importing dependencies

We would be importing an ERC20 token contract from the OpenZeppelin repo, which is the most used and standard repository used across the Solidity development ecosystem.

To install this dependency, use the following command:

```shell
forge install @openzeppelin/openzeppelin-contracts --no-commit
```

You can see all the installed dependencies by navigating to `lib/openzeppelin-contracts`.

## Creating our ERC20 Token Contract

0. Under the folder `src` create a new contract file named `Token.sol` (or whatever you like)
1. Copy and paste the following code into that file:

```solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

import "lib/openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";

contract TestToken is ERC20 {
    constructor() ERC20 ("TestTokenGnosis", "TTG"){
        _mint(msg.sender, 1000*10**18);
    } 
}

```
2. What this code does is, simply mint new tokens named TestTokenGnosis with symbol TTG to the `msg.sender` or whosoever deploys this contract. By default the decimals for TTG would be 18 and hence when you write `1000 * (10 ** 18)` it simply means 1000 tokens.

3. Save the file and use the command `forge build` to see if the contract is compiling correctly. If everything goes well, it should look something like this:

<img width="1728" alt="Screenshot 2022-10-05 at 5 40 16 PM" src="https://user-images.githubusercontent.com/32522659/194059102-f9a02893-f23f-4ed1-ae0c-e898254affbf.png">

## Preparing to deploy our token contract

0. Any blockchain is a chain of blocks and the people responsible for creating and adding new blocks to this chain are called miners or stakers. As normal users of blockchain, we provide some fee to these people for their service and to use the computational power of the blockchain which is known as gas fee.

1. Now to deploy your contract and to later interact with it, we would need to pay these miners some gas fee. In an actual blockchain, the fee will be paid in real money denominated in the native currency of the blockchain (ETH for Ethereum, BTC for Bitcoin and xDAI for Gnosis). Similarly there is a concept of mock blockchains called testnets, where we can use mock money to pay gas fee (for example Chiado xDAI for Chiado network which is a Gnosis testnet)

2. To pay these gas fee and later interact with our and other contract, we would need something called a wallet. Wallets store private keys, keeping your crypto safe and accessible. They also allow to receive and send assets, some also to interact with smart contracts and dApps.

3. We will be using a wallet called Metamask, which is arguably the most famous crypto wallet out there right now. However you are free to choose any wallet of your liking. 

MetaMask is a web browser extension and mobile app that allows you to manage your Gnosis private keys. By doing so, it serves as a wallet for xDai, GNO and other tokens, and allows you to interact with decentralized applications, or dapps.

Further instructions on how to download and set up your Metamask wallet can be found on the [official Gnosis wiki](https://docs.gnosischain.com/tools/wallets/metamask). I would suggest setting up both the Gnosis mainnet and the Chiado testnet so that you can deploy your smart contracts wherever you like.

4. Now that we have our wallet, we would need some funds to pay as gas fee to deploy our token smart contract and to later interact with it. Gnosis has created faucets for disbursing small amounts of these funds so that people can get started using the chain. To grab your funds, go to the official [Gnosis faucet website](https://gnosisfaucet.com/) and request funds for Chiado testnet (and also the Gnosis mainnet if you want). If things go as expected, you should see a notification like this:

<img width="1728" alt="Screenshot 2022-10-06 at 9 32 59 AM" src="https://user-images.githubusercontent.com/32522659/194211450-a5fe53df-a527-4cda-920f-4cea9cd1209a.png">

On the website you will realise that while you can get `1 Chiado xDAI` from the faucet for the Chiado testnet you only get `0.001 xDAI` from the faucet for the Gnosis maninet. The reason for this as explained above is that the Chiado network is a testnet or a mock blockchain to test your applications, hence the gas fee paid here is also in a mock currency called `Chiado xDAI`. However for the Gnosis mainnet, which is an actual blockchain, you will need to pay the gas fee in real money in terms of a real (crypto) currency called xDAI, and hence the lesser amount.

Post this, you should be able to see the funds in your metamask wallet. Sweet.

## Deploying our smart contract

0. Make sure that your Token contract is compiling without any issues by using the following command:

```shell

forge build

```

If things went as expected, you should see something like this:

<img width="1728" alt="Screenshot 2022-10-05 at 5 40 16 PM" src="https://user-images.githubusercontent.com/32522659/194212564-4f1f90f1-3e11-4997-8704-ee6a40bf8521.png">

1. Now we need to grab our private keys in order to deploy our token smart contract. 

    + Open your Metamask (or whatever wallet you installed) extension and make sure you are on the correct account (one from which you want to deploy the token smart contract).
    + Click on the kebab (three dots) menu
    + Click on Account Details
    + Click on Export Private Key
    + Type your Metamask password
    + Now your private keys are exposed. Copy and paste them somewhere on your system.
    
Your exposed private key window would look something like this (This is for demonstration purposes and this is a throwaway wallet)

![Private Key Metamask](https://0x.games/wp-content/uploads/2021/06/img-2021-06-21-17-30-32.png)

2. A note about private keys: Your private keys are supposed to be private and not meant to be shared with **anyone** under any circumstance. If anyone gets hold of your private keys, they then have unrestricted access to your wallets and all the assets inside it. So, take care and either delete your private keys from your system after this tutorial or simply create a new wallet/account and use that. 

3. Once you do have your private keys, its time to use them to deploy your token smart contract to either the Chiado testnet or the Gnosis mainnet. The command to deploy that is as follows:

In our case, the first placeholder <YourContract> would be replaced by `Token` and the second <YourContract> placeholder will be replaced by `TestToken` and the <your_private_key> will be replaced by whatever we grabbed in step 1 of this section.

+ For Chiado testnet
```shell

forge create --rpc-url https://rpc.chiadochain.net --private-key <your_private_key> src/<YourContract>.sol:<YourContract>

```

+ For Gnosis mainnet
```shell

forge create --rpc-url https://rpc.gnosischain.com --private-key <your_private_key> src/<YourContract>.sol:<YourContract>

```

If things go as expected, you should see a screen similar to this:

<img width="1728" alt="deploy-success" src="https://user-images.githubusercontent.com/32522659/194214389-dfdf4697-04b9-4b6f-89f4-b0f8a33d17e3.png">

4. Congratulations!! You just deployed your tokens on the Chiado testnet (or the gnosis mainnet) and the address of your tokens (or token contract) is the address written infront of the `deployed to` spec in the earlier image.

5. You can also see the deployment done by you (your wallet) on the Gnosis/Chiado block explorer. For this you need to visit the official [Chiado explorer](https://blockscout.chiadochain.net/) or [Gnosis explorer](https://gnosisscan.io/) depending on where you deployed and search for your wallet address. In the page that opens up, you should see a deployment done by you, something like this:
    
    <img width="1728" alt="Screenshot 2022-10-05 at 5 13 24 PM" src="https://user-images.githubusercontent.com/32522659/194222864-3c57b1b8-3584-410a-91b6-16c67a16c2bb.png">
    
6. Remember the code that we wrote in `Token.sol` ? There we minted 1000 `TTG` tokens to the msg.sender (which is our deploying wallet in this case), so we should be able to see those 1000 tokens in our wallet, right? We need to include our token in our wallets for that to happen. The steps to do that are as follows:
    + Click on your Metamask wallet extension
    + Make sure you are on the correct account and correct network
    + Click on the `Import Tokens` option
    + In the `Token Contract Address` fill the address where your token contract was deployed to
    + Other fields should get autofilled (if not, fill the name and decimals which was 18 by default)
    + Click on `Add Custom Token`
    + Now you should be able to see your token in your wallet
    + Congratulations!! Now you can send and recieve your tokens to and from your friends using your Metamask wallet.
    
If things went as expected, you should see something like this:
    
<img width="1728" alt="Screenshot 2022-10-05 at 5 19 41 PM" src="https://user-images.githubusercontent.com/32522659/194227648-7a96ae5a-2d36-4506-b870-8a4cc28250d3.png">

## Connecting your contract to a front end
    
### Creating a new React App

0. Create a new folder separate from the one where we working till now and please update npm version to at least node 14.
1. Create a new react application using the following command: (we are naming the app `erc20-frontend`)
    ```shell
        npx create-react-app erc20-frontend
    ```
    If things go as expected, you should see a screen as follows:
    
    <img width="1728" alt="Screenshot 2022-10-06 at 1 45 35 PM" src="https://user-images.githubusercontent.com/32522659/194259235-2db9b0f7-2bb0-49bd-8d0a-ece8c48de45d.png">
2. Add the ethers library to your project using the following command:
    ```shell
    npm install --save ethers
    ```
2. Navigate to the folder you created, using `cd erc20-frontend`
3. Use the `npm run start` command to see if the default react app is opening in your browser. If things go as expected, you should see a screen like this:
    <img width="1502" alt="Screenshot 2022-10-06 at 1 47 44 PM" src="https://user-images.githubusercontent.com/32522659/194259826-5aa56852-861d-4309-b2cd-149c367391c7.png">
4. Now you can exit using the `Ctrl + C` command.
    
### Writing the Frontend code
   
0. Navigate to src/App.js and clear all the code written there.
1. Paste the following code in this file:
    
```javascript
import { useState } from "react";
import {ethers} from 'ethers';
import Token from "./contracts/TestToken.json";

import './App.css';

const tokenAddress = "0x96273AAc53dED55e0cE26E7dd4d834662F163516"; // Change this to your recent deployed token address

function App() {
  const [userAccount, setUserAccount] = useState('');
  const [amount, setAmount] = useState(ethers.BigNumber.from(0));
  const [balance, setBalance] = useState(0);
  const [currentUser, setCurrentUser] = useState('yourAddress');
  const [transferred, setTransferred] = useState(false);
  const [balanceCall, setBalanceCall] = useState(false);


  async function requestAccount() {
    await window.ethereum.request({method: 'eth_requestAccounts'});
  }

  async function getBalance() {
    if(typeof window.ethereum !== 'undefined') {
      const [account] = await window.ethereum.request({method: 'eth_requestAccounts'});
      setCurrentUser(account);
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const contract = new ethers.Contract(tokenAddress, Token.abi, provider);
      const balance = await contract.balanceOf(account);
      setBalance((parseInt(balance))/1e18);
      setBalanceCall(true);
      console.log("Balance: ", (parseInt(balance))/1e18);
    }
  }

  async function sendCoins() {
    if(typeof window.ethereum !== 'undefined') {
      await requestAccount();
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner();
      const contract = new ethers.Contract(tokenAddress, Token.abi, signer);
      let stringAmount = amount.toString();
      stringAmount = stringAmount + "000000000000000000";
      console.log(stringAmount);
      const transaction = await contract.transfer(userAccount, ethers.BigNumber.from(stringAmount));
      await transaction.wait();
      getBalance();
      setTransferred(true);
      console.log(`${amount} GTT coins successfully sent to ${userAccount}`);
    }
  }

  return (
    <div className="App">
      <header className="App-header">
        <input onChange = {e => setUserAccount(e.target.value)} placeholder = "Account to transfer to" />
        <input onChange = {e => setAmount(e.target.value)} placeholder = "Enter Amount"/>
        <span>
        <button onClick = {getBalance}>Get Balance</button>
        <button onClick = {sendCoins}>Send Coins</button>
        </span>
        <br/>
        <> 
          {
            balanceCall ? <h2>Balance of {currentUser}: {balance}</h2> : null
          }
        </>
        <>
          {
            transferred ? <h2>Transfer Successful</h2> : null
          } 
        </>
      </header>
    </div>
  );
}

export default App;
 
```

2. Don't forget to change the `const tokenAddress` to your deployed address
3. Now, you have to go back to the ERC20 folder where you created and deployed your token contract using Forge.
4. There you'll see `out/Token.sol/TestToken.json`. Open that file and copy the json file.
5. Come back to your frontend folder, create a new folder called `contracts` under the `src` folder.
6. Now under this `contracts` folder, create a new file called `TestToken.json` and paste the json you copied in step 3.
7. Run the app using the command:
    ```shell
    npm run start
    ```
    
### Interacting with the frontend
    
0. If everything went well, you should see a screen like this:
    
<img width="1728" alt="Screenshot 2022-10-06 at 5 47 15 PM" src="https://user-images.githubusercontent.com/32522659/194310437-7f282483-375d-4a6a-a01f-d4a1b3298e7c.png">

1. You will either get a prompt automatically to connect your wallet, or will get once you click on the `Get Balance` button.
2. Clicking on `Get Balance` will show you your balance (assuming the default 18 decimals of your tokens)
3. Entering the amount and reciever address and then clicking the `Send Coins` button will transfer the coins, after you approve the transaction using your Metamask wallet.
4. After both the operations, a prompt will be updated on the screen.
    > Note: After using the `Send Coins` button, wait for atleast 15-20 seconds for the transaction to go through. If it does not update even after 1+ minute, check the console (after clicking on inspect)
5. If both the functions work as expected, you will see a screen like this:
    <img width="1728" alt="Screenshot 2022-10-06 at 5 53 06 PM" src="https://user-images.githubusercontent.com/32522659/194311605-96ecbb82-f19e-4158-b092-51e29cc23f8d.png">

----------------------
    
#### Congratulations!! You just wrote a contract, deployed it on the Gnosis mainnet/testnet and then interacted with the contract using a front end that you created.
    
