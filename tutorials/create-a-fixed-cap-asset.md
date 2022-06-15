# Create a Fixed-cap Asset

Create a Fixed-cap AssetFixed-cap assets are fungible tokens for which the supply is determined at the time of asset creation. No additional quantities can be generated afterwards.

## Create the Asset <a href="#create-the-asset" id="create-the-asset"></a>

### 1. Write the token smart contract <a href="#1.-write-the-token-smart-contract" id="1.-write-the-token-smart-contract"></a>

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20Capped.solgithub.comExtension of ERC20 that adds a cap to the supply of tokensThis contract is designed to be unopinionated, allowing developers to access the internal functions in ERC20 (such as [\_mint](https://docs.openzeppelin.com/contracts/3.x/api/token/erc20#ERC20-\_mint-address-uint256-)) and expose them as external functions in the way they prefer. On the other hand, [ERC20 Presets](https://docs.openzeppelin.com/contracts/3.x/erc20#Presets) (such as [ERC20PresetMinterPauser](https://docs.openzeppelin.com/contracts/3.x/api/presets#ERC20PresetMinterPauser)) are designed using opinionated patterns to provide developers with ready to use, deployable contracts.

#### Functions <a href="#functions" id="functions"></a>

_Fixed-cap Assets_

* constructor(cap)
* cap()
* \_beforeTokenTransfer(from, to, amount)

_General_

* name()
* symbol()
* decimals()
* totalSupply()
* balanceOf(account)
* transfer(recipient, amount)
* allowance(owner, spender)
* approve(spender, amount)
* transferFrom(sender, recipient, amount)
* increaseAllowance(spender, addedValue)
* decreaseAllowance(spender, subtractedValue)
* \_transfer(sender, recipient, amount)
* \_mint(account, amount)
* \_burn(account, amount)
* \_approve(owner, spender, amount)
* \_setupDecimals(decimals\_)

### 2. Compile your code into bytecode <a href="#2.-compile-your-code-into-bytecode" id="2.-compile-your-code-into-bytecode"></a>

### 3. Deploy your Fixed-cap Asset by sending your code in a transaction to the Enter network <a href="#3.-deploy-your-fixed-cap-asset-by-sending-your-code-in-a-transaction-to-the-fantom-network" id="3.-deploy-your-fixed-cap-asset-by-sending-your-code-in-a-transaction-to-the-fantom-network"></a>

### 4. Navigate to [the explorer](https://scan.entercoin.net/) to check that your token has been created <a href="#4.-navigate-to-the-explorer-to-check-that-your-token-has-been-created" id="4.-navigate-to-the-explorer-to-check-that-your-token-has-been-created"></a>
