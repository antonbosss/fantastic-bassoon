# MOR Delegation Guide
## Introduction
[A Morpheus Compute Subnet](/!KEYDOCS%20README%20FIRST!/Compute%20Providers/Compute%20Node/Subnets.md ) is a specialized Compute Provider that offers "Inference" services (LLM and Diffusion Models) to users within the Morpheus ecosystem. To create a subnet, providers must stake a minimum of 10,000 MOR. This staking requirement ensures that only dedicated providers, who have made significant investments in both hardware and MOR tokens, can participate. This reduces the risk of bad actors entering the marketplace.

Additionally, MOR staked toward each subnet increases its [Rank](https://docs.google.com/document/d/1jQPcGcjpO-vu9PTiMKyEqXOwRyuE_gzmQq56irXd3Zc/edit?tab=t.0#heading=h.m15gchuzoli8) with the Router, enhancing the likelihood of being matched with users. This creates natural economic incentives for subnets to attract MOR and share rewards with MOR holders.

While MOR lend and borrow market is still under development, Morpheus has implemented a Delegation process that allows subnets (**Delegatee**) to stake MOR on behalf of users (**Delegator**) within the protocol.

This guide outlines the delegation process using the Arbitrum Sepolia testnet. 

> [!IMPORTANT]
> - **A Subnet (Delegatee) wallet interacts with the protocol on behalf of the user (Delegator)**.
>   
> - **Delegator funds are **NOT** transferred to the Delegatee’s balance.**
>   
> - **The Delegatee can **ONLY** transfer Delegator funds within the Morpheus Compute contract, ensuring security and limiting misuse.**

---

## Table of contents
1) [**Smart Contract Addresses**](#smart-contract-addresses)
3) [**Delegate Contract**](#delegate-contract)
4) [**Compute Contract Allowance**](#compute-contract-allowance)
5) [**Delegatee Transaction Execution**](#delegatee-transaction-execution)
6) [**Rules List**](#rules-list)

---

## Delegation Video Guide
The video guide contains all the steps described in textbelow
- [**Part 1**](https://www.loom.com/share/9e266e1893a8488f952f1667680c27e4?sid=935720fb-c946-4a45-9e92-02f073c8e2cb) 
- [**Part 2**](https://www.loom.com/share/851d97c803f042e59769280d4367ceb6?sid=5dbc6e8b-ed8e-4af9-945a-88786a6d2fcd) 
- [**Part 3**](https://www.loom.com/share/3693904a0d334cf1aa0a45aced9915d4?sid=3884d026-c330-41eb-998e-89e9b96d4f43) 

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

---

## Delegate Contract
This step performs by the Delegator and will assign a contract the Delegatee can interact with.
The steps are: 
1. Go to the [Delegate Registry smart contract](https://sepolia.arbiscan.io/address/0x00000000000000447e69651d841bD8D104Bed493) deployed by [delegate.xyz](https://delegate.xyz/) in the desired network (the same address for all chains) and connect your wallet.
2. Open the **"Contract"** tab, then select the **"Write Contract"** tab.
3. Call the `delegateContract()` and input the following parameters:
- `payable amount`: 0;
- `to (address)`: Delegatee address (the address you want to delegate rights to);
- `contract (address)`: Compute Contract address (for testnet it's 0xDE819AaEE474626E3f34Ef0263373357e5a6C71b);
- `rights (bytes32)`: `0x0000000000000000000000000000000000000000000000000000000000000000` (full protocol rights);
  
This parameter restricts the Delegatee's actions within the protocol, for example, allow opening sessions, but forbid creating models. Here is the [list](#rules-list) of available right options.

- `enable (bool)`: true.
4. Click **"Write"** and confirm the transaction in your wallet.

<img src="https://github.com/user-attachments/assets/d53cf0cc-3b5a-4247-a00e-3f2aacd2a90c" width=80% height=80%> 

> [!TIP]
> To verificate the operation, you can call `checkDelegateForContract()`. It should return “true”. Where “from”: Delegator's address, i.e. the address from which the delegation function was called. The rest of the parameters are the same as above.

---

## Compute Contract Allowance
As the next step, the Delegator needs to give a permission to transfer MOR to the Compute contract. For this:
1. Go to the [MOR contract](https://sepolia.arbiscan.io/address/0x34a285a1b1c166420df5b6630132542923b5b27e) and connect your wallet.
2. Open the **"Contract"** tab, then select the **"Write Contract"** tab.
3. Call the `approve()` function and input the following parameters:
- `spender (address)`: the Compute contract address;
- `amount (uint256)`: MOR amount that's allowed to be transferred to the Compute contract (in [wei](https://etherscan.io/unitconverter)).
4. Click **"Write"** and confirm the transaction in your wallet.

<img src="https://github.com/user-attachments/assets/4b4ce221-560e-45fe-8ac4-dadc168a95b2" width=70% height=70%>

> [!TIP]
> You can check the permission by calling the `allowance()` function, where the `owner` will be the user's (Delegator) address and the `spender` will be the Compute contract address. The result will reflect the amount the Delegator allow Delegatee to transfer to the contract.

---

## Delegatee Transaction Execution
Starting this step, further transactions need to be executed by the Delegatee. 

Morpheus-Lumerin Compute architecture based on proxy contracts. This means that users cannot call all protocol methods through the block explorer.   
For that we need to use:
a) console; 
b) frontend; 
c) [remix](https://remix.ethereum.org/).

To interact with the protocol, Delegatee needs an ABI (application binary interface), that can be found in contracts:
- Provider Registry: [**0x5B4Eb8Ce68614ac9d63af035dF9Ce49cF497467F**](https://sepolia.arbiscan.io/address/0x5B4Eb8Ce68614ac9d63af035dF9Ce49cF497467F)
- Model Registry: [**0xadA08ff9E0318dFfF0D02668C2815D0e5fCc1bC0**](https://sepolia.arbiscan.io/address/0xadA08ff9E0318dFfF0D02668C2815D0e5fCc1bC0)
- Marketplace: [**0x19354CeF672bb57F1Eb9f422150e770CD9a2A3C7**](https://sepolia.arbiscan.io/address/0x19354CeF672bb57F1Eb9f422150e770CD9a2A3C7) 
- Session Router: [**0xCc48cB2DbA21A5D36C16f6f64e5B5E138EA1ba13**](https://sepolia.arbiscan.io/address/0xCc48cB2DbA21A5D36C16f6f64e5B5E138EA1ba13) 

To find the ABI you need to:
1. Go to the one of the contracts listed above (depending on the operation).
2. Open **"Contract"** tab and scroll down until you see **"Contract ABI**" section.
3. Click **"Copy ABI"** button.

<img src="https://github.com/user-attachments/assets/5f251981-c0e5-4cd4-b3bb-f0df63f7402e" width=70% height=70%>

For showcase purpose, Delegatee will create the provider with Delegator's MOR. For this we need to copy the ABI from the `ProviderRegistry`. 

1. Copy ABI from the `ProviderRegistry` contract.
2. Open https://remix.ethereum.org and open **/contract** folder.
3. Create `ProviderRegistry.abi` file and paste ABI to it. Keep the file open.

<img src="https://github.com/user-attachments/assets/94433e7b-bc92-4c31-a74f-2284a95cf96d" width=100% height=100%>

4. Go to the **"Deploy & run transaction"** section and connect your web3 wallet.
5. Double check that correct Delegatee wallet is connected on the correct chain.

<img src="https://github.com/user-attachments/assets/32e1c8dc-59b7-44ab-b4f8-96749b70d8c6" width=55% height=55%>

6. Enter the Compute contract address in the **“At Address”** field and click the button.  
If everything has done correctly you should see should see Compute contract and its functions under **"Deployed Contracts"** section.

<img src="https://github.com/user-attachments/assets/06886fd4-40a7-4e8f-8126-37cdbb47a77c" width=50% height=50%>

7. Open the provider registration function and and input the following parameters:
- `provider_`: Delegator's address (the one that gave delegation rights);
- `amount_`: provider registration price in wei, but no more than you approved at [Compute Contract Allowance](#compute-contract-allowance) step.
- `endpoint_`: AAA.

<img src="https://github.com/user-attachments/assets/6046d1ac-c8a8-4856-88ca-6187d097343b" width=50% height=50%>

8. Click **"transact"** and confirm the transaction in your wallet.

Here is the example transaction where caller and provider are different addresses and MOR were transferred from the Delegator's address.  
https://sepolia.arbiscan.io/tx/0x9ab24642a74bfe27ce905f569538c5cb1c428add55fd04c0469f959625742191

The rest of the functionality works according to a similar principle.

---

## Rules List 
Choose one according to needs:
- 0x0000000000000000000000000000000000000000000000000000000000000000: interaction with full protocol:
- 0x95f03c9db87c84e0b1043857b43822e60143b5fd222cbae6a58608238277e43b: interaction with providers functionality;
- 0xdb1a8f53c9efd81b585fe5c55b156a15496e6ca1c7d55f21128c594fd4b3157a: interaction with models functionality;
- 0xe0e347f99bca6ace06441ab53f851efe66ec57205ae2812242ccddb7923a8a79: interaction with marketplace functionality;
- 0xbb0b3346f3a62a3cf205ec3a488d79de48762f0ab35cbf72aaf290fde72d79f5: interaction with sessions functionality.


