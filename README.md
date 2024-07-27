# ✨ So you want to run an audit

This `README.md` contains a set of checklists for our audit collaboration.

Your audit will use two repos: 
- **an _audit_ repo** (this one), which is used for scoping your audit and for providing information to wardens
- **a _findings_ repo**, where issues are submitted (shared with you after the audit) 

Ultimately, when we launch the audit, this repo will be made public and will contain the smart contracts to be reviewed and all the information needed for audit participants. The findings repo will be made public after the audit report is published and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the audit sponsor (⭐️)**.

---

# ⭐️ Sponsor: Repo checklist

- [ ] Modify the [Overview](#overview) section of this `README.md` file. Describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the auditors should keep in mind when reviewing. (Here are two well-constructed examples: [Ajna Protocol](https://github.com/code-423n4/2023-05-ajna) and [Maia DAO Ecosystem](https://github.com/code-423n4/2023-05-maia))
- [ ] Review the Gas award pool amount, if applicable. This can be adjusted up or down, based on your preference - just flag it for Code4rena staff so we can update the pool totals across all comms channels.
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.

## ⭐️ Sponsor: Final touches
- [ ] Review and confirm the pull request created by the Scout (technical reviewer) who was assigned to your contest. *Note: any files not listed as "in scope" will be considered out of scope for the purposes of judging, even if the file will be part of the deployed contracts.*
- [ ] Check that images and other files used in this README have been uploaded to the repo as a file and then linked in the README using absolute path (e.g. `https://github.com/code-423n4/yourrepo-url/filepath.png`)
- [ ] Ensure that *all* links and image/file paths in this README use absolute paths, not relative paths
- [ ] Check that all README information is in markdown format (HTML does not render on Code4rena.com)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Reserve audit details
- Total Prize Pool: $187,500 in USDC
  - HM awards: $120,000 in USDC
  - QA awards: $5,000 in USDC
  - Judge awards: $11,500 in USDC
  - Validator awards: $6,000 USDC
  - Scout awards: $1,000 in USDC
  - Mitigation Review: $14,000 USDC (*Opportunity goes to top 3 backstage wardens based on placement in this audit who RSVP.*)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-07-reserve/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts July 29, 2024 20:00 UTC
- Ends August 19, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-07-reserve/blob/main/4naly3er-report.md).

_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._

- Any acknowledged issues reported in previous audits: https://github.com/reserve-protocol/protocol/tree/4.0.0/audits
- Low-value tokens, details here: https://github.com/reserve-protocol/protocol/blob/4.0.0/docs/system-design.md#collateral-decimals-restriction
- Distributor rounding error at small DAO fees: non-negligible but deemed acceptable
- Changing distributions can send unintended amounts of funds to the veRSR DAO
- require(req.sellAmount > 1) in RevenueTrader no longer ensures sellAmount in {tok} units is greater zero
- BackingManager.forwardRevenue() will leave few revenues due to precision loss
- Distributor._setDistribution() will update the revenue distribution percentages without calling forwardRevenue() first
- Economic risks that arise from using RSR as an overcollateralization layer
- Individual RTokens should assume they can trust veRSR governance. The governing veRSR body should be assumed to be non-malicious
- Guardian censorship risk
- Any known issues / behavior documented directly in the code
- Upgrade

✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

# Overview

[ ⭐️ SPONSORS: add info here ]

## Links

- **Previous audits:**  Quite a few audits, all uploaded here: https://github.com/reserve-protocol/protocol/tree/4.0.0/audits
  - ✅ SCOUTS: If there are multiple report links, please format them in a list.
- **Documentation:** https://reserve.org/protocol/ and https://github.com/reserve-protocol/protocol/tree/4.0.0/docs
- **Website:** 🐺 CA: add a link to the sponsor's website
- **X/Twitter:** 🐺 CA: add a link to the sponsor's Twitter
- **Discord:** 🐺 CA: add a link to the sponsor's Discord

---

# Scope

[ ✅ SCOUTS: add scoping and technical details here ]

