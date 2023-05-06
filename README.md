# How to Build An English Auction Smart Contract On The Celo Blockchain

English Auction is a type of auction in which bidders openly compete against each other, with each subsequent bid being higher than the previous bid. This style of auction is also known as an ascending-price auction.

A smart contract is a self-executing contract that is capable of enforcing the terms of an agreement. In the context of an English Auction, a smart contract can be used to govern the bidding process and the transfer of ownership of an item to the highest bidder, in our case it's an NFT.

In this article, you will learn how to implement an NFT English auction smart contract on the Celo blockchain network. Finally, we will demonstrate how our English smart contract works.

## Prerequisites

Before you get started with this tutorial, make sure you have a basic understanding of the following:

* [Solidity](https://docs.soliditylang.org/)
    
* [Blockchain technology](https://www.simplilearn.com/tutorials/blockchain-tutorial/blockchain-technology#:~:text=advance%20your%20career.-,What%20Is%20Blockchain%20Technology%3F,computers%20participating%20in%20the%20blockchain.)
    
* [Laika](https://medium.com/laika-lab/laika-quickstart-guide-88324fdc0baa)
    
* Experience using [Remix IDE](https://remix.ethereum.org/)
    
* [How a smart contract works](https://www.ibm.com/topics/smart-contracts)
    

## Requirements

To successfully build this smart contract, your system should have the following tools:

* A chromium-based web browser
    
* An internet connection
    
* The Celo plugin activated in Remix IDE
    
* [The Remix IDE](https://remix.ethereum.org/)
    
* [The Metamask Extension Wallet](https://metamask.io/download/)
    
* A Metamask account with [Alfajores CELO tokens](https://faucet.celo.org/)
    
* A [Celo extension wallet](https://chrome.google.com/webstore/detail/celoextensionwallet/kkilomkmpmkbdnfelcpgckmpcaemjcdh?hl=en) account with Alfajores CELO tokens
    

## English Auction Smart Contract Development

Navigate to [The Remix IDE](https://remix.ethereum.org/) or any other IDE compatible with the Solidity programming language. Follow the steps below to create an English auction smart contract.

### Step 1

In the **Remix IDE**, create a new file. Name it `EnglishAuction.sol`. This will contain our smart contract code. Open the file.

<img src="https://github.com/zogram/English-Auction-Celo-Tuts/blob/main/create-file.png" height="500" />

### Step 2

Copy the code below, and paste it into the opened `EnglishAuction.sol` file.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC721 {
    function safeTransferFrom(address from, address to, uint tokenId) external;

    function transferFrom(address, address, uint) external;
}
```

At the top level of our contract, we have a comment that specifies the license the contract uses. The **SPDX license identifier** is a standard format that communicates licensing information about a software package.

Next, we defined the version of the Solidity compiler that should be used to compile our smart contract code. In our case, we specified `version 0.8.17`.

Next, we declared the IERC721 interface. IERC721 are standards that provide a way to interact with non-fungible tokens (NFTs). The IERC721 interface ensures that contracts and applications that implement the IERC721 interface are compatible with one another. In other words, these contracts and applications can easily transfer NFTs between one another.

### Step 3

Add the code below to `EnglishAuction.sol` file.

```solidity
contract EnglishAuction {
    event Start();
    event Bid(address indexed sender, uint amount);
    event Withdraw(address indexed bidder, uint amount);
    event End(address winner, uint amount);

    IERC721 public nft;
    uint public nftId;

    address payable public auctionSeller;
    uint public endEnglishAuction;
    bool public startedEnglishAuction;
    bool public endedEnglishAuction;

    address public auctionHighestBidder;
    uint public auctionHighestBid;
    mapping(address => uint) public auctionTotalBids;
}
```

The contract defines four events that can be emitted (i.e., triggered) during the execution of the contract. These events are:

* `Start()`
    
* `Bid()`
    
* `Withdraw()`
    
* `End()`.
    

Events are used to provide feedback to the users of the contract when certain actions are taken or conditions are met.

Next, we have two public variables that define the NFT to be sold in the auction, these public variables are:

* `IERC721 public nft` - This is a public variable that stores the NFT to be auctioned.
    
* `uint public nftId` - This is another public integer variable. It stores the unique token ID of the non-fungible token to be auctioned in this smart contract.
    

Next, we declared some variables involving the **bids**. These include:

* `address payable public auctionSeller`: This is a public variable of type `address payable`, which stores the address of the seller who created the auction. The `payable` keyword allows this address to receive funds.
    
* `uint public endEnglishAuction`: Another public variable of type `uint`, which stores the timestamp when the auction will end.
    
* `bool public startedEnglishAuction`: This public boolean variable keeps track of whether the auction has started or not. Its value is `true` if the auction has started, and `false` otherwise.
    
* `bool public endedEnglishAuction`: This public boolean variable keeps track of whether the auction has ended or not. Its value is `true` if the auction has ended, and `false` otherwise.
    
* `address public auctionHighestBidder`: This public variable of type `address` stores the address of the highest bidder in the auction.
    
* `uint public auctionHighestBid`: Another public variable of type `uint`, which stores the highest bid amount made in the auction so far.
    
* `mapping(address => uint) public auctionTotalBids`: This is a public mapping that maps addresses to their respective bid amounts. Whenever a bidder makes a new bid, the `address => uint` pair is added to the mapping. Where `address` is the address of the bidder and `uint` is the amount they bid.
    

### Step 4

Add the code at the end of `EnglishAuction` contract.

```solidity
constructor(address _nft, uint _nftId, uint _auctionStartingBid) {
        nft = IERC721(_nft);
        nftId = _nftId;

        auctionSeller = payable(msg.sender);
        auctionHighestBid = _auctionStartingBid;
    }
```

The above code is a constructor function. It initializes some of our variables during the deployment of our smart contract. Below is a proper breakdown of the code above:

* The constructor takes three parameters:
    
    * The `_nft` parameter is an NFT contract address that represents an instance of the ERC-721 token contract.
        
    * The `_nftId` parameter is an unsigned integer that represents the specific token ID of the NFT being auctioned.
        
    * The `_auctionStartingBid` parameter is an unsigned integer that represents the starting bid for the auction.
        
* The `auctionSeller` variable is a storage variable that stores the address of the user starting the auction.
    
* The `payable` keyword indicates that the `auctionSeller` address can receive Celo.
    
* The `auctionHighestBid` variable is a storage variable that stores the starting bid for the auction. The value of `auctionHighestBid` changes has higher bids are made.
    

### Step 4

Add the code at the end of `EnglishAuction` contract.

```solidity
function startEnglishAuction() external {
        require(!startedEnglishAuction, "started");
        require(msg.sender == auctionSeller, "not auctionSeller");

        nft.transferFrom(msg.sender, address(this), nftId);
        startedEnglishAuction = true;
        endEnglishAuction = block.timestamp + 2 days;

        emit Start();
    }
```

The `startEnglishAuction` function starts an auction when called. In the `startEnglishAuction` function, we have the following code:

* `require(!startedEnglishAuction, "started")` - This checks whether the auction has already been started. If it has, the function throws an error with the message "started".
    
* `require(msg.sender == auctionSeller, "not auctionSeller")` - This checks whether the caller of the function is the same as the `auctionSeller` (the user that initiated the auction). If the caller is not the `auctionSeller`, the function throws an error with the message "not auctionSeller".
    
* `nft.transferFrom(msg.sender, address(this), nftId)` - If the function has not thrown any errors, this transfers ownership of the token from the `auctionSeller` to the auction contract.
    
* `startedEnglishAuction = true` - The `startedEnglishAuction` variable is set to `true` to indicate that the auction has been started.
    
* `endEnglishAuction = block.timestamp + 2 days` - The `endEnglishAuction` variable is set to the current time (as represented by `block.timestamp`) plus two days, indicating the time the auction ends.
    
* `emit Start()` - an `emit` statement is used to trigger the `Start` event, indicating that the auction has been started.
    

### Step 5

Add the code at the end of `EnglishAuction` contract.

```solidity
function bidAmount() external payable {
        require(startedEnglishAuction, "not started");
        require(block.timestamp < endEnglishAuction, "ended");
        require(msg.value > auctionHighestBid, "value < highest");
        if (auctionHighestBidder != address(0)) {
            auctionTotalBids[auctionHighestBidder] += auctionHighestBid;
        }

        auctionHighestBidder = msg.sender;
        auctionHighestBid = msg.value;

        emit Bid(msg.sender, msg.value);
    }
```

The `bidAmount` function allows potential bidders to bid once the auction starts. In the `bidAmount` function, we have the following code:

* `require(startedEnglishAuction, "not started")` - checks whether the auction has started. If it hasn't started, the function throws an error with the message "not started".
    
* `require(block.timestamp < endEnglishAuction, "ended")` - checks whether the auction has ended. If it has ended, the function throws an error with the message "ended".
    
* `require(msg.value > auctionHighestBid, "value < highest")` - checks whether the new bid is greater than the current highest bid (`auctionHighestBid`). If it's not greater, the function throws an error with the message "value &lt; highest".
    
* `if (auctionHighestBidder != address(0)) { -auctionTotalBids[auctionHighestBidder] += auctionHighestBid; }` - If the current highest bidder is not the contract owner (meaning that there has already been a bid), the `auctionTotalBids` mapping is updated to reflect the new total for that bidder (adding the previous highest bid value).
    
* `auctionHighestBidder = msg.sender` - The current highest bidder (`auctionHighestBidder`) is updated to be the address of the caller (`msg.sender`).
    
* `auctionHighestBid = msg.value` - The `auctionHighestBid` is set to the new highest bid (`msg.value`).
    
* `emit Bid(msg.sender, msg.value)` - The `emit` statement triggers the `Bid` event, indicating that a new bid has been made and includes the new highest bidder's address and the amount they bid.
    

### Step 6

Add the code at the end of `EnglishAuction` contract.

```solidity
function withdrawBids() external {
        uint balance = auctionTotalBids[msg.sender];
        auctionTotalBids[msg.sender] = 0;
        payable(msg.sender).transfer(balance);

        emit Withdraw(msg.sender, balance);
    }
```

The `withdrawBids` function allows bidders who aere not the highest bidders to withdraw their bids. In the `withdrawBids` function, we have the following code:

* `uint balance = auctionTotalBids[msg.sender]` - This retrieves the total bid amount of the current caller (i.e., `msg.sender`) from the `auctionTotalBids` mapping and stores it in a local variable called `balance`.
    
* `auctionTotalBids[msg.sender] = 0` - This sets the total bid amount of the caller to zero, effectively withdrawing their bid from the auction.
    
* `payable(msg.sender).transfer(balance)` - This uses the `payable()` function to transfer the right amount to the caller's address.
    
* `emit Withdraw(msg.sender, balance)` - Finally, the function emits a `Withdraw` event that notifies the user that their bid has been withdrawn by passing in `msg.sender` and `balance` as parameters.
    

### Step 7

Add the code at the end of `EnglishAuction` contract.

```solidity
 function endAuction() external {
        require(startedEnglishAuction, "not started");
        require(block.timestamp >= endEnglishAuction, "not ended");
        require(!endedEnglishAuction, "ended");

        endedEnglishAuction = true;
        if (auctionHighestBidder != address(0)) {
            nft.safeTransferFrom(address(this), auctionHighestBidder, nftId);
            auctionSeller.transfer(auctionHighestBid);
        } else {
            nft.safeTransferFrom(address(this), auctionSeller, nftId);
        }

        emit End(auctionHighestBidder, auctionHighestBid);
    }
```

The `endAuction` function allows auction seller to end the auction. In the `endAuction` function, we have the following code:

* `require(startedEnglishAuction, "not started")` - This checks if the English auction has started, and if it has not, it throws an error using the `require()` function with the message "not started".
    
* `require(block.timestamp >= endEnglishAuction, "not ended")` - This checks if the current block's timestamp is greater than or equal to the ending time of the English auction. If the auction has not yet ended, it throws an error with the message "not ended".
    
* `require(!endedEnglishAuction, "ended")` - This checks whether the auction has already ended by checking the `endedEnglishAuction` variable. If the auction has already ended, the function throws an error with the message "ended".
    
* `endedEnglishAuction = true` - If none of the above conditions triggers a `require()` error, the function sets the `endedEnglishAuction` variable to `true`, effectively marking the end of the English auction.
    
* `if (auctionHighestBidder != address(0)) { nft.safeTransferFrom(address(this), auctionHighestBidder, nftId); auctionSeller.transfer(auctionHighestBid); }` - If there was at least one valid bid on the auction, the function transfers the NFT token with `nftId` from the contract to the `address` of the highest bidder using the `safeTransferFrom()` function. It also transfers the highest bid amount to the `auctionSeller` address using the `transfer()` function.
    
* `else { nft.safeTransferFrom(address(this), auctionSeller, nftId); }` - If there was no valid bid on the auction, the function transfers the NFT token back to the `auctionSeller` address using the `safeTransferFrom()` function.
    
* Finally, the function emits an `End` event with the `auctionHighestBidder` and `auctionHighestBid` as parameters, which notifies the users who participated in the auction about the result.
    

Now, we have created an English Auction smart contract, your smart contract shouyld look like the one below.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC721 {
    function safeTransferFrom(address from, address to, uint tokenId) external;

    function transferFrom(address, address, uint) external;
}

contract EnglishAuction {
    event Start();
    event Bid(address indexed sender, uint amount);
    event Withdraw(address indexed bidder, uint amount);
    event End(address winner, uint amount);

    IERC721 public nft;
    uint public nftId;

    address payable public auctionSeller;
    uint public endEnglishAuction;
    bool public startedEnglishAuction;
    bool public endedEnglishAuction;

    address public auctionHighestBidder;
    uint public auctionHighestBid;
    mapping(address => uint) public auctionTotalBids;

    constructor(address _nft, uint _nftId, uint _auctionStartingBid) {
        nft = IERC721(_nft);
        nftId = _nftId;

        auctionSeller = payable(msg.sender);
        auctionHighestBid = _auctionStartingBid;
    }

    function startEnglishAuction() external {
        require(!startedEnglishAuction, "started");
        require(msg.sender == auctionSeller, "not auctionSeller");

        nft.transferFrom(msg.sender, address(this), nftId);
        startedEnglishAuction = true;
        endEnglishAuction = block.timestamp + 2 days;

        emit Start();
    }

    function bidAmount() external payable {
        require(startedEnglishAuction, "not started");
        require(block.timestamp < endEnglishAuction, "ended");
        require(msg.value > auctionHighestBid, "value < highest");
        if (auctionHighestBidder != address(0)) {
            auctionTotalBids[auctionHighestBidder] += auctionHighestBid;
        }

        auctionHighestBidder = msg.sender;
        auctionHighestBid = msg.value;

        emit Bid(msg.sender, msg.value);
    }

    function withdrawBids() external {
        uint balance = auctionTotalBids[msg.sender];
        auctionTotalBids[msg.sender] = 0;
        payable(msg.sender).transfer(balance);

        emit Withdraw(msg.sender, balance);
    }

    function endAuction() external {
        require(startedEnglishAuction, "not started");
        require(block.timestamp >= endEnglishAuction, "not ended");
        require(!endedEnglishAuction, "ended");

        endedEnglishAuction = true;
        if (auctionHighestBidder != address(0)) {
            nft.safeTransferFrom(address(this), auctionHighestBidder, nftId);
            auctionSeller.transfer(auctionHighestBid);
        } else {
            nft.safeTransferFrom(address(this), auctionSeller, nftId);
        }

        emit End(auctionHighestBidder, auctionHighestBid);
    }
}
```

## Smart Contract Deployment

Now that we have implemented an English Auction smart contract, let's deploy this smart contract on the **Celo Alfajores network**. But before we can deploy our English auction smart contract we need to **deploy and mint an NFT**.

### Step 1: Deploy and Mint NFT

If you don't know how to build and deploy one, go through this tutorial on how to [deploy and mint NFTs on the Celo Alfajores network](https://dacade.org/communities/celo/courses/celo-201/learning-modules/30a4b854-6722-488f-937f-c26591b89f2e).

After you are done deploying the contract, mint an NFT by calling and passing the necessary parameter on the **safeMint or mint** function.

<img src="https://github.com/zogram/English-Auction-Celo-Tuts/blob/main/safemint.png" height="500" />

### Step 2: Deploy English Auction Smart Contract

Follow the steps below to deploy your English auction on the celo alfajores network.

1. Activate the **Remix IDE Celo plugin**.
    
2. Click on the **Celo plugin icon**.
    
3. Click on **connect** button to connect the **Celo extension wallet**.
    
4. Click on the **compile** button to compile the smart contract.
    
5. Enter and fill in the details of the parameters provided. These include:
    
    1. `_nft`: This is the contract address of the NFT you want to auction.
        
    2. `_nftId`: This is a unique identifier or id for the NFT you want to auction.
        
    3. `__auctionStartingBid`: This is the minimum bid amount that a bidder must place to participate in the auction. Adjusted according to your preference.
    
    <img src="https://github.com/zogram/English-Auction-Celo-Tuts/blob/main/deployauction.png" height="500" />
        
6. Click on the **deploy** button to deploy the smart contract.
    
7. Confirm the completion of the smart contract in the Celo wallet extension.
    
8. Wait for the contract to be deployed.
    
9. Take note of the generated **contract address and** [**Contract Application Binary Interface (ABI)**](https://docs.soliditylang.org/en/v0.8.19/abi-spec.html) as we will need it to test our smart contract on Laika.
    

### Step 3: Approve English Auction Smart Contract

In this step, you are to approve the transfer of ownership of the NFT minted to the English auction smart contract by following the steps below:

1. Navigate to the deployed `NFT smart contract`.
    
2. Click on the **approve** function.
    
3. Enter the **address** of your English auction smart contract and the NFT's **id**.
    
4. Call and approve the transaction.

<img src="https://github.com/zogram/English-Auction-Celo-Tuts/blob/main/approve-correct.png" height="500" />
    

### Test Smart Contract Using Laika

[Laika](https://web.getlaika.app/) is a tool for quick interaction with smart contracts. Follow the steps below to test some of the functions of our smart contract.

1. Navigate to [**Laika**](https://web.getlaika.app/) in your browser.
    
2. Click on the **connect** button to connect your Metamask wallet. Use the same Celo address used in deploying the English auction smart contract.
    
3. Set the Metamask wallet network to Celo Alfajores.
    
4. Click on the **new requests** button close to the collections tab.
    
5. Click on the **new request** button on the interface displayed.
    
6. Copy and paste the smart contract **ABI** and **contract address** of the English auction smart contract from **Remix IDE** into the provided field.
    
7. Click on the **import** button.
    

If the steps are done properly you will see a **New Contract** folder under **collections**. Inside the **New Contrac**t folder are our smart contract **functions**.

Let's go ahead to test these functions. You will receive a response for each function you call, these responses can help us debug our code.

#### Testing the `startEnglishAuction` function

1. Click on the `startEnglishAuction` function.
    
2. Click on the **send** button close to the contract address input field.
    
3. Confirm the transaction in your Metamask wallet.
    
4. After doing this you will have two(2) days for the auction bid.
    

#### Testing the `bidAmount` function

1. Click on the `bidAmount` function.
    
2. Enter an amount higher than the auction starting bid (in CELO) in the input field beside the **send** button.
    
3. Click on the **send** button close to the contract address input field.
    
4. Confirm the transaction in your Metamask wallet.
    

#### Testing The `auctionHighestBidder` function

1. Click on the `auctionHighestBidder` function.
    

1. Click on the **send** button close to the contract address input field to get the auction's highest bidder.
    
2. Confirm the transaction in your Metamask wallet.
    

You can continually switch wallet accounts to make a bid higher the the previous bids.

#### Testing The `withdrawBids` function

Accounts that have been outbid can withdraw their bids with the `withdrawBids`function. Test this by following the steps below:

1. Click on the `withdrawBids` function.
    

1. Click on the **send** button close to the contract address input field to end the auction.
    
2. Confirm the transaction in your Metamask wallet
    

#### Testing The `endAuction` function

After 2 days you can end the auction by following the steps below:

1. Click on the `endAuction` function.
    

1. Click on the **send** button close to the contract address input field to end the auction.
    
2. Confirm the transaction in your Metamask wallet
    

Feel free to try out other functions if you'd like.

## Conclusion

This tutorial covered the basics of building an English auction smart contract. You can improve the smart contract by adding other functionalities and building the front-end dApp.

Happy coding!
