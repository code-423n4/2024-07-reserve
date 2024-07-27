# ✨ So you want to run an audit

This `README.md` contains a set of checklists for our audit collaboration.

Your audit will use two repos: 
- **an _audit_ repo** (this one), which is used for scoping your audit and for providing information to wardens
- **a _findings_ repo**, where issues are submitted (shared with you after the audit) 

Ultimately, when we launch the audit, this repo will be made public and will contain the smart contracts to be reviewed and all the information needed for audit participants. The findings repo will be made public after the audit report is published and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the audit sponsor (⭐️)**.

---

# Audit setup

## 🐺 C4: Set up repos
- [ ] Create a new private repo named `YYYY-MM-sponsorname` using this repo as a template.
- [ ] Rename this repo to reflect audit date (if applicable)
- [ ] Rename audit H1 below
- [ ] Update pot sizes
  - [ ] Remove the "Bot race findings opt out" section if there's no bot race.
- [ ] Fill in start and end times in audit bullets below
- [ ] Add link to submission form in audit details below
- [ ] Add the information from the scoping form to the "Scoping Details" section at the bottom of this readme.
- [ ] Add matching info to the Code4rena site
- [ ] Add sponsor to this private repo with 'maintain' level access.
- [ ] Send the sponsor contact the url for this repo to follow the instructions below and add contracts here. 
- [ ] Delete this checklist.

# Repo setup

## ⭐️ Sponsor: Add code to this repo

- [ ] Create a PR to this repo with the below changes:
- [ ] Confirm that this repo is a self-contained repository with working commands that will build (at least) all in-scope contracts, and commands that will run tests producing gas reports for the relevant contracts.
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 48 business hours prior to audit start time.**
- [ ] Be prepared for a 🚨code freeze🚨 for the duration of the audit — important because it establishes a level playing field. We want to ensure everyone's looking at the same code, no matter when they look during the audit. (Note: this includes your own repo, since a PR can leak alpha to our wardens!)

## ⭐️ Sponsor: Repo checklist

- [ ] Modify the [Overview](#overview) section of this `README.md` file. Describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the auditors should keep in mind when reviewing. (Here are two well-constructed examples: [Ajna Protocol](https://github.com/code-423n4/2023-05-ajna) and [Maia DAO Ecosystem](https://github.com/code-423n4/2023-05-maia))
- [ ] Review the Gas award pool amount, if applicable. This can be adjusted up or down, based on your preference - just flag it for Code4rena staff so we can update the pool totals across all comms channels.
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] [This checklist in Notion](https://code4rena.notion.site/Key-info-for-Code4rena-sponsors-f60764c4c4574bbf8e7a6dbd72cc49b4#0cafa01e6201462e9f78677a39e09746) provides some best practices for Code4rena audit repos.

## ⭐️ Sponsor: Final touches
- [ ] Review and confirm the pull request created by the Scout (technical reviewer) who was assigned to your contest. *Note: any files not listed as "in scope" will be considered out of scope for the purposes of judging, even if the file will be part of the deployed contracts.*
- [ ] Check that images and other files used in this README have been uploaded to the repo as a file and then linked in the README using absolute path (e.g. `https://github.com/code-423n4/yourrepo-url/filepath.png`)
- [ ] Ensure that *all* links and image/file paths in this README use absolute paths, not relative paths
- [ ] Check that all README information is in markdown format (HTML does not render on Code4rena.com)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Reserve audit details
- Total Prize Pool: $187500 in USDC
  - HM awards: $120000 in USDC
  - (remove this line if there is no Analysis pool) Analysis awards: XXX XXX USDC (Notion: Analysis pool)
  - QA awards: $5000 in USDC
  - (remove this line if there is no Bot race) Bot Race awards: XXX XXX USDC (Notion: Bot Race pool)
 
  - Judge awards: $11500 in USDC
  - Validator awards: XXX XXX USDC (Notion: Triage fee - final)
  - Scout awards: $1000 in USDC
  - (this line can be removed if there is no mitigation) Mitigation Review: XXX XXX USDC (*Opportunity goes to top 3 backstage wardens based on placement in this audit who RSVP.*)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-07-reserve/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts July 29, 2024 20:00 UTC
- Ends August 19, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-07-reserve/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._
## 🐺 C4: Begin Gist paste here (and delete this line)





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