### Files in scope
- ✅ This should be completed using the `metrics.md` file
- ✅ Last row of the table should be Total: SLOC
- ✅ SCOUTS: Have the sponsor review and and confirm in text the details in the section titled "Scoping Q amp; A"

*For sponsors that don't use the scoping tool: list all files in scope in the table below (along with hyperlinks) -- and feel free to add notes to emphasize areas of focus.*

| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| [contracts/folder/sample.sol](https://github.com/code-423n4/repo-name/blob/contracts/folder/sample.sol) | 123 | This contract does XYZ | [`@openzeppelin/*`](https://openzeppelin.com/contracts/) |

### Files out of scope
✅ SCOUTS: List files/directories out of scope

## Scoping Q &amp; A

### General questions
### Are there any ERC20's in scope?: Yes

✅ SCOUTS: If the answer above 👆 is "Yes", please add the tokens below 👇 to the table. Otherwise, update the column with "None".

Any (all possible ERC20s)


### Are there any ERC777's in scope?: No

✅ SCOUTS: If the answer above 👆 is "Yes", please add the tokens below 👇 to the table. Otherwise, update the column with "None".



### Are there any ERC721's in scope?: No

✅ SCOUTS: If the answer above 👆 is "Yes", please add the tokens below 👇 to the table. Otherwise, update the column with "None".



### Are there any ERC1155's in scope?: No

✅ SCOUTS: If the answer above 👆 is "Yes", please add the tokens below 👇 to the table. Otherwise, update the column with "None".



✅ SCOUTS: Once done populating the table below, please remove all the Q/A data above.

| Question                                | Answer                       |
| --------------------------------------- | ---------------------------- |
| ERC20 used by the protocol              |       🖊️             |
| Test coverage                           | ✅ SCOUTS: Please populate this after running the test coverage command                          |
| ERC721 used  by the protocol            |            🖊️              |
| ERC777 used by the protocol             |           🖊️                |
| ERC1155 used by the protocol            |              🖊️            |
| Chains the protocol will be deployed on | Ethereum,Arbitrum,Base,Optimism |

### ERC20 token behaviors in scope

| Question                                                                                                                                                   | Answer |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| [Missing return values](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#missing-return-values)                                                      |   In scope  |
| [Fee on transfer](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#fee-on-transfer)                                                                  |  Out of scope  |
| [Balance changes outside of transfers](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#balance-modifications-outside-of-transfers-rebasingairdrops) | Out of scope    |
| [Upgradeability](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#upgradable-tokens)                                                                 |   In scope  |
| [Flash minting](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#flash-mintable-tokens)                                                              | In scope    |
| [Pausability](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#pausable-tokens)                                                                      | Out of scope    |
| [Approval race protections](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#approval-race-protections)                                              | In scope    |
| [Revert on approval to zero address](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-approval-to-zero-address)                            | Out of scope    |
| [Revert on zero value approvals](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-approvals)                                    | Out of scope    |
| [Revert on zero value transfers](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-transfers)                                    | Out of scope    |
| [Revert on transfer to the zero address](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-transfer-to-the-zero-address)                    | In scope    |
| [Revert on large approvals and/or transfers](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-large-approvals--transfers)                  | In scope    |
| [Doesn't revert on failure](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#no-revert-on-failure)                                                   |  In scope   |
| [Multiple token addresses](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#revert-on-zero-value-transfers)                                          | Out of scope    |
| [Low decimals ( < 6)](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#low-decimals)                                                                 |   In scope  |
| [High decimals ( > 18)](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#high-decimals)                                                              | In scope    |
| [Blocklists](https://github.com/d-xo/weird-erc20?tab=readme-ov-file#tokens-with-blocklists)                                                                | In scope    |

### External integrations (e.g., Uniswap) behavior in scope:


| Question                                                  | Answer |
| --------------------------------------------------------- | ------ |
| Enabling/disabling fees (e.g. Blur disables/enables fees) | Yes   |
| Pausability (e.g. Uniswap pool gets paused)               |  Yes   |
| Upgradeability (e.g. Uniswap gets upgraded)               |   Yes  |


### EIP compliance checklist
RToken.sol and StRSR.sol should comply with ERC20 standard

✅ SCOUTS: Please format the response above 👆 using the template below👇

| Question                                | Answer                       |
| --------------------------------------- | ---------------------------- |
| src/Token.sol                           | ERC20, ERC721                |
| src/NFT.sol                             | ERC721                       |


# Additional context

## Main invariants

Any invariants that hold within a contract are documented within the contract itself. 

✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

## Attack ideas (where to focus for bugs)
Would prefer not to guide wardens to particular areas. The protocol has _mostly_ received differential audits that examine the impact of the marginal change. Our intent with this audit is to not bias the auditors and allow them to survey the protocol generally, in order to look for issues that may have been missed along the way via the differential structure.

That said, some particular areas our attention has recently been on:
- Impact of oracleErrors on recollateralization
- Addition of issuance premium to prevent toxic issuance during partial de-pegs of collateral
- Stability of individual collateral markets as it relates to collateral plugin configuration
- ...not exhaustive

✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

## All trusted roles in the protocol

- Issuance pauser
- Trading pauser
- Short freeze
- Long freeze
- Owner
- Guardian (ie timelock canceller)
- RoleRegistry.owner
- RoleRegistry.emergencyCouncil

✅ SCOUTS: Please format the response above 👆 using the template below👇

| Role                                | Description                       |
| --------------------------------------- | ---------------------------- |
| Owner                          | Has superpowers                |
| Administrator                             | Can change fees                       |

## Describe any novel or unique curve logic or mathematical models implemented in the contracts:

n/a

RecollateralizationLib implements a bespoke recollateralization algorithm that tends to have lots of complexity in it and deserves special attention. 

Otherwise the math everywhere else in the protocol is fairly straightforward. 

✅ SCOUTS: Please format the response above 👆 so its not a wall of text and its readable.

## Running tests

See: https://github.com/reserve-protocol/protocol/blob/4.0.0/docs/dev-env.md

✅ SCOUTS: Please format the response above 👆 using the template below👇

```bash
git clone https://github.com/code-423n4/2023-08-arbitrum
git submodule update --init --recursive
cd governance
foundryup
make install
make build
make sc-election-test
```
To run code coverage
```bash
make coverage
```
To run gas benchmarks
```bash
make gas
```

✅ SCOUTS: Add a screenshot of your terminal showing the gas report
✅ SCOUTS: Add a screenshot of your terminal showing the test coverage

## Miscellaneous
Employees of Reserve Protocol and employees' family members are ineligible to participate in this audit.




# Scope

*See [scope.txt](https://github.com/code-423n4/2024-07-reserve/blob/main/scope.txt)*

### Files in scope


| File   | Logic Contracts | Interfaces | nSLOC | Purpose | Libraries used |
| ------ | --------------- | ---------- | ----- | -----   | ------------ |
| /contracts/libraries/Allowance.sol | 1| 1 | 18 | ||
| /contracts/libraries/Array.sol | 1| **** | 20 | |@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/libraries/Fixed.sol | 1| **** | 342 | ||
| /contracts/libraries/Permit.sol | 1| **** | 23 | |@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol|
| /contracts/libraries/String.sol | 1| **** | 15 | ||
| /contracts/libraries/Throttle.sol | 1| **** | 40 | ||
| /contracts/mixins/Auth.sol | 1| **** | 91 | |@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol|
| /contracts/mixins/ComponentRegistry.sol | 1| **** | 80 | |@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol<br>@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/mixins/Versioned.sol | 1| **** | 8 | ||
| /contracts/p1/AssetRegistry.sol | 1| **** | 153 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol|
| /contracts/p1/BackingManager.sol | 1| **** | 170 | |@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/p1/BasketHandler.sol | 1| **** | 376 | |@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/utils/structs/EnumerableMap.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol|
| /contracts/p1/Broker.sol | 1| **** | 179 | |@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@openzeppelin/contracts/proxy/Clones.sol|
| /contracts/p1/Deployer.sol | 1| **** | 189 | |@openzeppelin/contracts/proxy/Clones.sol<br>@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol|
| /contracts/p1/Distributor.sol | 1| **** | 168 | |@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol|
| /contracts/p1/Furnace.sol | 1| **** | 35 | ||
| /contracts/p1/Main.sol | 1| **** | 98 | |@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol<br>@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/p1/RToken.sol | 1| **** | 229 | |@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol|
| /contracts/p1/RevenueTrader.sol | 1| **** | 115 | |@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/p1/StRSR.sol | 1| **** | 433 | |@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol<br>@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/p1/StRSRVotes.sol | 1| **** | 173 | |@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol|
| /contracts/p1/mixins/BasketLib.sol | 1| **** | 159 | |@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/utils/structs/EnumerableSet.sol<br>@openzeppelin/contracts/utils/structs/EnumerableMap.sol|
| /contracts/p1/mixins/Component.sol | 1| **** | 44 | |@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol<br>@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol|
| /contracts/p1/mixins/RecollateralizationLib.sol | 1| **** | 163 | |@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/p1/mixins/RewardableLib.sol | 1| **** | 15 | |@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol<br>@openzeppelin/contracts/utils/Address.sol|
| /contracts/p1/mixins/TradeLib.sol | 1| **** | 78 | |@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/p1/mixins/Trading.sol | 1| **** | 65 | |@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol<br>@openzeppelin/contracts/token/ERC20/IERC20.sol|
| /contracts/plugins/governance/Governance.sol | 1| **** | 100 | |@openzeppelin/contracts/governance/Governor.sol<br>@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol<br>@openzeppelin/contracts/governance/extensions/GovernorSettings.sol<br>@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol<br>@openzeppelin/contracts/governance/extensions/GovernorVotes.sol<br>@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol|
| /contracts/plugins/trading/DutchTrade.sol | 1| 1 | 164 | |@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol|
| /contracts/plugins/trading/GnosisTrade.sol | 1| **** | 110 | |@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@openzeppelin/contracts/utils/math/Math.sol<br>@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol|
| /contracts/registry/AssetPluginRegistry.sol | 1| **** | 85 | |@openzeppelin/contracts/access/Ownable.sol|
| /contracts/registry/DAOFeeRegistry.sol | 1| **** | 67 | ||
| /contracts/registry/RoleRegistry.sol | 1| **** | 17 | |@openzeppelin/contracts/access/AccessControlEnumerable.sol|
| /contracts/registry/VersionRegistry.sol | 1| **** | 57 | ||
| **Totals** | **34** | **2** | **4079** | | |

### Files out of scope

*See [out_of_scope.txt](https://github.com/code-423n4/2024-07-reserve/blob/main/out_of_scope.txt)*

| File         |
| ------------ |
| ./contracts/facade/DeployerRegistry.sol |
| ./contracts/facade/Facade.sol |
| ./contracts/facade/FacadeMonitor.sol |
| ./contracts/facade/FacadeTest.sol |
| ./contracts/facade/FacadeWrite.sol |
| ./contracts/facade/facets/ActFacet.sol |
| ./contracts/facade/facets/MaxIssuableFacet.sol |
| ./contracts/facade/facets/ReadFacet.sol |
| ./contracts/facade/lib/FacadeWriteLib.sol |
| ./contracts/interfaces/IAsset.sol |
| ./contracts/interfaces/IAssetRegistry.sol |
| ./contracts/interfaces/IBackingManager.sol |
| ./contracts/interfaces/IBasketHandler.sol |
| ./contracts/interfaces/IBroker.sol |
| ./contracts/interfaces/IComponent.sol |
| ./contracts/interfaces/IDeployer.sol |
| ./contracts/interfaces/IDeployerRegistry.sol |
| ./contracts/interfaces/IDistributor.sol |
| ./contracts/interfaces/IFacade.sol |
| ./contracts/interfaces/IFacadeMonitor.sol |
| ./contracts/interfaces/IFacadeTest.sol |
| ./contracts/interfaces/IFacadeWrite.sol |
| ./contracts/interfaces/IFurnace.sol |
| ./contracts/interfaces/IGnosis.sol |
| ./contracts/interfaces/IMain.sol |
| ./contracts/interfaces/IRToken.sol |
| ./contracts/interfaces/IRTokenOracle.sol |
| ./contracts/interfaces/IRevenueTrader.sol |
| ./contracts/interfaces/IRewardable.sol |
| ./contracts/interfaces/IStRSR.sol |
| ./contracts/interfaces/IStRSRVotes.sol |
| ./contracts/interfaces/ITrade.sol |
| ./contracts/interfaces/ITrading.sol |
| ./contracts/interfaces/IVersioned.sol |
| ./contracts/libraries/test/ArrayCallerMock.sol |
| ./contracts/libraries/test/FixedCallerMock.sol |
| ./contracts/libraries/test/StringCallerMock.sol |
| ./contracts/mocks/AssetPluginRegistryMock.sol |
| ./contracts/mocks/DeployerMock.sol |
| ./contracts/p0/AssetRegistry.sol |
| ./contracts/p0/BackingManager.sol |
| ./contracts/p0/BasketHandler.sol |
| ./contracts/p0/Broker.sol |
| ./contracts/p0/Deployer.sol |
| ./contracts/p0/Distributor.sol |
| ./contracts/p0/Furnace.sol |
| ./contracts/p0/Main.sol |
| ./contracts/p0/RToken.sol |
| ./contracts/p0/RevenueTrader.sol |
| ./contracts/p0/StRSR.sol |
| ./contracts/p0/mixins/Component.sol |
| ./contracts/p0/mixins/Rewardable.sol |
| ./contracts/p0/mixins/Trading.sol |
| ./contracts/p0/mixins/TradingLib.sol |
| ./contracts/plugins/assets/AppreciatingFiatCollateral.sol |
| ./contracts/plugins/assets/Asset.sol |
| ./contracts/plugins/assets/ERC4626FiatCollateral.sol |
| ./contracts/plugins/assets/EURFiatCollateral.sol |
| ./contracts/plugins/assets/FiatCollateral.sol |
| ./contracts/plugins/assets/L2LSDCollateral.sol |
| ./contracts/plugins/assets/NonFiatCollateral.sol |
| ./contracts/plugins/assets/OracleErrors.sol |
| ./contracts/plugins/assets/OracleLib.sol |
| ./contracts/plugins/assets/RTokenAsset.sol |
| ./contracts/plugins/assets/SelfReferentialCollateral.sol |
| ./contracts/plugins/assets/VersionedAsset.sol |
| ./contracts/plugins/assets/aave/ATokenFiatCollateral.sol |
| ./contracts/plugins/assets/aave/IStaticATokenLM.sol |
| ./contracts/plugins/assets/aave/StaticATokenErrors.sol |
| ./contracts/plugins/assets/aave/StaticATokenLM.sol |
| ./contracts/plugins/assets/aave/vendor/ERC20.sol |
| ./contracts/plugins/assets/aave/vendor/IAToken.sol |
| ./contracts/plugins/assets/aave/vendor/IAaveIncentivesController.sol |
| ./contracts/plugins/assets/aave/vendor/RayMathNoRounding.sol |
| ./contracts/plugins/assets/aave/vendor/ReentrancyGuard.sol |
| ./contracts/plugins/assets/aave-v3/AaveV3FiatCollateral.sol |
| ./contracts/plugins/assets/aave-v3/mock/MockStaticATokenV3.sol |
| ./contracts/plugins/assets/aave-v3/vendor/ERC20.sol |
| ./contracts/plugins/assets/aave-v3/vendor/RayMathExplicitRounding.sol |
| ./contracts/plugins/assets/aave-v3/vendor/StaticATokenErrors.sol |
| ./contracts/plugins/assets/aave-v3/vendor/StaticATokenV3LM.sol |
| ./contracts/plugins/assets/aave-v3/vendor/interfaces/IAToken.sol |
| ./contracts/plugins/assets/aave-v3/vendor/interfaces/IERC4626.sol |
| ./contracts/plugins/assets/aave-v3/vendor/interfaces/IInitializableStaticATokenLM.sol |
| ./contracts/plugins/assets/aave-v3/vendor/interfaces/IStaticATokenV3LM.sol |
| ./contracts/plugins/assets/ankr/AnkrStakedEthCollateral.sol |
| ./contracts/plugins/assets/ankr/vendor/IAnkrETH.sol |
| ./contracts/plugins/assets/cbeth/CBETHCollateral.sol |
| ./contracts/plugins/assets/cbeth/CBETHCollateralL2.sol |
| ./contracts/plugins/assets/cbeth/vendor/ICBEth.sol |
| ./contracts/plugins/assets/compoundv2/CTokenFiatCollateral.sol |
| ./contracts/plugins/assets/compoundv2/CTokenNonFiatCollateral.sol |
| ./contracts/plugins/assets/compoundv2/CTokenSelfReferentialCollateral.sol |
| ./contracts/plugins/assets/compoundv2/DEPRECATED_CTokenWrapper.sol |
| ./contracts/plugins/assets/compoundv2/ICToken.sol |
| ./contracts/plugins/assets/compoundv3/CTokenV3Collateral.sol |
| ./contracts/plugins/assets/compoundv3/CometHelpers.sol |
| ./contracts/plugins/assets/compoundv3/CusdcV3Wrapper.sol |
| ./contracts/plugins/assets/compoundv3/ICusdcV3Wrapper.sol |
| ./contracts/plugins/assets/compoundv3/IWrappedERC20.sol |
| ./contracts/plugins/assets/compoundv3/WrappedERC20.sol |
| ./contracts/plugins/assets/compoundv3/vendor/CometCore.sol |
| ./contracts/plugins/assets/compoundv3/vendor/CometExtInterface.sol |
| ./contracts/plugins/assets/compoundv3/vendor/CometExtMock.sol |
| ./contracts/plugins/assets/compoundv3/vendor/CometInterface.sol |
| ./contracts/plugins/assets/compoundv3/vendor/CometMainInterface.sol |
| ./contracts/plugins/assets/compoundv3/vendor/CometStorage.sol |
| ./contracts/plugins/assets/compoundv3/vendor/IComet.sol |
| ./contracts/plugins/assets/compoundv3/vendor/ICometConfigurator.sol |
| ./contracts/plugins/assets/compoundv3/vendor/ICometProxyAdmin.sol |
| ./contracts/plugins/assets/compoundv3/vendor/ICometRewards.sol |
| ./contracts/plugins/assets/curve/CurveAppreciatingRTokenFiatCollateral.sol |
| ./contracts/plugins/assets/curve/CurveAppreciatingRTokenSelfReferentialCollateral.sol |
| ./contracts/plugins/assets/curve/CurveRecursiveCollateral.sol |
| ./contracts/plugins/assets/curve/CurveStableCollateral.sol |
| ./contracts/plugins/assets/curve/CurveStableMetapoolCollateral.sol |
| ./contracts/plugins/assets/curve/CurveStableRTokenMetapoolCollateral.sol |
| ./contracts/plugins/assets/curve/L2ConvexStableCollateral.sol |
| ./contracts/plugins/assets/curve/PoolTokens.sol |
| ./contracts/plugins/assets/curve/crv/CurveGaugeWrapper.sol |
| ./contracts/plugins/assets/curve/cvx/vendor/ConvexInterfaces.sol |
| ./contracts/plugins/assets/curve/cvx/vendor/ConvexStakingWrapper.sol |
| ./contracts/plugins/assets/curve/cvx/vendor/CvxMining.sol |
| ./contracts/plugins/assets/curve/cvx/vendor/IConvexStakingWrapper.sol |
| ./contracts/plugins/assets/curve/cvx/vendor/IRewardStaking.sol |
| ./contracts/plugins/assets/curve/stakedao/StakeDAORecursiveCollateral.sol |
| ./contracts/plugins/assets/dsr/SDaiCollateral.sol |
| ./contracts/plugins/assets/erc20/RewardableERC20.sol |
| ./contracts/plugins/assets/erc20/RewardableERC20Wrapper.sol |
| ./contracts/plugins/assets/erc20/RewardableERC4626Vault.sol |
| ./contracts/plugins/assets/frax/SFraxCollateral.sol |
| ./contracts/plugins/assets/frax-eth/SFraxEthCollateral.sol |
| ./contracts/plugins/assets/frax-eth/vendor/IfrxEthMinter.sol |
| ./contracts/plugins/assets/frax-eth/vendor/IsfrxEth.sol |
| ./contracts/plugins/assets/lido/L2LidoStakedEthCollateral.sol |
| ./contracts/plugins/assets/lido/LidoStakedEthCollateral.sol |
| ./contracts/plugins/assets/lido/vendor/ISTETH.sol |
| ./contracts/plugins/assets/lido/vendor/IWSTETH.sol |
| ./contracts/plugins/assets/meta-morpho/MetaMorphoFiatCollateral.sol |
| ./contracts/plugins/assets/meta-morpho/MetaMorphoSelfReferentialCollateral.sol |
| ./contracts/plugins/assets/morpho-aave/IMorpho.sol |
| ./contracts/plugins/assets/morpho-aave/MorphoAaveV2TokenisedDeposit.sol |
| ./contracts/plugins/assets/morpho-aave/MorphoFiatCollateral.sol |
| ./contracts/plugins/assets/morpho-aave/MorphoNonFiatCollateral.sol |
| ./contracts/plugins/assets/morpho-aave/MorphoSelfReferentialCollateral.sol |
| ./contracts/plugins/assets/morpho-aave/MorphoTokenisedDeposit.sol |
| ./contracts/plugins/assets/mountain/USDMCollateral.sol |
| ./contracts/plugins/assets/mountain/vendor/IChronicle.sol |
| ./contracts/plugins/assets/rocket-eth/RethCollateral.sol |
| ./contracts/plugins/assets/rocket-eth/vendor/IReth.sol |
| ./contracts/plugins/assets/rocket-eth/vendor/IRocketNetworkBalances.sol |
| ./contracts/plugins/assets/rocket-eth/vendor/IRocketStorage.sol |
| ./contracts/plugins/assets/stargate/DO_NOT_USE_StargatePoolETHCollateral.sol |
| ./contracts/plugins/assets/stargate/StargatePoolFiatCollateral.sol |
| ./contracts/plugins/assets/stargate/StargateRewardableWrapper.sol |
| ./contracts/plugins/assets/stargate/interfaces/IStargateLPStaking.sol |
| ./contracts/plugins/assets/stargate/interfaces/IStargatePool.sol |
| ./contracts/plugins/assets/stargate/interfaces/IStargateRouter.sol |
| ./contracts/plugins/assets/stargate/mocks/StargateLPStakingMock.sol |
| ./contracts/plugins/assets/stargate/mocks/StargatePoolMock.sol |
| ./contracts/plugins/assets/yearnv2/YearnV2CurveFiatCollateral.sol |
| ./contracts/plugins/mocks/ATokenMock.sol |
| ./contracts/plugins/mocks/ATokenNoController.sol |
| ./contracts/plugins/mocks/AaveLendingPoolMock.sol |
| ./contracts/plugins/mocks/AppreciatingMockDecimals.sol |
| ./contracts/plugins/mocks/AppreciatingMockDecimalsCollateral.sol |
| ./contracts/plugins/mocks/AssetMock.sol |
| ./contracts/plugins/mocks/BackingMgrBackCompatible.sol |
| ./contracts/plugins/mocks/BadCollateralPlugin.sol |
| ./contracts/plugins/mocks/BadERC20.sol |
| ./contracts/plugins/mocks/CTokenMock.sol |
| ./contracts/plugins/mocks/CallbackDutchTradeBidder.sol |
| ./contracts/plugins/mocks/ChainlinkMock.sol |
| ./contracts/plugins/mocks/CometMock.sol |
| ./contracts/plugins/mocks/ComptrollerMock.sol |
| ./contracts/plugins/mocks/CurveMetapoolMock.sol |
| ./contracts/plugins/mocks/CurvePoolMock.sol |
| ./contracts/plugins/mocks/CurveReentrantReceiver.sol |
| ./contracts/plugins/mocks/CusdcV3WrapperMock.sol |
| ./contracts/plugins/mocks/DutchTradeCallbackReentrantTest.sol |
| ./contracts/plugins/mocks/DutchTradeRouter.sol |
| ./contracts/plugins/mocks/EACAggregatorProxyMock.sol |
| ./contracts/plugins/mocks/ERC1271Mock.sol |
| ./contracts/plugins/mocks/ERC20Mock.sol |
| ./contracts/plugins/mocks/ERC20MockDecimals.sol |
| ./contracts/plugins/mocks/ERC20MockRewarding.sol |
| ./contracts/plugins/mocks/EasyAuction.sol |
| ./contracts/plugins/mocks/EmaPriceOracleStableSwapMock.sol |
| ./contracts/plugins/mocks/FraxAggregator.sol |
| ./contracts/plugins/mocks/GasGuzzlingFiatCollateral.sol |
| ./contracts/plugins/mocks/GnosisMock.sol |
| ./contracts/plugins/mocks/GnosisMockReentrant.sol |
| ./contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol |
| ./contracts/plugins/mocks/InvalidBrokerMock.sol |
| ./contracts/plugins/mocks/InvalidChainlinkMock.sol |
| ./contracts/plugins/mocks/InvalidFiatCollateral.sol |
| ./contracts/plugins/mocks/InvalidRefPerTokCollateral.sol |
| ./contracts/plugins/mocks/InvalidRevTraderP1Mock.sol |
| ./contracts/plugins/mocks/MakerPotMock.sol |
| ./contracts/plugins/mocks/MockMetaMorpho4626.sol |
| ./contracts/plugins/mocks/MockRoleRegistry.sol |
| ./contracts/plugins/mocks/MockableCollateral.sol |
| ./contracts/plugins/mocks/MorphoAaveV2TokenisedDepositMock.sol |
| ./contracts/plugins/mocks/NontrivialPegCollateral.sol |
| ./contracts/plugins/mocks/RTokenCollateral.sol |
| ./contracts/plugins/mocks/RevenueTraderBackComp.sol |
| ./contracts/plugins/mocks/RevertingFacetMock.sol |
| ./contracts/plugins/mocks/RewardableERC20WrapperTest.sol |
| ./contracts/plugins/mocks/RewardableERC4626VaultTest.sol |
| ./contracts/plugins/mocks/SelfdestructTransferMock.sol |
| ./contracts/plugins/mocks/SfraxEthMock.sol |
| ./contracts/plugins/mocks/USDCMock.sol |
| ./contracts/plugins/mocks/UnpricedPlugins.sol |
| ./contracts/plugins/mocks/WBTCMock.sol |
| ./contracts/plugins/mocks/WETH.sol |
| ./contracts/plugins/mocks/ZeroDecimalMock.sol |
| ./contracts/plugins/mocks/upgrades/AssetRegistryV2.sol |
| ./contracts/plugins/mocks/upgrades/BackingManagerV2.sol |
| ./contracts/plugins/mocks/upgrades/BasketHandlerV2.sol |
| ./contracts/plugins/mocks/upgrades/BrokerV2.sol |
| ./contracts/plugins/mocks/upgrades/DeployerV2.sol |
| ./contracts/plugins/mocks/upgrades/DistributorV2.sol |
| ./contracts/plugins/mocks/upgrades/FacadeMonitorV2.sol |
| ./contracts/plugins/mocks/upgrades/FurnaceV2.sol |
| ./contracts/plugins/mocks/upgrades/MainV2.sol |
| ./contracts/plugins/mocks/upgrades/RTokenV2.sol |
| ./contracts/plugins/mocks/upgrades/RevenueTraderV2.sol |
| ./contracts/plugins/mocks/upgrades/StRSRV2.sol |
| ./contracts/plugins/mocks/vendor/EasyAuction.sol |
| ./contracts/spells/3_4_0.sol |
| ./contracts/vendor/ERC20PermitUpgradeable.sol |
| ./contracts/vendor/oz/ERC4626.sol |
| ./contracts/vendor/oz/IERC4626.sol |
| ./contracts/vendor/oz/Multicall.sol |
| Totals: 234 |

