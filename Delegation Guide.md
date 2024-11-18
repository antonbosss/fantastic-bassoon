# MOR Delegation Guide
## Introduction
[A Morpheus Compute Subnet](/!KEYDOCS%20README%20FIRST!/Compute%20Providers/Compute%20Node/Subnets.md ) is a specialized Compute Provider that offers "Inference" services (LLM and Diffusion Models) to users within the Morpheus ecosystem. To create a subnet, providers must stake a minimum of 10,000 MOR. This staking requirement ensures that only dedicated providers, who have made significant investments in both hardware and MOR tokens, can participate. This reduces the risk of bad actors entering the marketplace.

Additionally, MOR staked toward each subnet increases its "rank" with the Router, enhancing the likelihood of being matched with users. This creates natural economic incentives for subnets to attract MOR and share rewards with MOR holders.

While MOR lend and borrow market is still under development, Morpheus has implemented a Delegation process that allows subnets to stake MOR on behalf of users within the protocol.

This guide outlines the delegation process using the Arbitrum Sepolia testnet. 

> [!IMPORTANT]
> - A Subnet wallet interacts with the protocol on behalf of the user.
>   
> - User funds are **NOT** transferred to the delegate’s balance.
>   
> - The delegate can **ONLY** transfer user funds within the Morpheus Compute contract, ensuring security and limiting misuse.

---

## Table of contents
1) [**Smart Contract Addresses**](#smart-contract-addresses)
3) [**Delegate Appointment**](#stake-сode-mor-rewards)
4) [**Allowance for the Compute Contract**](#check-power-factor-multiplier)
5) [**Execution of a Transaction by a Delegate**](#check-mor-rewards-stake-time)
6) [**Rules List**](#rules-list)
7) [**ABIs List**](#additional-guide-links)

--- 
## Smart Contract Addresses

#### Arbitrum Mainnet
- Morpheus-Lumerin Compute Contract: [**0xDE819AaEE474626E3f34Ef0263373357e5a6C71b**](https://arbiscan.io/address/0xDE819AaEE474626E3f34Ef0263373357e5a6C71b)
- Provider Registry: [**0x8621E6b808A3d925533446B767B7BCA6ACCb62a2**](https://arbiscan.io/address/0x8621E6b808A3d925533446B767B7BCA6ACCb62a2)
- Model Registry: [**0x2E96cEF46D2a82e63570b538EF4aB697a09a3996**](https://arbiscan.io/address/0x2E96cEF46D2a82e63570b538EF4aB697a09a3996)
- Marketplace: [**0xc371404682A2E02C3b46814261BEE615E57F48a8**](https://arbiscan.io/address/0xc371404682A2E02C3b46814261BEE615E57F48a8) 
- Session Router: [**0xAB493D93Bd9C93C7590865df82F4e09F3dF96D4C**](https://arbiscan.io/address/0xAB493D93Bd9C93C7590865df82F4e09F3dF96D4C)
- Delegate Registry: [**0x00000000000000447e69651d841bD8D104Bed493**](https://arbiscan.io/address/0x00000000000000447e69651d841bD8D104Bed493)
- MOR: [**0x092baadb7def4c3981454dd9c0a0d7ff07bcfc86**](https://arbiscan.io/address/0x092bAaDB7DEf4C3981454dD9c0A0D7FF07bCFc86)

#### Arbitrum Sepolia (testnet)
- Morpheus-Lumerin Compute Contract: [**0xb8C55cD613af947E73E262F0d3C54b7211Af16CF**](https://sepolia.arbiscan.io/address/0xb8C55cD613af947E73E262F0d3C54b7211Af16CF) 
- Provider Registry: [**0x5B4Eb8Ce68614ac9d63af035dF9Ce49cF497467F**](https://sepolia.arbiscan.io/address/0x5B4Eb8Ce68614ac9d63af035dF9Ce49cF497467F)
- Model Registry: [**0xadA08ff9E0318dFfF0D02668C2815D0e5fCc1bC0**](https://sepolia.arbiscan.io/address/0xadA08ff9E0318dFfF0D02668C2815D0e5fCc1bC0)
- Marketplace: [**0x19354CeF672bb57F1Eb9f422150e770CD9a2A3C7**](https://sepolia.arbiscan.io/address/0x19354CeF672bb57F1Eb9f422150e770CD9a2A3C7) 
- Session Router: [**0xCc48cB2DbA21A5D36C16f6f64e5B5E138EA1ba13**](https://sepolia.arbiscan.io/address/0xCc48cB2DbA21A5D36C16f6f64e5B5E138EA1ba13) 
- Delegate Registry: [**0x00000000000000447e69651d841bD8D104Bed493**](https://sepolia.arbiscan.io/address/0x00000000000000447e69651d841bD8D104Bed493)
- MOR: [**0x34a285a1b1c166420df5b6630132542923b5b27e**](https://sepolia.arbiscan.io/address/0x34a285a1b1c166420df5b6630132542923b5b27e)

Delegation testing video
https://www.loom.com/share/9e266e1893a8488f952f1667680c27e4?sid=935720fb-c946-4a45-9e92-02f073c8e2cb
https://www.loom.com/share/851d97c803f042e59769280d4367ceb6?sid=5dbc6e8b-ed8e-4af9-945a-88786a6d2fcd
https://www.loom.com/share/3693904a0d334cf1aa0a45aced9915d4?sid=3884d026-c330-41eb-998e-89e9b96d4f43


---

## Delegate Appointment
1. Open the smart contract [0x00000000000000447e69651d841bD8D104Bed493](https://sepolia.arbiscan.io/address/0x00000000000000447e69651d841bD8D104Bed493) deployed by [delegate.xyz](https://delegate.xyz/) in the desired network (the same address for all chains), 
2. Go to the section with the contract, write functions.
3. Call the `delegateContract()` function that will assign a delegate to the transaction sender.
   Parameters:
- `payable amount` (delegateContract): 0;
- `to (address)`: your delegator address;
- `contract (address)`: diamond protocol address;
- `rights (bytes32)`: see [rules list](#rules-list). If you are unsure or testing functionality, choose full rights (full protocol rights). With this parameter you can restrict the delegator's actions within the protocol, i.e., for - example, allow opening sessions, but forbid creating models.
- `enable (bool)`: true.

> [!TIP]
> For additional verification, you can call `checkDelegateForContract()`. It should return “true”. Where “from”: the address from which the delegation function was called. The rest of the parameters are the same as you specified when delegating.


## Allowance for the Compute Contract
The user needs to give permission to transfer tokens to the protocol, this step is pretty standard, you need to call `approve()` on the [MOR contract](https://sepolia.arbiscan.io/address/0x34a285a1b1c166420df5b6630132542923b5b27e), specify the number of tokens (in wei) and the protocol address. 

At this point, the configuration of the delegate is complete. Further transactions need to be executed by the delegate.


> [!TIP]
> You can check the permission by calling the `allowance()` function, where the owner will be the user's address and the spender will be the protocol address. The result should be the amount the user is willing to invest in the protocol.


## Execution of a Transaction by a Delegate
Morpheus-Lumerin Compute architecture based on proxy contracts. This means that users cannot call all protocol methods through the block explorer.   
For that we need to use:
a) console 
b) frontend 
c) remix.

To interact with the protocol, we will need an ABI. ABI can be used for both mainnet and testnet, it can be found in contracts:
- ProviderRegistry: 0x5B4Eb8Ce68614ac9d63af035dF9Ce49cF497467F
- ModelRegistry: 0xadA08ff9E0318dFfF0D02668C2815D0e5fCc1bC0
- Marketplace: 0x19354CeF672bb57F1Eb9f422150e770CD9a2A3C7 
- SessionRouter: 0xCc48cB2DbA21A5D36C16f6f64e5B5E138EA1ba13 

Depending on what you plan to do, you should choose an ABI. We will create the provider by the delegate, so we will take the ABI from the ProviderRegistry. 

1. Go to the Contract section and scroll down. Copy ABI.


2. Open https://remix.ethereum.org. Open files tab (1). Create a ProviderRegistry.abi file in the “contracts” folder (2). Paste ABI to the new file (3). Keep this file open.


3. Go to the deploy section (4). Connect your wallet with metamask (5), check the network in metamask. It will be displayed that the network is not correct (6), it's not a big deal, just close the network change window.
Double-check that the delegate address has been connected.


4. Next, enter the protocol address in the “At Address” field (1) and click this button (2). If everything was successful, you should see that the section of the deployed contracts has changed (3), our protocol and its functions have appeared.

5. Open the provider registration function, fill in all the fields. Remember that in the “provider” field you should specify the actual address of the provider, and the caller will be the delegator. That is, the one who gave delegation rights will be the provider in our case.

6. Confirm the transaction.

https://sepolia.arbiscan.io/tx/0xc2cb571edd6b721f6c2d83717ec4aa8008ce65001334c4c3a600a2cd89757133 - this is a delegate transaction, caller and provider do not match, tokens were transferred from the provider's address.

The rest of the functionality works according to a similar principle.


## Rules List 
Choose one according to needs:
- 0x0000000000000000000000000000000000000000000000000000000000000000: interaction with full protocol:
- 0x95f03c9db87c84e0b1043857b43822e60143b5fd222cbae6a58608238277e43b: interaction with providers functionality;
- 0xdb1a8f53c9efd81b585fe5c55b156a15496e6ca1c7d55f21128c594fd4b3157a: interaction with models functionality;
- 0xe0e347f99bca6ace06441ab53f851efe66ec57205ae2812242ccddb7923a8a79: interaction with marketplace functionality;
- 0xbb0b3346f3a62a3cf205ec3a488d79de48762f0ab35cbf72aaf290fde72d79f5: interaction with sessions functionality.


## ABIs List


