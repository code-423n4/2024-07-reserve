# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Don't use `_msgSender()` if not supporting EIP-2771 | 36 |
| [GAS-2](#GAS-2) | `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings) | 36 |
| [GAS-3](#GAS-3) | Use assembly to check for `address(0)` | 81 |
| [GAS-4](#GAS-4) | `array[index] += amount` is cheaper than `array[index] = array[index] + amount` (or related variants) | 1 |
| [GAS-5](#GAS-5) | Using bools for storage incurs overhead | 12 |
| [GAS-6](#GAS-6) | Cache array length outside of loop | 30 |
| [GAS-7](#GAS-7) | State variables should be cached in stack variables rather than re-reading them from storage | 19 |
| [GAS-8](#GAS-8) | Use calldata instead of memory for function arguments that do not get mutated | 26 |
| [GAS-9](#GAS-9) | For Operations that will not overflow, you could use unchecked | 740 |
| [GAS-10](#GAS-10) | Use Custom Errors instead of Revert Strings to save Gas | 174 |
| [GAS-11](#GAS-11) | Avoid contract existence checks by using low level calls | 27 |
| [GAS-12](#GAS-12) | State variables only set in the constructor should be declared `immutable` | 10 |
| [GAS-13](#GAS-13) | Functions guaranteed to revert when called by normal users can be marked `payable` | 24 |
| [GAS-14](#GAS-14) | `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`) | 7 |
| [GAS-15](#GAS-15) | Using `private` rather than `public` for constants, saves gas | 34 |
| [GAS-16](#GAS-16) | Use shift right/left instead of division/multiplication if possible | 11 |
| [GAS-17](#GAS-17) | Splitting require() statements that use && saves gas | 16 |
| [GAS-18](#GAS-18) | Use of `this` instead of marking as `public` an `external` function | 6 |
| [GAS-19](#GAS-19) | Increments/decrements can be unchecked in for-loops | 62 |
| [GAS-20](#GAS-20) | Use != 0 instead of > 0 for unsigned integer comparison | 8 |
| [GAS-21](#GAS-21) | `internal` functions not called by the contract should be removed | 32 |
### <a name="GAS-1"></a>[GAS-1] Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support

*Instances (36)*:
```solidity
File: ./contracts/mixins/Auth.sol

77:         _grantRole(OWNER, _msgSender());

126:         _revokeRole(SHORT_FREEZER, _msgSender());

140:         longFreezes[_msgSender()] -= 1; // reverts on underflow

143:         if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

90:         if (_msgSender() == address(trade)) {

116:             _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

138:         require(_msgSender() == address(assetRegistry), "asset registry only");

162:             main.hasRole(OWNER, _msgSender()) ||

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

130:         address caller = _msgSender();

151:         require(trades[_msgSender()], "unrecognized trade contract");

152:         ITrade trade = ITrade(_msgSender());

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

123:         address caller = _msgSender();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

95:         issueTo(_msgSender(), amount);

114:         address issuer = _msgSender(); // OK to save: it can't be changed in reentrant runs

161:         redeemTo(_msgSender(), amount);

189:         address caller = _msgSender();

267:         require(amount <= balanceOf(_msgSender()), "insufficient balance");

281:         uint192 baskets = _scaleDown(_msgSender(), amount);

282:         emit Redemption(_msgSender(), recipient, amount, baskets);

357:         require(_msgSender() == address(backingManager), "not backing manager");

371:         address caller = _msgSender();

388:         address caller = _msgSender();

398:         require(_msgSender() == address(backingManager), "not backing manager");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

233:         address caller = _msgSender();

263:         address account = _msgSender();

348:         address account = _msgSender();

428:         address caller = _msgSender();

773:         _transfer(_msgSender(), to, amount);

782:         _approve(_msgSender(), spender, amount);

795:         _spendAllowance(from, _msgSender(), amount);

801:         address owner = _msgSender();

807:         address owner = _msgSender();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

163:         _delegate(_msgSender(), delegatee);

189:         address caller = _msgSender();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

57:         require(main.hasRole(OWNER, _msgSender()), "governance only");

62:         require(_msgSender() == address(main), "main only");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

### <a name="GAS-2"></a>[GAS-2] `a = a + b` is more gas effective than `a += b` for state variables (excluding arrays and mappings)
This saves **16 gas per instance.**

*Instances (36)*:
```solidity
File: ./contracts/libraries/Fixed.sol

105:     shiftLeft += 18;

515:             if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);

516:             else if (rounding == RoundingMode.CEIL) shiftDelta += FIX_ONE - 1;

584:             lo += hi * ((0 - pow2) / pow2 + 1);

598:                 if (mm != 0) result_256 += 1;

600:                 if (mm > ((c_256 - 1) / 2)) result_256 += 1;

633:         lo += hi * ((0 - pow2) / pow2 + 1);

661:         if (mm != 0) result += 1;

663:         if (mm > ((z - 1) / 2)) result += 1; // z should be z-1

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

62:             available += uint256(-amount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

298:             if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

431:                 low256 += qty.safeMul(lowP, FLOOR);

446:                     high256 += qty.safeMul(highP, CEIL);

584:                     refAmtsAll[erc20Index] += amt;

666:             nonce += 1;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Distributor.sol

153:             paidOutShares += numberOfShares;

208:             revTotals.rTokenTotal += share.rTokenDist;

209:             revTotals.rsrTotal += share.rsrDist;

221:                 revTotals.rsrTotal += uint24(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

76:         lastPayout += numPeriods;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/RToken.sol

270:             portionsSum += portions[i];

493:         basketsNeeded += amtBaskets;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

451:             seizedRSR += stakeRSR;

458:         seizedRSR += draftRSRToTake;

467:             seizedRSR += draftRSR;

472:         seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

610:             stakeRSR += payout;

613:         payoutLastPaid += numPeriods;

646:         draftRSR += rsrAmount;

733:         stakeRSR += rsrAmount;

833:         eraStakes[to] += amount;

846:         stakes[era][account] += amount;

847:         totalStakes += amount;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

371:             newPrice += targetAmts[i].mulDiv(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

175:                     deltaTop += int256(

198:                 uoaBottom += (val < ctx.minTradeVolume) ? 0 : val - ctx.minTradeVolume;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

### <a name="GAS-3"></a>[GAS-3] Use assembly to check for `address(0)`
*Saves 6 gas per instance*

*Instances (81)*:
```solidity
File: ./contracts/mixins/Auth.sol

92:         require(account != address(0), "cannot grant role to address 0");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

36:         require(address(val) != address(0), "invalid RToken address");

44:         require(address(val) != address(0), "invalid StRSR address");

52:         require(address(val) != address(0), "invalid AssetRegistry address");

60:         require(address(val) != address(0), "invalid BasketHandler address");

68:         require(address(val) != address(0), "invalid BackingManager address");

76:         require(address(val) != address(0), "invalid Distributor address");

84:         require(address(val) != address(0), "invalid RSRTrader address");

92:         require(address(val) != address(0), "invalid RTokenTrader address");

100:         require(address(val) != address(0), "invalid Furnace address");

108:         require(address(val) != address(0), "invalid Broker address");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

174:         if (address(assetPluginRegistry) != address(0)) {

222:         if (address(assetPluginRegistry) != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

550:                 if (address(b.erc20s[j]) == address(0)) continue;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

87:             address(batchTradeImplementation_) != address(0),

91:             address(dutchTradeImplementation_) != address(0),

179:         require(address(newGnosis) != address(0), "invalid Gnosis address");

188:             address(newTradeImplementation) != address(0),

210:             address(newTradeImplementation) != address(0),

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

49:             address(rsr_) != address(0) &&

50:                 address(gnosis_) != address(0) &&

51:                 address(rsrAsset_) != address(0) &&

52:                 address(implementations_.main) != address(0) &&

53:                 address(implementations_.trading.gnosisTrade) != address(0) &&

54:                 address(implementations_.trading.dutchTrade) != address(0) &&

55:                 address(implementations_.components.assetRegistry) != address(0) &&

56:                 address(implementations_.components.backingManager) != address(0) &&

57:                 address(implementations_.components.basketHandler) != address(0) &&

58:                 address(implementations_.components.broker) != address(0) &&

59:                 address(implementations_.components.distributor) != address(0) &&

60:                 address(implementations_.components.furnace) != address(0) &&

61:                 address(implementations_.components.rsrTrader) != address(0) &&

62:                 address(implementations_.components.rTokenTrader) != address(0) &&

63:                 address(implementations_.components.rToken) != address(0) &&

64:                 address(implementations_.components.stRSR) != address(0),

114:         require(owner != address(0) && owner != address(this), "invalid owner");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

178:         if (address(daoFeeRegistry) != address(0)) {

182:                 if (recipient != address(0)) {

213:         if (address(daoFeeRegistry) != address(0)) {

220:             if (feeRecipient != address(0) && feeNumerator != 0) {

241:         require(dest != address(0), "dest cannot be zero");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Main.sol

39:         require(address(rsr_) != address(0), "invalid RSR address");

62:         require(address(versionRegistry_) != address(0), "invalid registry address");

63:         require(address(versionRegistry) == address(0), "already set");

71:         require(address(registry_) != address(0), "invalid registry address");

72:         require(address(assetPluginRegistry) == address(0), "already set");

80:         require(address(feeRegistry_) != address(0), "invalid registry address");

81:         require(address(daoFeeRegistry) == address(0), "already set");

100:         require(address(versionRegistry) != address(0), "no registry");

116:         require(address(versionRegistry) != address(0), "no registry");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

33:         require(address(tokenToBuy_) != address(0), "invalid token address");

157:             require(address(trades[erc20]) == address(0), "trade open");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

912:         require(addr != address(0), "zero address");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

192:         if (delegatee == address(0) && currentDelegate == address(0)) {

195:         } else if (delegatee != address(0) && currentDelegate != delegatee) {

236:             if (src != address(0)) {

245:             if (dst != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

296:         if (address(erc20) == address(0)) return false;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

34:         require(address(main_) != address(0), "main is zero address");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

50:         if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {

357:         if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

96:         require(address(trade) != address(0), "no trade open");

126:         assert(address(trades[sell]) == address(0)); // ensure calling class has checked this

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

165:         assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);

200:         require(bidder == address(0), "bid already received");

236:         require(bidder == address(0), "bid already received");

280:         require(bidder != address(0) || block.timestamp > endTime, "auction not over");

306:         return status == TradeStatus.OPEN && (bidder != address(0) || block.timestamp > endTime);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

101:         assert(origin_ != address(0));

240:         return data.clearingPriceOrder != bytes32(0);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

35:         if (_asset == address(0)) {

41:             if (address(versionRegistry.deployments(versionHash)) == address(0)) {

63:         if (_asset == address(0)) {

69:             if (address(versionRegistry.deployments(versionHash)) == address(0)) {

91:         if (address(versionRegistry.deployments(_versionHash)) == address(0)) {

97:             if (asset == address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

36:         if (address(_roleRegistry) == address(0)) {

45:         if (feeRecipient_ == address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

28:         if (address(_roleRegistry) == address(0)) {

42:         if (address(deployer) == address(0)) {

49:         if (address(deployments[versionHash]) != address(0)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="GAS-4"></a>[GAS-4] `array[index] += amount` is cheaper than `array[index] = array[index] + amount` (or related variants)
When updating a value in an array with arithmetic, using `array[index] += amount` is cheaper than `array[index] = array[index] + amount`.

This is because you avoid an additional `mload` when the array is stored in memory, and an `sload` when the array is stored in storage.

This can be applied for any arithmetic operation including `+=`, `-=`,`/=`,`*=`,`^=`,`&=`, `%=`, `<<=`,`>>=`, and `>>>=`.

This optimization can be particularly significant if the pattern occurs during a loop.

*Saves 28 gas for a storage array, 38 for a memory array*

*Instances (1)*:
```solidity
File: ./contracts/p1/BackingManager.sol

295:             ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

### <a name="GAS-5"></a>[GAS-5] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (12)*:
```solidity
File: ./contracts/mixins/Auth.sol

45:     bool public tradingPaused;

46:     bool public issuancePaused;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

56:     bool private disabled;

89:     bool public reweightable; // immutable after init

96:     bool public enableIssuancePremium;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

49:     bool public batchTradeDisabled;

52:     mapping(address => bool) private trades;

63:     mapping(IERC20Metadata => bool) public dutchTradeDisabled;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

16:     mapping(bytes32 => mapping(address => bool)) private _isValidAsset;

17:     mapping(address => bool) public isDeprecated;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

16:     mapping(address => bool) private rTokenFeeSet;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

14:     mapping(bytes32 => bool) public isDeprecated;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="GAS-6"></a>[GAS-6] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (30)*:
```solidity
File: ./contracts/libraries/String.sol

14:         for (uint256 i = 0; i < bStr.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

289:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

294:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

251:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

260:         for (uint256 i = 0; i < erc20s.length; ++i) {

296:         for (uint256 i = 0; i < erc20s.length; ++i) {

535:         for (uint48 i = 0; i < basketNonces.length; ++i) {

547:             for (uint256 j = 0; j < b.erc20s.length; ++j) {

684:         for (uint256 i = 0; i < erc20s.length; ++i) {

713:         for (uint256 i = 0; i < b.erc20s.length; ++i) {

751:         for (uint256 i = 0; i < erc20s.length; ++i) {

769:         for (uint256 i = 0; i < erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Distributor.sol

92:         for (uint256 i = 0; i < dests.length; ++i) {

145:         for (uint256 i = 0; i < destinations.length(); ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

148:         for (uint256 i = 0; i < erc20s.length; ++i) {

214:         for (uint256 i = 0; i < erc20s.length; ++i) {

269:         for (uint256 i = 0; i < portions.length; ++i) {

297:         for (uint256 i = 0; i < erc20s.length; ++i) {

311:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

321:             for (uint256 i = 0; i < erc20s.length; ++i) {

339:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

180:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

198:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

252:             for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {

263:             for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

139:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

288:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

39:         for (uint256 i = 0; i < validForVersions.length; ++i) {

67:         for (uint256 i = 0; i < _versionHashes.length; ++i) {

95:         for (uint256 i = 0; i < _assets.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

### <a name="GAS-7"></a>[GAS-7] State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*Saves 100 gas per instance*

*Instances (19)*:
```solidity
File: ./contracts/p1/BackingManager.sol

73:         IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);

298:             if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

189:             lastCollateralized = nonce;

668:             basketHistory[nonce].setFrom(_newBasket);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Deployer.sol

203:         components.rsrTrader.init(main, rsr, params.maxTradeSlippage, params.minTradeVolume);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/RToken.sol

328:                     address(backingManager),

358:         _scaleUp(address(backingManager), baskets, totalSupply());

492:         emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

126:         bool involvesRToken = tokenToBuy == IERC20(address(rToken));

128:             if (erc20s[i] == IERC20(address(rToken))) involvesRToken = true;

136:         IAsset assetToBuy = assetRegistry.toAsset(tokenToBuy);

192:         tokenToBuy.safeApprove(address(distributor), bal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

258:         IDutchTradeCallee(bidder).dutchTradeCallback(address(buy), amountIn, data);

291:         buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}

292:         sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}

351:             uint192 _bestPrice = bestPrice; // gas savings

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

145:             sell,

148:             endTime,

206:         if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="GAS-8"></a>[GAS-8] Use calldata instead of memory for function arguments that do not get mutated
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. Each iteration of this for-loop costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly bypasses this loop. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gas-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one. 

 *Saves 60 gas per instance*

*Instances (26)*:
```solidity
File: ./contracts/p1/BackingManager.sol

273:     function tradingContext(BasketRange memory basketsHeld)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

522:         uint48[] memory basketNonces,

523:         uint192[] memory portions,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

127:         TradeRequest memory req,

128:         TradePrices memory prices

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

108:         string memory name,

109:         string memory symbol,

112:         DeploymentParams memory params

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Main.sol

34:         Components memory components,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

256:         uint48[] memory basketNonces,

257:         uint192[] memory portions,

258:         address[] memory expectedERC20sOut,

259:         uint256[] memory minAmounts

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

76:     function returnTokens(IERC20[] memory erc20s) external notTradingPausedOrFrozen {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

33:     function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

105:         address[] memory targets,

106:         uint256[] memory values,

107:         bytes[] memory calldatas,

108:         string memory description

115:         address[] memory targets,

116:         uint256[] memory values,

117:         bytes[] memory calldatas,

125:         address[] memory targets,

126:         uint256[] memory values,

127:         bytes[] memory calldatas,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

162:         TradePrices memory prices

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="GAS-9"></a>[GAS-9] For Operations that will not overflow, you could use unchecked

*Instances (740)*:
```solidity
File: ./contracts/libraries/Allowance.sol

33:         bool success; // bool success = false;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/libraries/Array.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

11:         for (uint256 i = 1; i < arrLen; ++i) {

12:             for (uint256 j = 0; j < i; ++j) {

23:         for (uint256 i = 1; i < arrLen; ++i) {

24:             if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/Fixed.sol

11:     "fixed192x18" -- a value represented by 192 bits, that makes 18 digits available to

14:     The range of values that uint192 can represent is about [-1.7e20, 1.7e20].

49: uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

51: uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.

52: uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.

53: uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)

54: uint192 constant FIX_MIN = 0; // The smallest uint192.

58:     FLOOR, // Round towards zero

59:     ROUND, // Round to the nearest int

60:     CEIL // Round away from zero

68:    Thus, all the tedious-looking double conversions like uint256(uint256 (foo))

69:    See: https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html#new-restrictions

82:     return _safeWrap(x * FIX_SCALE);

102:     if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5

103:     if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

105:     shiftLeft += 18;

107:     uint256 coeff = 10**abs(shiftLeft);

108:     uint256 shifted = (shiftLeft >= 0) ? x * coeff : _divrnd(x, coeff, rounding);

120:     if (x < uint256(type(uint256).max / FIX_SCALE_SQ)) {

121:         return _safeWrap(uint256(x * FIX_SCALE_SQ) / y);

149:     return x < 0 ? uint256(-x) : uint256(x);

160:     uint256 result = numerator / divisor;

165:         if (numerator % divisor > (divisor - 1) / 2) {

166:             result++;

170:             result++;

213:         if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192

214:         if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0

216:         uint256 coeff = uint256(10**abs(decimals));

217:         return _safeWrap(decimals >= 0 ? x * coeff : _divrnd(x, coeff, rounding));

224:         return x + y;

231:         return _safeWrap(x + y * FIX_SCALE);

238:         return x - y;

245:         return _safeWrap(uint256(x) - uint256(y * FIX_SCALE));

264:         return _safeWrap(_divrnd(uint256(x) * uint256(y), FIX_SCALE, rounding));

271:         return _safeWrap(x * y);

290:         return _safeWrap(_divrnd(uint256(x) * FIX_SCALE, y, rounding));

311:     uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

321:         uint256 x = uint256(x_) * FIX_SCALE; // x is D36

322:         uint256 result = FIX_SCALE_SQ; // result is D36

324:             if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

327:             x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

329:         return _safeWrap(result / FIX_SCALE);

333:         return _safeWrap(sqrt256(x * FIX_ONE_256)); // FLOOR

369:         uint192 diff = x <= y ? y - x : x - y;

393:         if (x == 0) return 0; // always computable, no matter what decimals is

394:         if (decimals <= -42) return (rounding == CEIL ? 1 : 0);

397:         decimals -= 18; // shift so that toUint happens at the same time.

399:         uint256 coeff = uint256(10**abs(decimals));

400:         return decimals >= 0 ? uint256(x * coeff) : uint256(_divrnd(x, coeff, rounding));

509:             uint256 rawDelta = uint256(b) * a; // {D36} = {D18} * {D18}

511:             if (rawDelta / b != a) return FIX_MAX;

515:             if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);

516:             else if (rounding == RoundingMode.CEIL) shiftDelta += FIX_ONE - 1;

534:             if (shiftDelta / FIX_ONE > FIX_MAX) return FIX_MAX;

537:             return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}

552:         uint256 raw = _divrnd(FIX_ONE_256 * a, uint256(b), rounding);

554:         return uint192(raw); // don't need _safeWrap

575:             if (mm > lo) hi -= 1;

576:             lo -= mm;

577:             uint256 pow2 = c & (0 - c);

582:             c_256 /= pow2;

583:             lo /= pow2;

584:             lo += hi * ((0 - pow2) / pow2 + 1);

586:             r *= 2 - c_256 * r;

587:             r *= 2 - c_256 * r;

588:             r *= 2 - c_256 * r;

589:             r *= 2 - c_256 * r;

590:             r *= 2 - c_256 * r;

591:             r *= 2 - c_256 * r;

592:             r *= 2 - c_256 * r;

593:             r *= 2 - c_256 * r;

594:             result_256 = lo * r;

598:                 if (mm != 0) result_256 += 1;

600:                 if (mm > ((c_256 - 1) / 2)) result_256 += 1;

628:         if (mm > lo) hi -= 1;

629:         lo -= mm;

630:         uint256 pow2 = z & (0 - z);

631:         z /= pow2;

632:         lo /= pow2;

633:         lo += hi * ((0 - pow2) / pow2 + 1);

635:         r *= 2 - z * r;

636:         r *= 2 - z * r;

637:         r *= 2 - z * r;

638:         r *= 2 - z * r;

639:         r *= 2 - z * r;

640:         r *= 2 - z * r;

641:         r *= 2 - z * r;

642:         r *= 2 - z * r;

643:         result = lo * r;

661:         if (mm != 0) result += 1;

663:         if (mm > ((z - 1) / 2)) result += 1; // z should be z-1

676:         uint256 mm = mulmod(x, y, uint256(0) - uint256(1));

677:         lo = x * y;

678:         hi = mm - lo;

679:         if (mm < lo) hi -= 1;

724:     if (xAux >= 2**128) {

728:     if (xAux >= 2**64) {

732:     if (xAux >= 2**32) {

736:     if (xAux >= 2**16) {

740:     if (xAux >= 2**8) {

744:     if (xAux >= 2**4) {

748:     if (xAux >= 2**2) {

757:         result = (result + x / result) >> 1;

758:         result = (result + x / result) >> 1;

759:         result = (result + x / result) >> 1;

760:         result = (result + x / result) >> 1;

761:         result = (result + x / result) >> 1;

762:         result = (result + x / result) >> 1;

763:         result = (result + x / result) >> 1;

766:         uint256 roundedResult = x / result;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Permit.sol

4: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/libraries/String.sol

14:         for (uint256 i = 0; i < bStr.length; i++) {

18:                 bLower[i] = bytes1(uint8(bStr[i]) + 32);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

4: import "./Fixed.sol";

6: uint48 constant ONE_HOUR = 3600; // {seconds/hour}

21:         uint256 amtRate; // {qRTok/hour} a quantity of RToken hourly; cannot be 0

22:         uint192 pctRate; // {1/hour} a fraction of RToken hourly; can be 0

29:         uint48 lastTimestamp; // {seconds}

30:         uint256 lastAvailable; // {qRTok}

46:         uint256 limit = hourlyLimit(throttle, supply); // {qRTok}

59:             available -= uint256(amount);

62:             available += uint256(-amount);

74:         uint48 delta = uint48(block.timestamp) - throttle.lastTimestamp; // {seconds}

75:         available = throttle.lastAvailable + (limit * delta) / ONE_HOUR;

88:         limit = (supply * params.pctRate) / FIX_ONE_256; // {qRTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

4: import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

5: import "../interfaces/IMain.sol";

7: uint256 constant LONG_FREEZE_CHARGES = 6; // 6 uses

9: uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month

10: uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year

38:     uint48 public unfreezeAt; // {s} uint48.max to pause indefinitely

39:     uint48 public shortFreeze; // {s} length of an initial freeze

40:     uint48 public longFreeze; // {s} length of a freeze extension

127:         freezeUntil(uint48(block.timestamp) + shortFreeze);

140:         longFreezes[_msgSender()] -= 1; // reverts on underflow

144:         freezeUntil(uint48(block.timestamp) + longFreeze);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7: import "./Auth.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/mixins/Versioned.sol

4: import "../interfaces/IVersioned.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Versioned.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

6: import "../interfaces/IAssetRegistry.sol";

7: import "../interfaces/IMain.sol";

8: import "./mixins/Component.sol";

15:     uint256 public constant GAS_FOR_BH_QTY = 100_000; // enough to call bh.quantity

16:     uint256 public constant GAS_FOR_DISABLE_BASKET = 900_000; // enough to disable basket on n=128

30:     uint48 public lastRefresh; // {s}

48:         for (uint256 i = 0; i < length; ++i) {

63:         for (uint256 i = 0; i < length; ++i) {

68:         lastRefresh = uint48(block.timestamp); // safer to do this at end than start, actually

94:             if (quantity != 0) basketHandler.disableBasket(); // not an interaction

113:             if (quantity != 0) basketHandler.disableBasket(); // not an interaction

151:         for (uint256 i = 0; i < length; ++i) {

163:         for (uint256 i = 0; i < length; ++i) {

176:             for (uint256 i = 0; i < assetLen; ++i) {

260:             gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

4: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "../interfaces/IAsset.sol";

7: import "../interfaces/IBackingManager.sol";

8: import "../interfaces/IMain.sol";

9: import "../libraries/Array.sol";

10: import "../libraries/Fixed.sol";

11: import "./mixins/Trading.sol";

12: import "./mixins/RecollateralizationLib.sol";

33:     uint48 public constant MAX_TRADING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year

34:     uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%

36:     uint48 public tradingDelay; // {s} how long to wait until resuming trading after switching

37:     uint192 public backingBuffer; // {1} how much extra backing collateral to keep

41:     mapping(TradeKind => uint48) private tradeEnd; // {s} last endTime() of an auction per kind

44:     mapping(IERC20 => uint192) private tokensOut; // {tok} token balances out in ITrades

87:         trade = super.settleTrade(sell); // nonReentrant

97:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

122:         require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");

131:         if (balance >= MAX_DISTRIBUTION * MAX_DESTINATIONS) rToken.dissolve(balance);

132:         if (basketsHeld.bottom >= rToken.basketsNeeded()) return; // return if now capitalized

161:                 if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);

166:             tradeEnd[kind] = trade.endTime(); // {s}

167:             tokensOut[sellERC20] = trade.sellAmount(); // {tok}

189:         require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");

221:         uint192 baskets = (basketsHeld.bottom.div(FIX_ONE + backingBuffer));

223:             rToken.mint(baskets - rToken.basketsNeeded());

226:         uint192 needed = rToken.basketsNeeded().mul(FIX_ONE + backingBuffer); // {BU}

236:         for (uint256 i = 0; i < length; ++i) {

247:                 uint256 tokensPerShare = delta / (totals.rTokenTotal + totals.rsrTotal);

254:                     erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);

259:                         tokensPerShare * totals.rTokenTotal

289:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

294:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

295:             ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];

298:             if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";

7: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

8: import "../interfaces/IAssetRegistry.sol";

9: import "../interfaces/IBasketHandler.sol";

10: import "../interfaces/IMain.sol";

11: import "../libraries/Array.sol";

12: import "../libraries/Fixed.sol";

13: import "./mixins/BasketLib.sol";

14: import "./mixins/Component.sol";

31:     uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight

32:     uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute

33:     uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year

51:     uint48 public nonce; // {basketNonce} A unique identifier for this basket instance

52:     uint48 public timestamp; // The timestamp when this basket was last set

69:     uint48 public warmupPeriod; // {s} how long to wait until issuance/trading after regaining SOUND

83:     EnumerableMap.Bytes32ToUintMap private _targetAmts; // targetName -> {target/BU}

89:     bool public reweightable; // immutable after init

91:     uint48 public lastCollateralized; // {basketNonce} most recent full collateralization

123:         reweightable = reweightable_; // immutable thereafter

142:         for (uint256 i = 0; i < len; ++i) refAmts[i] = basket.refAmts[basket.erc20s[i]];

146:         trackStatus(); // does NOT interact with collateral plugins or tokens

251:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

260:         for (uint256 i = 0; i < erc20s.length; ++i) {

296:         for (uint256 i = 0; i < erc20s.length; ++i) {

299:             assetRegistry.toColl(erc20s[i]); // reverts if not collateral

321:         for (uint256 i = 0; i < size; ++i) {

334:             (block.timestamp >= lastStatusTimestamp + warmupPeriod);

373:             uint192 targetPerRef = coll.targetPerRef(); // {target/ref}

424:         for (uint256 i = 0; i < len; ++i) {

431:                 low256 += qty.safeMul(lowP, FLOOR);

440:                         uint192 premium = issuancePremium(coll); // {1} always CEIL

446:                     high256 += qty.safeMul(highP, CEIL);

450:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

491:         for (uint256 i = 0; i < length; ++i) {

500:                 uint192 premium = issuancePremium(coll); // {1} always CEIL by definition

532:         uint256 len; // length of return arrays

535:         for (uint48 i = 0; i < basketNonces.length; ++i) {

547:             for (uint256 j = 0; j < b.erc20s.length; ++j) {

554:                 for (uint256 k = 0; k < len; ++k) {

567:                         if (!asset.isCollateral()) continue; // skip token if not collateral

574:                         ++len;

579:                         if (errData.length == 0) revert(); // solhint-disable-line reason-string

584:                     refAmtsAll[erc20Index] += amt;

593:         for (uint256 i = 0; i < len; ++i) {

615:         for (uint256 i = 0; i < length; ++i) {

666:             nonce += 1;

676:         for (uint256 i = 0; i < len; ++i) {

684:         for (uint256 i = 0; i < erc20s.length; ++i) {

713:         for (uint256 i = 0; i < b.erc20s.length; ++i) {

717:                 if (!asset.isCollateral()) continue; // skip token if no longer registered

728:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

751:         for (uint256 i = 0; i < erc20s.length; ++i) {

769:         for (uint256 i = 0; i < erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

5: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

6: import "@openzeppelin/contracts/proxy/Clones.sol";

7: import "../interfaces/IBroker.sol";

8: import "../interfaces/IMain.sol";

9: import "../interfaces/ITrade.sol";

10: import "../libraries/Fixed.sol";

11: import "./mixins/Component.sol";

12: import "../plugins/trading/DutchTrade.sol";

13: import "../plugins/trading/GnosisTrade.sol";

25:     uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week

28:     uint48 public constant MIN_AUCTION_LENGTH = 60; // {s} 60 seconds auction min duration

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

4: import "@openzeppelin/contracts/proxy/Clones.sol";

5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

6: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

7: import "../interfaces/IAsset.sol";

8: import "../interfaces/IAssetRegistry.sol";

9: import "../interfaces/IBackingManager.sol";

10: import "../interfaces/IBasketHandler.sol";

11: import "../interfaces/IBroker.sol";

12: import "../interfaces/IDeployer.sol";

13: import "../interfaces/IDistributor.sol";

14: import "../interfaces/IFurnace.sol";

15: import "../interfaces/IRevenueTrader.sol";

16: import "../interfaces/IRToken.sol";

17: import "../interfaces/IStRSR.sol";

18: import "../mixins/Versioned.sol";

19: import "../plugins/assets/Asset.sol";

20: import "../plugins/assets/RTokenAsset.sol";

21: import "./Main.sol";

22: import "../libraries/String.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

7: import "../interfaces/IDistributor.sol";

8: import "../interfaces/IMain.sol";

9: import "../libraries/Fixed.sol";

10: import "./mixins/Component.sol";

35:     uint8 public constant MAX_DESTINATIONS_ALLOWED = MAX_DESTINATIONS; // 100

92:         for (uint256 i = 0; i < dests.length; ++i) {

126:         bool isRSR = erc20 == rsr; // if false: isRToken

133:             if (totalShares != 0) tokensPerShare = amount / totalShares;

145:         for (uint256 i = 0; i < destinations.length(); ++i) {

152:             uint256 transferAmt = tokensPerShare * numberOfShares;

153:             paidOutShares += numberOfShares;

164:             ++numTransfers;

169:         for (uint256 i = 0; i < numTransfers; ++i) {

186:                         tokensPerShare * (totalShares - paidOutShares)

206:         for (uint256 i = 0; i < length; ++i) {

208:             revTotals.rTokenTotal += share.rTokenDist;

209:             revTotals.rsrTotal += share.rsrDist;

221:                 revTotals.rsrTotal += uint24(

222:                     (feeNumerator * uint256(revTotals.rTokenTotal + revTotals.rsrTotal)) /

223:                         (feeDenominator - feeNumerator)

266:         require(uint256(rTokenTotal) + uint256(rsrTotal) >= MAX_DISTRIBUTION, "totals too low");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

4: import "../libraries/Fixed.sol";

5: import "../interfaces/IFurnace.sol";

6: import "./mixins/Component.sol";

15:     uint192 public constant MAX_RATIO = 1e14; // {1} 0.01%

20:     uint192 public ratio; // {1} What fraction of balance to melt each period

23:     uint48 public lastPayout; // {seconds} The last time we did a payout

24:     uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout

66:         if (uint48(block.timestamp) < uint64(lastPayout + 1)) return;

69:         uint48 numPeriods = uint48((block.timestamp) - lastPayout);

76:         lastPayout += numPeriods;

77:         lastPayoutBal = rToken.balanceOf(address(this)) - amount;

85:         melt(); // cannot revert

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

8: import "../interfaces/IMain.sol";

9: import "../mixins/ComponentRegistry.sol";

10: import "../mixins/Auth.sol";

11: import "../mixins/Versioned.sol";

12: import "../registry/VersionRegistry.sol";

13: import "../registry/AssetPluginRegistry.sol";

14: import "../registry/DAOFeeRegistry.sol";

15: import "../interfaces/IBroker.sol";

53:         assetRegistry.refresh(); // runs furnace.melt()

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

5: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6: import "../interfaces/IMain.sol";

7: import "../interfaces/IRToken.sol";

8: import "../libraries/Fixed.sol";

9: import "../libraries/Throttle.sol";

10: import "../vendor/ERC20PermitUpgradeable.sol";

11: import "./mixins/Component.sol";

22:     uint256 public constant MIN_THROTTLE_RATE_AMT = 1e18; // {qRTok}

23:     uint256 public constant MAX_THROTTLE_RATE_AMT = 1e48; // {qRTok}

24:     uint192 public constant MAX_THROTTLE_PCT_AMT = 1e18; // {qRTok}

25:     uint192 public constant MIN_EXCHANGE_RATE = 1e9; // D18{BU/rTok}

26:     uint192 public constant MAX_EXCHANGE_RATE = 1e27; // D18{BU/rTok}

55:     uint192 public basketsNeeded; // D18{BU}

114:         address issuer = _msgSender(); // OK to save: it can't be changed in reentrant runs

121:         issuanceThrottle.useAvailable(supply, int256(amount)); // reverts on over-issuance

122:         redemptionThrottle.useAvailable(supply, -int256(amount)); // shouldn't revert

148:         for (uint256 i = 0; i < erc20s.length; ++i) {

199:         issuanceThrottle.useAvailable(supply, -int256(amount));

200:         redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption

214:         for (uint256 i = 0; i < erc20s.length; ++i) {

269:         for (uint256 i = 0; i < portions.length; ++i) {

270:             portionsSum += portions[i];

277:         issuanceThrottle.useAvailable(supply, -int256(amount));

278:         redemptionThrottle.useAvailable(supply, int256(amount)); // reverts on over-redemption

297:         for (uint256 i = 0; i < erc20s.length; ++i) {

303:             ); // FLOOR

311:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

321:             for (uint256 i = 0; i < erc20s.length; ++i) {

322:                 if (amounts[i] == 0) continue; // unregistered ERC20s will have 0 amount

339:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

342:             require(bal - pastBals[i] >= minAmounts[i], "redemption below minimum");

409:         uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}

410:         uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}

490:             ? amtBaskets.muluDivu(totalSupply, basketsNeeded) // {rTok} = {BU} * {qRTok} * {qRTok}

491:             : amtBaskets; // {rTok}

492:         emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);

493:         basketsNeeded += amtBaskets;

510:         amtBaskets = basketsNeeded.muluDivu(amtRToken, totalSupply()); // FLOOR

511:         emit BasketsNeededChanged(basketsNeeded, basketsNeeded - amtBaskets);

512:         basketsNeeded -= amtBaskets;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

4: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "../interfaces/IMain.sol";

7: import "../interfaces/IAssetRegistry.sol";

8: import "./mixins/Trading.sol";

9: import "./mixins/TradeLib.sol";

55:         trade = super.settleTrade(sell); // nonReentrant

62:             if (errData.length == 0) revert(); // solhint-disable-line reason-string

89:         for (uint256 i = 0; i < len; ++i) {

127:         for (uint256 i = 0; i < len; ++i) {

131:                 if (len == 1) return; // return early if tokenToBuy is only entry

142:             for (uint256 i = 0; i < len; ++i) {

145:             assetToBuy.refresh(); // invariant: can never be the RTokenAsset

149:         (uint192 buyLow, uint192 buyHigh) = assetToBuy.price(); // {UoA/tok}

153:         for (uint256 i = 0; i < len; ++i) {

161:             (uint192 sellLow, uint192 sellHigh) = assetToSell.price(); // {UoA/tok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

4: import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

7: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

8: import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";

9: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

11: import "../interfaces/IStRSR.sol";

12: import "../interfaces/IMain.sol";

13: import "../libraries/Fixed.sol";

14: import "../libraries/Permit.sol";

15: import "./mixins/Component.sol";

40:     uint48 private constant MIN_UNSTAKING_DELAY = 60 * 2; // {s} 2 minutes

41:     uint48 private constant MAX_UNSTAKING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year

42:     uint192 private constant MAX_REWARD_RATIO = 1e14; // {1} 0.01%

45:     string public name; // immutable

46:     string public symbol; // immutable

63:     mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}

64:     uint256 private totalStakes; // Total of all stakes {qStRSR}

65:     uint256 private stakeRSR; // Amount of RSR backing all stakes {qRSR}

66:     uint192 private stakeRate; // The exchange rate between stakes and RSR. D18{qStRSR/qRSR}

68:     uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}

76:     uint256 internal draftEra; // {draftEra}

80:         uint176 drafts; // Total amount of drafts that will become available // {qDrafts}

81:         uint64 availableAt; // When the last of the drafts will become available

84:     mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {qDrafts}

85:     mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index

86:     uint256 private totalDrafts; // Total of all drafts {qDrafts}

87:     uint256 private draftRSR; // Amount of RSR backing all drafts {qRSR}

88:     uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}

90:     uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}

138:     uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue

139:     uint192 public rewardRatio; // {1} The fraction of the revenue balance to handout per period

157:     uint192 private constant MAX_WITHDRAWAL_LEAK = 3e17; // {1} 30%

159:     uint192 private leaked; // {1} stake fraction that has withdrawn without a refresh

160:     uint48 private lastWithdrawRefresh; // {s} timestamp of last refresh() during withdraw()

161:     uint192 public withdrawalLeak; // {1} gov param -- % RSR that can be withdrawn without refresh

164:     uint192 private constant MAX_SAFE_STAKE_RATE = 1e6 * FIX_ONE; // 1e6   D18{qStRSR/qRSR}

165:     uint192 private constant MIN_SAFE_STAKE_RATE = uint192(1e12); // 1e-6  D18{qStRSR/qRSR}

274:         uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;

275:         uint256 rsrAmount = stakeRSR - newStakeRSR;

312:         require(endId <= queue.length, "index out-of-bounds");

313:         require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");

317:         uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;

318:         uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;

324:         uint256 newTotalDrafts = totalDrafts - draftAmount;

326:         uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;

327:         uint256 rsrAmount = draftRSR - newDraftRSR;

357:         require(endId <= queue.length, "index out-of-bounds");

364:         uint192 oldDrafts = firstId != 0 ? queue[firstId - 1].drafts : 0;

365:         uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;

371:         uint256 newTotalDrafts = totalDrafts - draftAmount;

373:         uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;

374:         uint256 rsrAmount = draftRSR - newDraftRSR;

441:         uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

442:         stakeRSR -= stakeRSRToTake;

448:             stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

451:             seizedRSR += stakeRSR;

456:         uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

457:         draftRSR -= draftRSRToTake;

458:         seizedRSR += draftRSRToTake;

463:             draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

467:             seizedRSR += draftRSR;

472:         seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

473:         rsrRewardsAtLastPayout = rsrRewards() - seizedRSR;

504:         return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

526:         while (left < right - 1) {

530:             test = (left + right) / 2; // left < test < right because left < right - 1

594:         if (block.timestamp < payoutLastPaid + 1) return;

595:         uint48 numPeriods = uint48(block.timestamp) - payoutLastPaid;

606:             uint192 payoutRatio = FIX_ONE - FixLib.powu(FIX_ONE - rewardRatio, numPeriods);

609:             payout = (payoutRatio * rsrRewardsAtLastPayout) / FIX_ONE;

610:             stakeRSR += payout;

613:         payoutLastPaid += numPeriods;

623:             : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

646:         draftRSR += rsrAmount;

648:         uint256 newTotalDrafts = (draftRate * draftRSR) / FIX_ONE;

649:         uint256 draftAmount = newTotalDrafts - totalDrafts;

656:         uint192 oldDrafts = index != 0 ? queue[index - 1].drafts : 0;

657:         uint64 lastAvailableAt = index != 0 ? queue[index - 1].availableAt : 0;

658:         availableAt = uint64(block.timestamp) + unstakingDelay;

663:         queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));

675:         era++;

687:         draftEra++;

694:         return rsr.balanceOf(address(this)) - stakeRSR - draftRSR;

701:         uint48 lastRefresh = assetRegistry.lastRefresh(); // {s}

704:         uint256 totalRSR = stakeRSR + draftRSR + rsrWithdrawal; // {qRSR}

705:         uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}

708:         leaked = lastWithdrawRefresh != lastRefresh ? withdrawal : leaked + withdrawal;

727:         uint256 newStakeRSR = stakeRSR + rsrAmount;

729:         uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;

730:         uint256 stakeAmount = newTotalStakes - totalStakes;

733:         stakeRSR += rsrAmount;

802:         _approve(owner, spender, _allowances[era][owner][spender] + addedValue);

811:             _approve(owner, spender, currentAllowance - subtractedValue);

831:             eraStakes[from] = fromBalance - amount;

833:         eraStakes[to] += amount;

844:         assert(totalStakes + amount < type(uint224).max);

846:         stakes[era][account] += amount;

847:         totalStakes += amount;

867:             eraStakes[account] = accountBalance - amount;

869:         totalStakes -= amount;

896:                 _approve(owner, spender, currentAllowance - amount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

4: import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol";

7: import "../interfaces/IStRSRVotes.sol";

8: import "./StRSR.sol";

37:     Checkpoint[] private _eras; // {era}

41:     mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}

43:     mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}

88:         return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;

140:             uint256 mid = length - MathUpgradeable.sqrt(length);

144:                 low = mid + 1;

153:                 low = mid + 1;

158:             return high == 0 ? 0 : ckpts[high - 1].val;

265:             Checkpoint memory oldCkpt = pos == 0 ? Checkpoint(0, 0) : ckpts[pos - 1];

271:                 ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);

284:         return a + b;

288:         return a - b;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

6: import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";

8: import "../../interfaces/IAssetRegistry.sol";

9: import "../../libraries/Fixed.sol";

15:     uint256 max; // Maximum number of backup collateral erc20s to use in a basket

16:     IERC20[] erc20s; // Ordered list of backup collateral ERC20s

49:     IERC20[] erc20s; // enumerated keys for refAmts

50:     mapping(IERC20 => uint192) refAmts; // {ref/BU}

72:         for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

80:         for (uint256 i = 0; i < length; ++i) {

81:             IERC20 _erc20 = other.erc20s[i]; // gas-saver

111:        - the basket configuration (config: BasketConfig)

112:        - the function (isGood: erc20 -> bool), implemented here by goodCollateral()

113:        - the function (targetPerRef: erc20 -> Fix) implemented by the Collateral plugin

120:        Let targetWeight(b, e) = b.refAmt[e] * targetPerRef(e)

131:                          then unsoundPrimeWt(tgt) / len(Backups(tgt))

140:        Else, return true and targetWeight(basket', e) == primeWt(e) + backupWt(e) for all e.

142:        ==== Higher-level desideratum ====

172:             targetNames.remove(targetNames.at(targetNames.length() - 1));

180:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

198:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

201:             IERC20 _erc20 = config.erc20s[i]; // gas-saver

202:             for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {

241:         for (uint256 i = 0; i < targetsLength; ++i) {

242:             if (totalWeights[i].lte(goodWeights[i])) continue; // Don't need any backup weight

248:             uint256 size = 0; // backup basket size

252:             for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {

253:                 if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) size++;

263:             for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {

274:                     assigned++;

325:         for (uint256 i = 0; i < len; ++i) {

331:                 contains ? amt + config.targetAmts[erc20] : config.targetAmts[erc20]

337:         for (uint256 i = 0; i < len; ++i) {

341:             if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);

357:         uint256 len = erc20s.length; // assumes erc20s.length == targetAmts.length

362:         uint192 newPrice; // {UoA/BU}

363:         for (uint256 i = 0; i < len; ++i) {

364:             ICollateral coll = assetRegistry.toColl(erc20s[i]); // reverts if unregistered

367:             (uint192 low, uint192 high) = coll.price(); // {UoA/tok}

371:             newPrice += targetAmts[i].mulDiv(

372:                 (low + high) / 2,

375:             ); // revert on overflow

380:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";

7: import "../../interfaces/IComponent.sol";

8: import "../../interfaces/IMain.sol";

9: import "../../mixins/Versioned.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "../../libraries/Fixed.sol";

6: import "./TradeLib.sol";

116:         (uint192 buPriceLow, uint192 buPriceHigh) = ctx.bh.price(false); // {UoA/BU}

119:         uint192 basketsNeeded = ctx.rToken.basketsNeeded(); // {BU}

132:         int256 deltaTop; // D18{BU} even though this is int256, it is D18

136:         uint192 uoaBottom; // {UoA} pessimistic UoA estimate of balances above basketsHeld.bottom

139:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

143:             (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/tok}

167:                     deltaTop -= int256(

168:                         uint256(low.mulDiv(anchor - ctx.bals[i], buPriceHigh, FLOOR))

175:                     deltaTop += int256(

176:                         uint256(high.safeMulDiv(ctx.bals[i] - anchor, buPriceLow, CEIL))

189:                 uint192 val = low.mul(ctx.bals[i] - anchor, FLOOR);

198:                 uoaBottom += (val < ctx.minTradeVolume) ? 0 : val - ctx.minTradeVolume;

206:             range.top = ctx.basketsHeld.top - _safeWrap(uint256(-deltaTop));

210:             if (uint256(deltaTop) + ctx.basketsHeld.top > FIX_MAX) range.top = FIX_MAX;

211:             else range.top = ctx.basketsHeld.top + _safeWrap(uint256(deltaTop));

219:             ctx.basketsHeld.bottom +

235:         CollateralStatus surplusStatus; // starts SOUND

236:         uint192 surplus; // {UoA}

237:         uint192 deficit; // {UoA}

282:         maxes.surplusStatus = CollateralStatus.IFFY; // least-desirable sell status

284:         uint256 rsrIndex = reg.erc20s.length; // invalid index, to-start

288:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

297:             uint192 needed = range.top.mul(ctx.quantities[i], CEIL); // {tok}

300:                 (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/sellTok}

302:                 if (high == 0) continue; // skip over worthless assets

308:                 CollateralStatus status; // starts SOUND

334:                 needed = range.bottom.mul(ctx.quantities[i], CEIL); // {buyTok};

337:                     uint192 amtShort = needed.minus(ctx.bals[i]); // {buyTok}

338:                     (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}

358:             (uint192 low, uint192 high) = reg.assets[rsrIndex].price(); // {UoA/RSR}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

4: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "@openzeppelin/contracts/utils/Address.sol";

7: import "../../interfaces/IAssetRegistry.sol";

8: import "../../interfaces/IBackingManager.sol";

27:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5: import "../../interfaces/IAsset.sol";

6: import "../../libraries/Fixed.sol";

11:     uint192 sellAmount; // {sellTok}

12:     uint192 buyAmount; // {buyTok}

147:         trade.sellAmount = fixMin(slippedSellAmount, trade.sellAmount); // {sellTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

4: import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6: import "../../interfaces/ITrade.sol";

7: import "../../interfaces/ITrading.sol";

8: import "../../libraries/Allowance.sol";

9: import "../../libraries/Fixed.sol";

10: import "../../vendor/oz/Multicall.sol";

11: import "./Component.sol";

12: import "./RewardableLib.sol";

22:     uint192 public constant MAX_TRADE_VOLUME = 1e29; // {UoA}

23:     uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}

33:     uint192 public maxTradeSlippage; // {%}

34:     uint192 public minTradeVolume; // {UoA}

37:     uint256 public tradesNonce; // to keep track of how many trades have been opened in total

100:         tradesOpen--;

126:         assert(address(trades[sell]) == address(0)); // ensure calling class has checked this

137:         ++tradesOpen;

138:         ++tradesNonce;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

4: import "@openzeppelin/contracts/governance/Governor.sol";

5: import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";

6: import "@openzeppelin/contracts/governance/extensions/GovernorSettings.sol";

7: import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";

8: import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";

9: import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";

10: import "../../interfaces/IStRSRVotes.sol";

12: uint256 constant ONE_DAY = 86400; // {s}

33:     uint256 public constant ONE_HUNDRED_PERCENT = 1e8; // {micro %}

36:     uint256 public constant MIN_VOTING_DELAY = 86400; // {s} ONE_DAY

41:         uint256 votingDelay_, // {s}

42:         uint256 votingPeriod_, // {s}

43:         uint256 proposalThresholdAsMicroPercent_, // e.g. 1e4 for 0.01%

44:         uint256 quorumPercent // e.g 4 for 4%

65:         super.setVotingDelay(newVotingDelay); // has onlyGovernance modifier

75:         uint256 asMicroPercent = super.proposalThreshold(); // {micro %}

78:         uint256 pastSupply = token.getPastTotalSupply(clock() - 1);

82:         return (asMicroPercent * pastSupply + (ONE_HUNDRED_PERCENT - 1)) / ONE_HUNDRED_PERCENT;

169:         bytes memory /*params*/

171:         return token.getPastVotes(account, timepoint); // {qStRSR}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

6: import "../../libraries/Fixed.sol";

7: import "../../interfaces/IAsset.sol";

8: import "../../interfaces/IBroker.sol";

9: import "../../interfaces/ITrade.sol";

10: import "../../mixins/Versioned.sol";

43: uint192 constant FIVE_PERCENT = 5e16; // {1} 0.05

44: uint192 constant TWENTY_PERCENT = 20e16; // {1} 0.2

45: uint192 constant TWENTY_FIVE_PERCENT = 25e16; // {1} 0.25

46: uint192 constant FORTY_FIVE_PERCENT = 45e16; // {1} 0.45

47: uint192 constant FIFTY_PERCENT = 50e16; // {1} 0.5

48: uint192 constant NINETY_FIVE_PERCENT = 95e16; // {1} 0.95

50: uint192 constant MAX_EXP = 6502287e18; // {1} (1000000/999999)^6502287 = ~666.6667

51: uint192 constant BASE = 999999e12; // {1} (999999/1000000)

52: uint192 constant ONE_POINT_FIVE = 150e16; // {1} 1.5

97:     BidType public bidType; // = BidType.NONE

99:     TradeStatus public status; // reentrancy protection

101:     IBroker public broker; // The Broker that cloned this contract into existence

102:     ITrading public origin; // the address that initialized the contract

107:     uint192 public sellAmount; // {sellTok}

110:     uint48 public startTime; // {s} when the dutch auction begins (one block after init()) lossy!

111:     uint48 public endTime; // {s} when the dutch auction ends

113:     uint192 public bestPrice; // {buyTok/sellTok} The best plausible price based on oracle data

114:     uint192 public worstPrice; // {buyTok/sellTok} The worst plausible price based on oracle data

168:         require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");

169:         require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");

177:         sellAmount = shiftl_toFix(sellAmount_, -int8(sell.decimals()), FLOOR); // {sellTok}

180:         uint48 _startTime = uint48(block.timestamp) + 1; // cannot fulfill in current block

181:         startTime = _startTime; // gas-saver

182:         endTime = _startTime + auctionLength;

186:             FIX_ONE - origin.maxTradeSlippage(),

190:         uint192 _bestPrice = prices.sellHigh.div(prices.buyLow, CEIL); // no additional slippage

192:         worstPrice = _worstPrice; // gas-saver

193:         bestPrice = _bestPrice; // gas-saver

204:         uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing

240:         uint192 price = _price(uint48(block.timestamp)); // enforces auction ongoing

255:         sell.safeTransfer(bidder, lot()); // {qSellTok}

257:         uint256 balanceBefore = buy.balanceOf(address(this)); // {qBuyTok}

260:             amountIn <= buy.balanceOf(address(this)) - balanceBefore,

283:             soldAmt = lot(); // {qSellTok}

285:             soldAmt = lot(); // {qSellTok}

286:             sell.safeTransfer(bidder, soldAmt); // {qSellTok}

290:         boughtAmt = buy.balanceOf(address(this)); // {qBuyTok}

291:         buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}

292:         sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}

315:         uint48 _startTime = startTime; // {s} gas savings

316:         uint48 _endTime = endTime; // {s} gas savings

326:         uint192 progression = divuu(timestamp - _startTime, _endTime - _startTime); // {1}

330:             uint192 exp = MAX_EXP.mulDiv(TWENTY_PERCENT - progression, TWENTY_PERCENT, ROUND);

341:             uint192 _bestPrice = bestPrice; // gas savings

345:                 highPrice -

346:                 (highPrice - _bestPrice).mulDiv(progression - TWENTY_PERCENT, TWENTY_FIVE_PERCENT);

351:             uint192 _bestPrice = bestPrice; // gas savings

354:                 _bestPrice -

355:                 (_bestPrice - worstPrice).mulDiv(progression - FORTY_FIVE_PERCENT, FIFTY_PERCENT);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5: import "@openzeppelin/contracts/utils/math/Math.sol";

6: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

7: import "../../libraries/Allowance.sol";

8: import "../../libraries/Fixed.sol";

9: import "../../interfaces/IBroker.sol";

10: import "../../interfaces/IGnosis.sol";

11: import "../../interfaces/ITrade.sol";

12: import "../../mixins/Versioned.sol";

29:     uint96 public constant MAX_ORDERS = 5000; // bounded to avoid going beyond block gas limit

32:     uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}

39:     IGnosis public gnosis; // Gnosis Auction contract

40:     uint256 public auctionId; // The Gnosis Auction ID returned by gnosis.initiateAuction()

41:     IBroker public broker; // The Broker that cloned this contract into existence

47:     IERC20Metadata public sell; // address of token this trade is selling

48:     IERC20Metadata public buy; // address of token this trade is buying

49:     uint256 public initBal; // {qSellTok}, this trade's balance of `sell` when init() was called

50:     uint192 public sellAmount; // {sellTok}, quantity of whole tokens being sold, != initBal

51:     uint48 public endTime; // timestamp after which this trade's auction can be settled

52:     uint192 public worstCasePrice; // D27{qBuyTok/qSellTok}, the worst price we expect to get

96:         sellAmount = shiftl_toFix(req.sellAmount, -int8(sell.decimals()), FLOOR); // {sellTok}

98:         initBal = sell.balanceOf(address(this)); // {qSellTok}

106:         endTime = uint48(block.timestamp) + batchAuctionLength;

116:                 req.sellAmount * FEE_DENOMINATOR,

117:                 FEE_DENOMINATOR + gnosis.feeNumerator(),

123:         uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd

126:             minBuyAmount / MAX_ORDERS,

210:             soldAmt = initBal - sellBal;

214:             uint256 adjustedBuyAmt = boughtAmt + 1;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

5: import { VersionRegistry } from "./VersionRegistry.sol";

6: import { RoleRegistry } from "./RoleRegistry.sol";

39:         for (uint256 i = 0; i < validForVersions.length; ++i) {

67:         for (uint256 i = 0; i < _versionHashes.length; ++i) {

95:         for (uint256 i = 0; i < _assets.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

4: import { RoleRegistry } from "./RoleRegistry.sol";

6: uint256 constant MAX_FEE_NUMERATOR = 15_00; // Max DAO Fee: 15%

13:     uint256 private defaultFeeNumerator; // 0%

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

5: import { AccessControlEnumerable } from "@openzeppelin/contracts/access/AccessControlEnumerable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

4: import { IDeployer, Implementations } from "../interfaces/IDeployer.sol";

5: import { RoleRegistry } from "./RoleRegistry.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="GAS-10"></a>[GAS-10] Use Custom Errors instead of Revert Strings to save Gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

Additionally, custom errors can be used inside and outside of contracts (including interfaces and libraries).

Source: <https://blog.soliditylang.org/2021/04/21/custom-errors/>:

> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Consider replacing **all revert strings** with custom errors in the solution, and particularly those that have multiple occurrences:

*Instances (174)*:
```solidity
File: ./contracts/libraries/Allowance.sol

28:         require(token.allowance(address(this), spender) == 0, "allowance not 0");

44:             require(token.allowance(address(this), spender) >= value, "allowance missing");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

58:             require(uint256(amount) <= available, "supply change throttled");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

92:         require(account != address(0), "cannot grant role to address 0");

199:         require(shortFreeze_ != 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

206:         require(longFreeze_ != 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");

215:         require(newUnfreezeAt > unfreezeAt, "frozen");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

36:         require(address(val) != address(0), "invalid RToken address");

44:         require(address(val) != address(0), "invalid StRSR address");

52:         require(address(val) != address(0), "invalid AssetRegistry address");

60:         require(address(val) != address(0), "invalid BasketHandler address");

68:         require(address(val) != address(0), "invalid BackingManager address");

76:         require(address(val) != address(0), "invalid Distributor address");

84:         require(address(val) != address(0), "invalid RSRTrader address");

92:         require(address(val) != address(0), "invalid RTokenTrader address");

100:         require(address(val) != address(0), "invalid Furnace address");

108:         require(address(val) != address(0), "invalid Broker address");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

91:         require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");

109:         require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");

110:         require(assets[asset.erc20()] == asset, "asset not found");

127:         require(_erc20s.contains(address(erc20)), "erc20 unregistered");

135:         require(_erc20s.contains(address(erc20)), "erc20 unregistered");

136:         require(assets[erc20].isCollateral(), "erc20 is not collateral");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

70:         require(assetRegistry.isRegistered(erc20), "erc20 unregistered");

120:         require(tradesOpen == 0, "trade open");

121:         require(basketHandler.isReady(), "basket not ready");

122:         require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");

125:         require(basketsHeld.bottom < rToken.basketsNeeded(), "already collateralized");

181:         require(ArrayLib.allUnique(erc20s), "duplicate tokens");

187:         require(tradesOpen == 0, "trade open");

188:         require(basketHandler.isReady(), "basket not ready");

189:         require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");

190:         require(basketsHeld.bottom >= rToken.basketsNeeded(), "undercollateralized");

316:         require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");

323:         require(val <= MAX_BACKING_BUFFER, "invalid backingBuffer");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

138:         require(_msgSender() == address(assetRegistry), "asset registry only");

233:         require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");

263:             require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "erc20 is not collateral");

264:             require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");

290:         require(max <= MAX_BACKUP_ERC20S && erc20s.length <= MAX_BACKUP_ERC20S, "too large");

526:         require(basketNonces.length == portions.length, "invalid lengths");

635:         require(val >= MIN_WARMUP_PERIOD && val <= MAX_WARMUP_PERIOD, "invalid warmupPeriod");

694:         require(ArrayLib.allUnique(erc20s), "contains duplicates");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

151:         require(trades[_msgSender()], "unrecognized trade contract");

171:             revert("unrecognized trade kind");

179:         require(address(newGnosis) != address(0), "invalid Gnosis address");

244:         require(!batchTradeDisabled, "batch auctions disabled");

245:         require(batchAuctionLength != 0, "batch auctions not enabled");

277:         require(dutchAuctionLength != 0, "dutch auctions not enabled");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

114:         require(owner != address(0) && owner != address(this), "invalid owner");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

85:         require(dests.length == shares.length, "array length mismatch");

124:         require(caller == rsrTrader || caller == rTokenTrader, "RevenueTraders only");

125:         require(erc20 == rsr || erc20 == rToken, "RSR or RToken");

134:             require(tokensPerShare != 0, "nothing to distribute");

241:         require(dest != address(0), "dest cannot be zero");

246:         require(dest != address(main.daoFeeRegistry()), "destination cannot be daoFeeRegistry");

247:         if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");

248:         if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");

249:         require(share.rsrDist <= MAX_DISTRIBUTION, "RSR distribution too high");

250:         require(share.rTokenDist <= MAX_DISTRIBUTION, "RToken distribution too high");

256:             require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");

266:         require(uint256(rTokenTotal) + uint256(rsrTotal) >= MAX_DISTRIBUTION, "totals too low");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

84:         require(ratio_ <= MAX_RATIO, "invalid ratio");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

39:         require(address(rsr_) != address(0), "invalid RSR address");

62:         require(address(versionRegistry_) != address(0), "invalid registry address");

63:         require(address(versionRegistry) == address(0), "already set");

71:         require(address(registry_) != address(0), "invalid registry address");

72:         require(address(assetPluginRegistry) == address(0), "already set");

80:         require(address(feeRegistry_) != address(0), "invalid registry address");

81:         require(address(daoFeeRegistry) == address(0), "already set");

100:         require(address(versionRegistry) != address(0), "no registry");

101:         require(!versionRegistry.isDeprecated(versionHash), "version deprecated");

108:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");

116:         require(address(versionRegistry) != address(0), "no registry");

117:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");

154:         require(msg.sender == address(this), "not self");

162:         require(success, "upgrade failed");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

69:         require(bytes(name_).length != 0, "name empty");

70:         require(bytes(symbol_).length != 0, "symbol empty");

71:         require(bytes(mandate_).length != 0, "mandate empty");

106:         require(amount != 0, "Cannot issue zero");

117:         require(basketHandler.isReady(), "basket not ready");

191:         require(amount != 0, "Cannot redeem zero");

192:         require(amount <= balanceOf(caller), "insufficient balance");

193:         require(basketHandler.fullyCollateralized(), "partial redemption; use redeemCustom");

266:         require(amount != 0, "Cannot redeem zero");

267:         require(amount <= balanceOf(_msgSender()), "insufficient balance");

272:         require(portionsSum == FIX_ONE, "portions do not add up to FIX_ONE");

333:             if (allZero) revert("empty redemption");

342:             require(bal - pastBals[i] >= minAmounts[i], "redemption below minimum");

357:         require(_msgSender() == address(backingManager), "not backing manager");

372:         require(caller == address(furnace), "furnace only");

389:         require(caller == address(backingManager), "not backing manager");

398:         require(_msgSender() == address(backingManager), "not backing manager");

404:         require(supply != 0, "0 supply");

413:         require(low >= MIN_EXCHANGE_RATE && high <= MAX_EXCHANGE_RATE, "BU rate out of range");

419:         require(assetRegistry.isRegistered(erc20), "erc20 unregistered");

452:         require(params.amtRate >= MIN_THROTTLE_RATE_AMT, "issuance amtRate too small");

453:         require(params.amtRate <= MAX_THROTTLE_RATE_AMT, "issuance amtRate too big");

454:         require(params.pctRate <= MAX_THROTTLE_PCT_AMT, "issuance pctRate too big");

463:         require(params.amtRate >= MIN_THROTTLE_RATE_AMT, "redemption amtRate too small");

464:         require(params.amtRate <= MAX_THROTTLE_RATE_AMT, "redemption amtRate too big");

465:         require(params.pctRate <= MAX_THROTTLE_PCT_AMT, "redemption pctRate too big");

535:         require(to != address(this), "RToken transfer to self");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

33:         require(address(tokenToBuy_) != address(0), "invalid token address");

79:             require(revTotals.rsrTotal == 0, "rsrTotal > 0");

81:             require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");

84:             revert("invalid tokenToBuy");

90:             require(assetRegistry.isRegistered(erc20s[i]), "unregistered erc20");

115:         require(len != 0, "empty erc20s list");

116:         require(len == kinds.length, "length mismatch");

150:         require(buyHigh != 0 && buyHigh != FIX_MAX, "buy asset price unknown");

157:             require(address(trades[erc20]) == address(0), "trade open");

158:             require(erc20.balanceOf(address(this)) != 0, "0 balance");

178:             require(req.sellAmount > 1, "sell amount too low");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

264:         require(stakes[era][account] >= stakeAmount, "insufficient balance");

312:         require(endId <= queue.length, "index out-of-bounds");

313:         require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");

341:         require(basketHandler.isReady() && basketHandler.fullyCollateralized(), "RToken readying");

357:         require(endId <= queue.length, "index out-of-bounds");

429:         require(caller == address(backingManager), "!bm");

432:         require(rsrAmount <= rsrBalance, "seize exceeds balance");

809:         require(currentAllowance >= subtractedValue, "decrease allowance");

829:         require(fromBalance >= amount, "insufficient balance");

865:         require(accountBalance >= amount, "insufficient balances");

894:             require(currentAllowance >= amount, "insufficient allowance");

908:         require(to != address(this), "transfer to self");

912:         require(addr != address(0), "zero address");

916:         require(val != 0, "zero amount");

931:         require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

972:         require(val > MIN_UNSTAKING_DELAY && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");

981:         require(val <= MAX_REWARD_RATIO, "invalid rewardRatio");

989:         require(val <= MAX_WITHDRAWAL_LEAK, "invalid withdrawalLeak");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

174:         require(block.timestamp <= expiry, "signature expired");

181:         require(nonce == _useDelegationNonce(signer), "invalid nonce");

292:         require(timepoint < block.timestamp, "future lookup");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

340:             require(contains && amt >= targetAmts[i], "new target weights");

344:         require(_targetAmts.length() == 0, "missing target weights");

365:             require(coll.status() == CollateralStatus.SOUND, "unsound new collateral");

368:             require(low != 0 && high != FIX_MAX, "invalid price");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

34:         require(address(main_) != address(0), "main is zero address");

42:         require(!main.tradingPausedOrFrozen(), "frozen or trading paused");

47:         require(!main.issuancePausedOrFrozen(), "frozen or issuance paused");

52:         require(!main.frozen(), "frozen");

57:         require(main.hasRole(OWNER, _msgSender()), "governance only");

62:         require(_msgSender() == address(main), "main only");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

117:         require(buPriceLow != 0 && buPriceHigh != FIX_MAX, "BUs unpriced");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

68:             require(maxSell > 1, "trade sizing error");

71:             require(trade.prices.sellLow == 0, "trade pricing error");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

96:         require(address(trade) != address(0), "no trade open");

97:         require(trade.canSettle(), "cannot settle yet");

147:         require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");

154:         require(val <= MAX_TRADE_VOLUME, "invalid minTradeVolume");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

121:         require(startedInSameEra(proposalId), "new era");

131:         require(!startedInSameEra(proposalId), "same era");

144:         require(startedInSameEra(proposalId), "new era");

193:         require(newVotingDelay >= MIN_VOTING_DELAY, "invalid votingDelay");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

122:         require(status == begin, "Invalid trade state");

168:         require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");

169:         require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");

176:         require(sellAmount_ <= sell.balanceOf(address(this)), "unfunded trade");

200:         require(bidder == address(0), "bid already received");

236:         require(bidder == address(0), "bid already received");

279:         require(msg.sender == address(origin), "only origin can settle");

280:         require(bidder != address(0) || block.timestamp > endTime, "auction not over");

299:         require(status == TradeStatus.CLOSED, "only after trade is closed");

317:         require(timestamp >= _startTime, "auction not started");

318:         require(timestamp <= _endTime, "auction over");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

59:         require(status == begin, "Invalid trade state");

91:         require(req.sellAmount <= type(uint96).max, "sellAmount too large");

92:         require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");

99:         require(initBal >= req.sellAmount, "unfunded trade");

180:         require(msg.sender == origin, "only origin can settle");

226:         require(status == TradeStatus.CLOSED, "only after trade is closed");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="GAS-11"></a>[GAS-11] Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*Instances (27)*:
```solidity
File: ./contracts/p1/BackingManager.sol

130:         uint256 balance = rToken.balanceOf(address(this));

160:                 uint256 bal = sellERC20.balanceOf(address(this));

213:         if (rsr.balanceOf(address(this)) != 0) {

215:             IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/Furnace.sol

38:         lastPayoutBal = rToken.balanceOf(address(this));

77:         lastPayoutBal = rToken.balanceOf(address(this)) - amount;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/RToken.sol

300:                 IERC20(erc20s[i]).balanceOf(address(backingManager)),

312:             pastBals[i] = IERC20(expectedERC20sOut[i]).balanceOf(recipient);

340:             uint256 bal = IERC20(expectedERC20sOut[i]).balanceOf(recipient);

422:             erc20.balanceOf(address(this))

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

91:             erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));

158:             require(erc20.balanceOf(address(this)) != 0, "0 balance");

190:         uint256 bal = tokenToBuy.balanceOf(address(this));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

196:         rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));

431:         uint256 rsrBalance = rsr.balanceOf(address(this));

694:         return rsr.balanceOf(address(this)) - stakeRSR - draftRSR;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

175:         address signer = ECDSAUpgradeable.recover(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

176:         require(sellAmount_ <= sell.balanceOf(address(this)), "unfunded trade");

257:         uint256 balanceBefore = buy.balanceOf(address(this)); // {qBuyTok}

260:             amountIn <= buy.balanceOf(address(this)) - balanceBefore,

290:         boughtAmt = buy.balanceOf(address(this)); // {qBuyTok}

292:         sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}

300:         erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

98:         initBal = sell.balanceOf(address(this)); // {qSellTok}

194:         uint256 sellBal = sell.balanceOf(address(this));

203:         boughtAmt = buy.balanceOf(address(this));

227:         IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="GAS-12"></a>[GAS-12] State variables only set in the constructor should be declared `immutable`
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around **20 000 gas** per variable) and replace the expensive storage-reading operations (around **2100 gas** per reading) to a less expensive value reading (**3 gas**)

*Instances (10)*:
```solidity
File: ./contracts/p1/Deployer.sol

68:         rsr = rsr_;

69:         gnosis = gnosis_;

70:         rsrAsset = rsrAsset_;

71:         _implementations = implementations_;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

146:         status = TradeStatus.CLOSED;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

67:         status = TradeStatus.CLOSED;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

27:         versionRegistry = VersionRegistry(_versionRegistry);

28:         roleRegistry = versionRegistry.roleRegistry();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

40:         roleRegistry = _roleRegistry;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

32:         roleRegistry = _roleRegistry;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="GAS-13"></a>[GAS-13] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (24)*:
```solidity
File: ./contracts/mixins/Auth.sol

68:     function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

124:     function freezeShort() external onlyRole(SHORT_FREEZER) {

139:     function freezeLong() external onlyRole(LONG_FREEZER) {

152:     function freezeForever() external onlyRole(OWNER) {

161:     function unfreeze() external onlyRole(OWNER) {

169:     function pauseTrading() external onlyRole(PAUSER) {

176:     function unpauseTrading() external onlyRole(PAUSER) {

183:     function pauseIssuance() external onlyRole(PAUSER) {

190:     function unpauseIssuance() external onlyRole(PAUSER) {

198:     function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

205:     function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

18:     function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/Broker.sol

186:     function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

208:     function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Main.sol

61:     function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {

70:     function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {

79:     function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {

99:     function upgradeMainTo(bytes32 versionHash) external onlyRole(OWNER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

33:     function __Component_init(IMain main_) internal onlyInitializing {

67:     function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

44:     function setFeeRecipient(address feeRecipient_) external onlyOwner {

56:     function setDefaultFeeNumerator(uint256 feeNumerator_) external onlyOwner {

66:     function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {

76:     function resetRTokenFee(address rToken) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="GAS-14"></a>[GAS-14] `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)
Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```

In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

*Saves 5 gas per instance*

*Instances (7)*:
```solidity
File: ./contracts/libraries/Fixed.sol

166:             result++;

170:             result++;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/String.sol

14:         for (uint256 i = 0; i < bStr.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/p1/StRSR.sol

675:         era++;

687:         draftEra++;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

274:                     assigned++;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

100:         tradesOpen--;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

### <a name="GAS-15"></a>[GAS-15] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*Instances (34)*:
```solidity
File: ./contracts/mixins/Auth.sol

29:     bytes32 public constant OWNER_ROLE = OWNER;

30:     bytes32 public constant SHORT_FREEZER_ROLE = SHORT_FREEZER;

31:     bytes32 public constant LONG_FREEZER_ROLE = LONG_FREEZER;

32:     bytes32 public constant PAUSER_ROLE = PAUSER;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

15:     uint256 public constant GAS_FOR_BH_QTY = 100_000; // enough to call bh.quantity

16:     uint256 public constant GAS_FOR_DISABLE_BASKET = 900_000; // enough to disable basket on n=128

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

33:     uint48 public constant MAX_TRADING_DELAY = 60 * 60 * 24 * 365; // {s} 1 year

34:     uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

31:     uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight

32:     uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute

33:     uint48 public constant MAX_WARMUP_PERIOD = 60 * 60 * 24 * 365; // {s} 1 year

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

25:     uint48 public constant MAX_AUCTION_LENGTH = 60 * 60 * 24 * 7; // {s} max valid duration, 1 week

28:     uint48 public constant MIN_AUCTION_LENGTH = 60; // {s} 60 seconds auction min duration

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

31:     string public constant ENS = "reserveprotocol.eth";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

32:     address public constant FURNACE = address(1);

33:     address public constant ST_RSR = address(2);

35:     uint8 public constant MAX_DESTINATIONS_ALLOWED = MAX_DESTINATIONS; // 100

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

15:     uint192 public constant MAX_RATIO = 1e14; // {1} 0.01%

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/RToken.sol

22:     uint256 public constant MIN_THROTTLE_RATE_AMT = 1e18; // {qRTok}

23:     uint256 public constant MAX_THROTTLE_RATE_AMT = 1e48; // {qRTok}

24:     uint192 public constant MAX_THROTTLE_PCT_AMT = 1e18; // {qRTok}

25:     uint192 public constant MIN_EXCHANGE_RATE = 1e9; // D18{BU/rTok}

26:     uint192 public constant MAX_EXCHANGE_RATE = 1e27; // D18{BU/rTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

48:     uint8 public constant decimals = 18;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

22:     uint192 public constant MAX_TRADE_VOLUME = 1e29; // {UoA}

23:     uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

33:     uint256 public constant ONE_HUNDRED_PERCENT = 1e8; // {micro %}

36:     uint256 public constant MIN_VOTING_DELAY = 86400; // {s} ONE_DAY

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

95:     TradeKind public constant KIND = TradeKind.DUTCH_AUCTION;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

24:     TradeKind public constant KIND = TradeKind.BATCH_AUCTION;

25:     uint256 public constant FEE_DENOMINATOR = 1000;

29:     uint96 public constant MAX_ORDERS = 5000; // bounded to avoid going beyond block gas limit

32:     uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

12:     bytes32 public constant EMERGENCY_COUNCIL = keccak256("EMERGENCY_COUNCIL");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

### <a name="GAS-16"></a>[GAS-16] Use shift right/left instead of division/multiplication if possible
While the `DIV` / `MUL` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`
- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`
- ...
- Use `>> 5` instead of `/ 2^5 == / 32`
- Use `<< 6` instead of `* 2^6 == * 64`

TL;DR:
- Shifting left by N is like multiplying by 2^N (Each bits to the left is an increased power of 2)
- Shifting right by N is like dividing by 2^N (Each bits to the right is a decreased power of 2)

*Saves around 2 gas + 20 for unchecked per instance*

*Instances (11)*:
```solidity
File: ./contracts/libraries/Fixed.sol

165:         if (numerator % divisor > (divisor - 1) / 2) {

311:     uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

324:             if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

327:             x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

515:             if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);

600:                 if (mm > ((c_256 - 1) / 2)) result_256 += 1;

663:         if (mm > ((z - 1) / 2)) result += 1; // z should be z-1

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/StRSR.sol

40:     uint48 private constant MIN_UNSTAKING_DELAY = 60 * 2; // {s} 2 minutes

504:         return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

530:             test = (left + right) / 2; // left < test < right because left < right - 1

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

372:                 (low + high) / 2,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

### <a name="GAS-17"></a>[GAS-17] Splitting require() statements that use && saves gas

*Instances (16)*:
```solidity
File: ./contracts/mixins/Auth.sol

199:         require(shortFreeze_ != 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

206:         require(longFreeze_ != 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

233:         require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");

264:             require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");

290:         require(max <= MAX_BACKUP_ERC20S && erc20s.length <= MAX_BACKUP_ERC20S, "too large");

635:         require(val >= MIN_WARMUP_PERIOD && val <= MAX_WARMUP_PERIOD, "invalid warmupPeriod");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Deployer.sol

114:         require(owner != address(0) && owner != address(this), "invalid owner");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/RToken.sol

413:         require(low >= MIN_EXCHANGE_RATE && high <= MAX_EXCHANGE_RATE, "BU rate out of range");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

150:         require(buyHigh != 0 && buyHigh != FIX_MAX, "buy asset price unknown");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

341:         require(basketHandler.isReady() && basketHandler.fullyCollateralized(), "RToken readying");

972:         require(val > MIN_UNSTAKING_DELAY && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

340:             require(contains && amt >= targetAmts[i], "new target weights");

368:             require(low != 0 && high != FIX_MAX, "invalid price");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

117:         require(buPriceLow != 0 && buPriceHigh != FIX_MAX, "BUs unpriced");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

168:         require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");

169:         require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="GAS-18"></a>[GAS-18] Use of `this` instead of marking as `public` an `external` function
Using `this.` is like making an expensive external call. Consider marking the called function as public

*Saves around 2000 gas per instance*

*Instances (6)*:
```solidity
File: ./contracts/p1/AssetRegistry.sol

181:                         keccak256(abi.encodePacked(this.version())),

225:                     keccak256(abi.encodePacked(this.version())),

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

92:             try this.rebalance(trade.KIND()) {} catch (bytes memory errData) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/Main.sol

108:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");

117:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

58:         try this.distributeTokenToBuy() {} catch (bytes memory errData) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

### <a name="GAS-19"></a>[GAS-19] Increments/decrements can be unchecked in for-loops
In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

The change would be:

```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

These save around **25 gas saved** per instance.

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256`.

*Instances (62)*:
```solidity
File: ./contracts/libraries/Array.sol

11:         for (uint256 i = 1; i < arrLen; ++i) {

12:             for (uint256 j = 0; j < i; ++j) {

23:         for (uint256 i = 1; i < arrLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/String.sol

14:         for (uint256 i = 0; i < bStr.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

48:         for (uint256 i = 0; i < length; ++i) {

63:         for (uint256 i = 0; i < length; ++i) {

151:         for (uint256 i = 0; i < length; ++i) {

163:         for (uint256 i = 0; i < length; ++i) {

176:             for (uint256 i = 0; i < assetLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

236:         for (uint256 i = 0; i < length; ++i) {

289:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

294:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

142:         for (uint256 i = 0; i < len; ++i) refAmts[i] = basket.refAmts[basket.erc20s[i]];

251:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

260:         for (uint256 i = 0; i < erc20s.length; ++i) {

296:         for (uint256 i = 0; i < erc20s.length; ++i) {

321:         for (uint256 i = 0; i < size; ++i) {

424:         for (uint256 i = 0; i < len; ++i) {

491:         for (uint256 i = 0; i < length; ++i) {

535:         for (uint48 i = 0; i < basketNonces.length; ++i) {

547:             for (uint256 j = 0; j < b.erc20s.length; ++j) {

554:                 for (uint256 k = 0; k < len; ++k) {

593:         for (uint256 i = 0; i < len; ++i) {

615:         for (uint256 i = 0; i < length; ++i) {

676:         for (uint256 i = 0; i < len; ++i) {

684:         for (uint256 i = 0; i < erc20s.length; ++i) {

713:         for (uint256 i = 0; i < b.erc20s.length; ++i) {

751:         for (uint256 i = 0; i < erc20s.length; ++i) {

769:         for (uint256 i = 0; i < erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Distributor.sol

92:         for (uint256 i = 0; i < dests.length; ++i) {

145:         for (uint256 i = 0; i < destinations.length(); ++i) {

169:         for (uint256 i = 0; i < numTransfers; ++i) {

206:         for (uint256 i = 0; i < length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

148:         for (uint256 i = 0; i < erc20s.length; ++i) {

214:         for (uint256 i = 0; i < erc20s.length; ++i) {

269:         for (uint256 i = 0; i < portions.length; ++i) {

297:         for (uint256 i = 0; i < erc20s.length; ++i) {

311:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

321:             for (uint256 i = 0; i < erc20s.length; ++i) {

339:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

89:         for (uint256 i = 0; i < len; ++i) {

127:         for (uint256 i = 0; i < len; ++i) {

142:             for (uint256 i = 0; i < len; ++i) {

153:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

72:         for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

80:         for (uint256 i = 0; i < length; ++i) {

180:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

198:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

202:             for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {

241:         for (uint256 i = 0; i < targetsLength; ++i) {

252:             for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {

263:             for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {

325:         for (uint256 i = 0; i < len; ++i) {

337:         for (uint256 i = 0; i < len; ++i) {

363:         for (uint256 i = 0; i < len; ++i) {

380:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

139:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

288:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

27:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

39:         for (uint256 i = 0; i < validForVersions.length; ++i) {

67:         for (uint256 i = 0; i < _versionHashes.length; ++i) {

95:         for (uint256 i = 0; i < _assets.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

### <a name="GAS-20"></a>[GAS-20] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (8)*:
```solidity
File: ./contracts/libraries/Fixed.sol

102:     if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5

103:     if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

57:         if (amount > 0) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

49:        0 <= longFreeze[a] <= LONG_FREEZE_CHARGES for all addrs a

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

264:             require(0 < targetAmts[i] && targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

79:             require(revTotals.rsrTotal == 0, "rsrTotal > 0");

81:             require(revTotals.rTokenTotal == 0, "rTokenTotal > 0");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

139:        If unsoundPrimeWt(tgt) > 0 and len(backups(tgt)) == 0 for some tgt, then return false.

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

### <a name="GAS-21"></a>[GAS-21] `internal` functions not called by the contract should be removed
If the functions are required by an interface, the contract should inherit from that interface and use the `override` keyword

*Instances (32)*:
```solidity
File: ./contracts/libraries/Allowance.sol

17:     function safeApproveFallbackToMax(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/libraries/Array.sol

9:     function allUnique(IERC20[] memory arr) internal pure returns (bool) {

21:     function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/Fixed.sol

223:     function plus(uint192 x, uint192 y) internal pure returns (uint192) {

230:     function plusu(uint192 x, uint256 y) internal pure returns (uint192) {

237:     function minus(uint192 x, uint192 y) internal pure returns (uint192) {

244:     function minusu(uint192 x, uint256 y) internal pure returns (uint192) {

270:     function mulu(uint192 x, uint256 y) internal pure returns (uint192) {

317:     function powu(uint192 x_, uint48 y) internal pure returns (uint192) {

332:     function sqrt(uint192 x) internal pure returns (uint192) {

337:     function lt(uint192 x, uint192 y) internal pure returns (bool) {

341:     function lte(uint192 x, uint192 y) internal pure returns (bool) {

345:     function gt(uint192 x, uint192 y) internal pure returns (bool) {

349:     function gte(uint192 x, uint192 y) internal pure returns (bool) {

353:     function eq(uint192 x, uint192 y) internal pure returns (bool) {

357:     function neq(uint192 x, uint192 y) internal pure returns (bool) {

364:     function near(

406:     function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {

413:     function mulu_toUint(

424:     function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {

431:     function mul_toUint(

494:     function safeMul(

544:     function safeDiv(

561:     function safeMulDiv(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Permit.sol

10:     function requireSignature(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/libraries/String.sol

11:     function toLower(string memory str) internal pure returns (string memory) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

37:     function useAvailable(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

77:     function setFrom(Basket storage self, Basket storage other) internal {

90:     function add(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

24:     function claimRewards(IAssetRegistry reg) internal {

39:     function claimRewardsSingle(IAsset asset) internal {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

118:     function prepareTradeToCoverDeficit(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)


## Non Critical Issues


| |Issue|Instances|
|-|:-|:-:|
| [NC-1](#NC-1) | Replace `abi.encodeWithSignature` and `abi.encodeWithSelector` with `abi.encodeCall` which keeps the code typo/type safe | 4 |
| [NC-2](#NC-2) | Array indices should be referenced via `enum`s rather than via numeric literals | 2 |
| [NC-3](#NC-3) | `require()` should be used instead of `assert()` | 19 |
| [NC-4](#NC-4) | Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked` | 9 |
| [NC-5](#NC-5) | Constants should be in CONSTANT_CASE | 3 |
| [NC-6](#NC-6) | `constant`s should be defined rather than using magic numbers | 57 |
| [NC-7](#NC-7) | Control structures do not follow the Solidity Style Guide | 172 |
| [NC-8](#NC-8) | Dangerous `while(true)` loop | 1 |
| [NC-9](#NC-9) | Default Visibility for constants | 33 |
| [NC-10](#NC-10) | Draft Dependencies | 1 |
| [NC-11](#NC-11) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 39 |
| [NC-12](#NC-12) | Unused `error` definition | 1 |
| [NC-13](#NC-13) | Event missing indexed field | 4 |
| [NC-14](#NC-14) | Events that mark critical parameter changes should contain both the old and the new value | 27 |
| [NC-15](#NC-15) | Function ordering does not follow the Solidity style guide | 15 |
| [NC-16](#NC-16) | Functions should not be longer than 50 lines | 225 |
| [NC-17](#NC-17) | Change int to int256 | 9 |
| [NC-18](#NC-18) | Change uint to uint256 | 1 |
| [NC-19](#NC-19) | Interfaces should be defined in separate files from their usage | 2 |
| [NC-20](#NC-20) | Lack of checks in setters | 7 |
| [NC-21](#NC-21) | Missing Event for critical parameters change | 13 |
| [NC-22](#NC-22) | NatSpec is completely non-existent on functions that should have them | 34 |
| [NC-23](#NC-23) | Incomplete NatSpec: `@param` is missing on actually documented functions | 50 |
| [NC-24](#NC-24) | Incomplete NatSpec: `@return` is missing on actually documented functions | 4 |
| [NC-25](#NC-25) | Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor | 10 |
| [NC-26](#NC-26) | Constant state variables defined more than once | 4 |
| [NC-27](#NC-27) | Consider using named mappings | 30 |
| [NC-28](#NC-28) | Numeric values having to do with time should use time units for readability | 1 |
| [NC-29](#NC-29) | Adding a `return` statement when the function defines a named return variable, is redundant | 20 |
| [NC-30](#NC-30) | `require()` / `revert()` statements should have descriptive reason strings | 1 |
| [NC-31](#NC-31) | Take advantage of Custom Error's return value property | 34 |
| [NC-32](#NC-32) | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`) | 2 |
| [NC-33](#NC-33) | Use scientific notation for readability reasons for large multiples of ten | 1 |
| [NC-34](#NC-34) | Contract does not follow the Solidity style guide's suggested layout ordering | 18 |
| [NC-35](#NC-35) | Some require descriptions are not clear | 1 |
| [NC-36](#NC-36) | Use Underscores for Number Literals (add an underscore every 3 digits) | 11 |
| [NC-37](#NC-37) | Internal and private variables and functions names should begin with an underscore | 162 |
| [NC-38](#NC-38) | Event is missing `indexed` fields | 5 |
| [NC-39](#NC-39) | Constants should be defined rather than using magic numbers | 5 |
| [NC-40](#NC-40) | `public` functions not called by the contract should be declared `external` instead | 33 |
| [NC-41](#NC-41) | Variables need not be initialized to zero | 62 |
### <a name="NC-1"></a>[NC-1] Replace `abi.encodeWithSignature` and `abi.encodeWithSelector` with `abi.encodeCall` which keeps the code typo/type safe
When using `abi.encodeWithSignature`, it is possible to include a typo for the correct function signature.
When using `abi.encodeWithSignature` or `abi.encodeWithSelector`, it is also possible to provide parameters that are not of the correct type for the function.

To avoid these pitfalls, it would be best to use [`abi.encodeCall`](https://solidity-by-example.org/abi-encode/) instead.

*Instances (4)*:
```solidity
File: ./contracts/libraries/Fixed.sol

34: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/Main.sol

159:             abi.encodeWithSelector(UUPSUpgradeable.upgradeTo.selector, implementation)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

30:                 abi.encodeWithSignature("claimRewards()"),

42:             abi.encodeWithSignature("claimRewards()"),

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

### <a name="NC-2"></a>[NC-2] Array indices should be referenced via `enum`s rather than via numeric literals

*Instances (2)*:
```solidity
File: ./contracts/p1/Deployer.sol

252:         assets[0] = new RTokenAsset(components.rToken, params.rTokenMaxTradeVolume);

253:         assets[1] = rsrAsset;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

### <a name="NC-3"></a>[NC-3] `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Additionally, a require statement (or a custom error) are more friendly in terms of understanding what happened."

*Instances (19)*:
```solidity
File: ./contracts/p1/BackingManager.sol

308:         assert(tradesOpen == 0);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/StRSR.sol

183:         assert(bytes(name_).length != 0);

184:         assert(bytes(symbol_).length != 0);

844:         assert(totalStakes + amount < type(uint224).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

205:             assert(targetIndex < targetsLength);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

75:         assert(doTrade);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

48:         assert(

123:         assert(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

126:         assert(address(trades[sell]) == address(0)); // ensure calling class has checked this

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

125:         assert(status == TradeStatus.PENDING);

165:         assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);

191:         assert(_worstPrice <= _bestPrice);

201:         assert(status == TradeStatus.OPEN);

225:         assert(status == TradeStatus.CLOSED);

237:         assert(status == TradeStatus.OPEN);

268:         assert(status == TradeStatus.CLOSED);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

62:         assert(status == TradeStatus.PENDING);

101:         assert(origin_ != address(0));

188:             assert(isAuctionCleared());

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="NC-4"></a>[NC-4] Use `string.concat()` or `bytes.concat()` instead of `abi.encodePacked`
Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

Solidity version 0.8.12 introduces `string.concat()` (vs `abi.encodePacked(<str>,<str>), which catches concatenation errors (in the event of a `bytes` data mixed in the concatenation)`)

*Instances (9)*:
```solidity
File: ./contracts/libraries/Permit.sol

19:                 IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==

28:                     abi.encodePacked(r, s, v)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

181:                         keccak256(abi.encodePacked(this.version())),

225:                     keccak256(abi.encodePacked(this.version())),

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/Deployer.sol

228:             string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));

229:             string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Main.sol

108:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");

117:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

47:         bytes32 versionHash = keccak256(abi.encodePacked(version));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-5"></a>[NC-5] Constants should be in CONSTANT_CASE
For `constant` variable names, each word should use all capital letters, with underscores separating each word (CONSTANT_CASE)

*Instances (3)*:
```solidity
File: ./contracts/libraries/Fixed.sol

34: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/StRSR.sol

48:     uint8 public constant decimals = 18;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

50: uint192 constant MAX_EXP = 6502287e18; // {1} (1000000/999999)^6502287 = ~666.6667

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-6"></a>[NC-6] `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*Instances (57)*:
```solidity
File: ./contracts/libraries/Fixed.sol

11:     "fixed192x18" -- a value represented by 192 bits, that makes 18 digits available to

105:     shiftLeft += 18;

107:     uint256 coeff = 10**abs(shiftLeft);

165:         if (numerator % divisor > (divisor - 1) / 2) {

324:             if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

327:             x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

397:         decimals -= 18; // shift so that toUint happens at the same time.

515:             if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);

586:             r *= 2 - c_256 * r;

587:             r *= 2 - c_256 * r;

588:             r *= 2 - c_256 * r;

589:             r *= 2 - c_256 * r;

590:             r *= 2 - c_256 * r;

591:             r *= 2 - c_256 * r;

592:             r *= 2 - c_256 * r;

593:             r *= 2 - c_256 * r;

600:                 if (mm > ((c_256 - 1) / 2)) result_256 += 1;

635:         r *= 2 - z * r;

636:         r *= 2 - z * r;

637:         r *= 2 - z * r;

638:         r *= 2 - z * r;

639:         r *= 2 - z * r;

640:         r *= 2 - z * r;

641:         r *= 2 - z * r;

642:         r *= 2 - z * r;

663:         if (mm > ((z - 1) / 2)) result += 1; // z should be z-1

724:     if (xAux >= 2**128) {

725:         xAux >>= 128;

726:         result <<= 64;

728:     if (xAux >= 2**64) {

729:         xAux >>= 64;

730:         result <<= 32;

732:     if (xAux >= 2**32) {

733:         xAux >>= 32;

734:         result <<= 16;

736:     if (xAux >= 2**16) {

737:         xAux >>= 16;

738:         result <<= 8;

740:     if (xAux >= 2**8) {

741:         xAux >>= 8;

742:         result <<= 4;

744:     if (xAux >= 2**4) {

745:         xAux >>= 4;

746:         result <<= 2;

748:     if (xAux >= 2**2) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/String.sol

16:             if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {

18:                 bLower[i] = bytes1(uint8(bStr[i]) + 32);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

260:             gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/StRSR.sol

504:         return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

530:             test = (left + right) / 2; // left < test < right because left < right - 1

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

139:         if (length > 5) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

372:                 (low + high) / 2,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

165:         assert(address(sell_) != address(0) && address(buy_) != address(0) && auctionLength >= 60);

168:         require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");

169:         require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

109:         worstCasePrice = shiftl_toFix(req.minBuyAmount, 9).divu(req.sellAmount, FLOOR);

217:             uint192 clearingPrice = shiftl_toFix(adjustedBuyAmt, 9).divu(adjustedSoldAmt, FLOOR);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="NC-7"></a>[NC-7] Control structures do not follow the Solidity Style Guide
See the [control structures](https://docs.soliditylang.org/en/latest/style-guide.html#control-structures) section of the Solidity Style Guide

*Instances (172)*:
```solidity
File: ./contracts/libraries/Allowance.sol

30:         if (value == 0) return;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/libraries/Array.sol

13:                 if (arr[i] == arr[j]) return false;

24:             if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/Fixed.sol

74:     if (FIX_MAX < x) revert UIntOutOfBounds();

90:     return shiftl_toFix(x, shiftLeft, FLOOR);

95: function shiftl_toFix(

97:     int8 shiftLeft,

101:     if (x == 0) return 0;

102:     if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5

103:     if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

105:     shiftLeft += 18;

107:     uint256 coeff = 10**abs(shiftLeft);

108:     uint256 shifted = (shiftLeft >= 0) ? x * coeff : _divrnd(x, coeff, rounding);

110:     return _safeWrap(shifted);

162:     if (rounding == FLOOR) return result;

199:         return shiftl(x, decimals, FLOOR);

206:     function shiftl(

212:         if (x == 0) return 0;

213:         if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192

214:         if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0

319:         if (y == 1) return x_;

320:         if (x_ == FIX_ONE || y == 0) return FIX_ONE;

324:             if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

325:             if (y <= 1) break;

369:         uint192 diff = x <= y ? y - x : x - y;

370:         return diff < epsilon;

381:         return shiftl_toUint(x, decimals, FLOOR);

387:     function shiftl_toUint(

393:         if (x == 0) return 0; // always computable, no matter what decimals is

394:         if (decimals <= -42) return (rounding == CEIL ? 1 : 0);

395:         if (96 <= decimals) revert UIntOutOfBounds();

397:         decimals -= 18; // shift so that toUint happens at the same time.

501:         if (a == 0 || b == 0) return 0;

504:         if (a == FIX_MAX || b == FIX_MAX) return FIX_MAX;

511:             if (rawDelta / b != a) return FIX_MAX;

512:             uint256 shiftDelta = rawDelta;

515:             if (rounding == RoundingMode.ROUND) shiftDelta += (FIX_ONE / 2);

516:             else if (rounding == RoundingMode.CEIL) shiftDelta += FIX_ONE - 1;

531:             if (shiftDelta < rawDelta) return FIX_MAX;

534:             if (shiftDelta / FIX_ONE > FIX_MAX) return FIX_MAX;

549:         if (a == 0) return 0;

550:         if (b == 0) return FIX_MAX;

553:         if (raw >= FIX_MAX) return FIX_MAX;

567:         if (a == 0 || b == 0) return 0;

568:         if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;

573:             if (hi >= c) return FIX_MAX;

575:             if (mm > lo) hi -= 1;

598:                 if (mm != 0) result_256 += 1;

600:                 if (mm > ((c_256 - 1) / 2)) result_256 += 1;

604:         if (result_256 >= FIX_MAX) return FIX_MAX;

626:         if (hi >= z) revert UIntOutOfBounds();

628:         if (mm > lo) hi -= 1;

657:     if (rounding == FLOOR) return result;

661:         if (mm != 0) result += 1;

663:         if (mm > ((z - 1) / 2)) result += 1; // z should be z-1

679:         if (mm < lo) hi -= 1;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

43:         if (throttle.params.amtRate == 0 && throttle.params.pctRate == 0) return;

76:         if (available > limit) available = limit;

89:         if (params.amtRate > limit) limit = params.amtRate;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

93:         if (role == LONG_FREEZER) longFreezes[account] = LONG_FREEZE_CHARGES;

143:         if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

97:     IFurnace public furnace;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

94:             if (quantity != 0) basketHandler.disableBasket(); // not an interaction

113:             if (quantity != 0) basketHandler.disableBasket(); // not an interaction

234:             if (assets[erc20] == asset) return false;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

40:     IFurnace private furnace;

97:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

131:         if (balance >= MAX_DISTRIBUTION * MAX_DESTINATIONS) rToken.dissolve(balance);

132:         if (basketsHeld.bottom >= rToken.basketsNeeded()) return; // return if now capitalized

161:                 if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);

246:                 uint256 delta = bal.minus(req).shiftl_toUint(int8(asset.erc20Decimals()));

248:                 if (tokensPerShare == 0) continue;

298:             if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

51:     uint48 public nonce; // {basketNonce} A unique identifier for this basket instance

236:         if (

299:             assetRegistry.toColl(erc20s[i]); // reverts if not collateral

319:         if (disabled || size == 0) return CollateralStatus.DISABLED;

324:                 if (s == CollateralStatus.DISABLED) return CollateralStatus.DISABLED;

360:         if (!asset.isCollateral()) return FIX_ZERO;

368:         if (!enableIssuancePremium || coll.lastSave() != block.timestamp) return FIX_ONE;

372:             if (pegPrice == 0) return FIX_ONE;

374:             if (pegPrice >= targetPerRef) return FIX_ONE;

397:         if (refPerTok == 0) return FIX_MAX;

427:                 if (qty == 0) continue;

443:                         if (premium > FIX_ONE) qty = qty.safeMul(premium, CEIL);

450:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

503:                 if (premium > FIX_ONE) q = q.safeMul(premium, rounding);

507:             quantities[i] = q.shiftl_toUint(

550:                 if (address(b.erc20s[j]) == address(0)) continue;

567:                         if (!asset.isCollateral()) continue; // skip token if not collateral

579:                         if (errData.length == 0) revert(); // solhint-disable-line reason-string

599:             .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);

612:         if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);

617:             if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);

621:             if (q == FIX_MAX) return BasketRange(FIX_ZERO, FIX_MAX);

717:                 if (!asset.isCollateral()) continue; // skip token if no longer registered

723:                 .shiftl_toUint(int8(asset.erc20Decimals()), FLOOR);

728:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Deployer.sol

14: import "../interfaces/IFurnace.sol";

172:             furnace: IFurnace(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

39:     IFurnace private furnace;

126:         bool isRSR = erc20 == rsr; // if false: isRToken

133:             if (totalShares != 0) tokensPerShare = amount / totalShares;

151:             if (numberOfShares == 0) continue;

157:                 if (transferAmt != 0) accountRewards = true;

160:                 if (transferAmt != 0) accountRewards = true;

247:         if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");

248:         if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

5: import "../interfaces/IFurnace.sol";

66:         if (uint48(block.timestamp) < uint64(lastPayout + 1)) return;

78:         if (amount != 0) rToken.melt(amount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/RToken.sol

50:     IFurnace private furnace;

215:             if (amounts[i] == 0) continue;

305:             if (prorata < amounts[i]) amounts[i] = prorata;

322:                 if (amounts[i] == 0) continue; // unregistered ERC20s will have 0 amount

323:                 if (allZero) allZero = false;

333:             if (allZero) revert("empty redemption");

437:         if (supply < available) available = supply;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

23:     IFurnace private furnace;

62:             if (errData.length == 0) revert(); // solhint-disable-line reason-string

128:             if (erc20s[i] == IERC20(address(rToken))) involvesRToken = true;

131:                 if (len == 1) return; // return early if tokenToBuy is only entry

139:         if (involvesRToken) assetRegistry.refresh();

155:             if (erc20 == tokenToBuy) continue;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

309:         if (endId == 0 || firstId >= endId) return;

329:         if (rsrAmount == 0) return;

355:         if (endId == 0 || firstId >= endId) return;

376:         if (rsrAmount == 0) return;

520:         if (left >= right) return right;

521:         if (queue[left].availableAt > time) return left;

531:             if (queue[test].availableAt <= time) left = test;

594:         if (block.timestamp < payoutLastPaid + 1) return;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

99:         if (weight == FIX_ZERO) return;

127:        Let primeWt(e) = if e in config.erc20s and isGood(e)

130:        Let backupWt(e) = if e in backups(tgt)

139:        If unsoundPrimeWt(tgt) > 0 and len(backups(tgt)) == 0 for some tgt, then return false.

154:        Given all that, if nextBasket() returns true, then for all tgt,

203:                 if (targetNames.at(targetIndex) == config.targetNames[_erc20]) break;

213:             if (

242:             if (totalWeights[i].lte(goodWeights[i])) continue; // Don't need any backup weight

253:                 if (goodCollateral(_targetName, backup.erc20s[j], assetRegistry)) size++;

257:             if (size == 0) return false;

296:         if (address(erc20) == address(0)) return false;

341:             if (amt > targetAmts[i]) _targetAmts.set(targetName, amt - targetAmts[i]);

364:             ICollateral coll = assetRegistry.toColl(erc20s[i]); // reverts if unregistered

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

57:         if (

141:             if (reg.erc20s[i] == IERC20(address(ctx.rToken))) continue;

147:             if (

210:             if (uint256(deltaTop) + ctx.basketsHeld.top > FIX_MAX) range.top = FIX_MAX;

225:         if (range.top > basketsNeeded) range.top = basketsNeeded;

226:         if (range.bottom > range.top) range.bottom = range.top;

282:         maxes.surplusStatus = CollateralStatus.IFFY; // least-desirable sell status

289:             if (address(reg.erc20s[i]) == address(ctx.rToken)) continue;

302:                 if (high == 0) continue; // skip over worthless assets

315:                 if (

361:             if (

395:             return other != CollateralStatus.IFFY || surplusAmt.gt(curr.surplus);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

69:             if (s > maxSell) s = maxSell;

83:         req.sellAmount = s.shiftl_toUint(int8(trade.sell.erc20Decimals()), FLOOR);

84:         req.minBuyAmount = b.shiftl_toUint(int8(trade.buy.erc20Decimals()), CEIL);

166:             amt.shiftl_toUint(int8(asset.erc20Decimals())) > 1;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

65:         super.setVotingDelay(newVotingDelay); // has onlyGovernance modifier

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

133:         return sellAmount.shiftl_toUint(int8(sell.decimals()));

355:                 (_bestPrice - worstPrice).mulDiv(progression - FORTY_FIVE_PERCENT, FIFTY_PERCENT);

367:         return sellAmount.mul(price, CEIL).shiftl_toUint(int8(buy.decimals()), CEIL);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

109:         worstCasePrice = shiftl_toFix(req.minBuyAmount, 9).divu(req.sellAmount, FLOOR);

127:             DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))

133:         if (minBuyAmtPerOrder == 0) minBuyAmtPerOrder = 1;

205:         if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);

206:         if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

217:             uint192 clearingPrice = shiftl_toFix(adjustedBuyAmt, 9).divu(adjustedSoldAmt, FLOOR);

218:             if (clearingPrice.lt(worstCasePrice)) broker.reportViolation();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="NC-8"></a>[NC-8] Dangerous `while(true)` loop
Consider using for-loops to avoid all risks of an infinite-loop situation

*Instances (1)*:
```solidity
File: ./contracts/libraries/Fixed.sol

323:         while (true) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

### <a name="NC-9"></a>[NC-9] Default Visibility for constants
Some constants are using the default visibility. For readability, consider explicitly declaring them as `internal`.

*Instances (33)*:
```solidity
File: ./contracts/libraries/Fixed.sol

34: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

37: uint256 constant FIX_ONE_256 = 1e18;

38: uint8 constant FIX_DECIMALS = 18;

42: uint64 constant FIX_SCALE = 1e18;

45: uint128 constant FIX_SCALE_SQ = 1e36;

49: uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

51: uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.

52: uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.

53: uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)

54: uint192 constant FIX_MIN = 0; // The smallest uint192.

63: RoundingMode constant FLOOR = RoundingMode.FLOOR;

64: RoundingMode constant ROUND = RoundingMode.ROUND;

65: RoundingMode constant CEIL = RoundingMode.CEIL;

311:     uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

6: uint48 constant ONE_HOUR = 3600; // {seconds/hour}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

7: uint256 constant LONG_FREEZE_CHARGES = 6; // 6 uses

8: uint48 constant MAX_UNFREEZE_AT = type(uint48).max;

9: uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month

10: uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/Versioned.sol

7: string constant VERSION = "4.0.0";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Versioned.sol)

```solidity
File: ./contracts/p1/Broker.sol

16: uint256 constant GNOSIS_MAX_TOKENS = 7e28;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

12: uint256 constant ONE_DAY = 86400; // {s}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

43: uint192 constant FIVE_PERCENT = 5e16; // {1} 0.05

44: uint192 constant TWENTY_PERCENT = 20e16; // {1} 0.2

45: uint192 constant TWENTY_FIVE_PERCENT = 25e16; // {1} 0.25

46: uint192 constant FORTY_FIVE_PERCENT = 45e16; // {1} 0.45

47: uint192 constant FIFTY_PERCENT = 50e16; // {1} 0.5

48: uint192 constant NINETY_FIVE_PERCENT = 95e16; // {1} 0.95

50: uint192 constant MAX_EXP = 6502287e18; // {1} (1000000/999999)^6502287 = ~666.6667

51: uint192 constant BASE = 999999e12; // {1} (999999/1000000)

52: uint192 constant ONE_POINT_FIVE = 150e16; // {1} 1.5

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

6: uint256 constant MAX_FEE_NUMERATOR = 15_00; // Max DAO Fee: 15%

7: uint256 constant FEE_DENOMINATOR = 100_00;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="NC-10"></a>[NC-10] Draft Dependencies
Draft contracts have not received adequate security auditing or are liable to change with future developments.

*Instances (1)*:
```solidity
File: ./contracts/p1/StRSR.sol

7: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

### <a name="NC-11"></a>[NC-11] Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

*Instances (39)*:
```solidity
File: ./contracts/p1/AssetRegistry.sol

127:         require(_erc20s.contains(address(erc20)), "erc20 unregistered");

135:         require(_erc20s.contains(address(erc20)), "erc20 unregistered");

179:                 require(

223:             require(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

120:         require(tradesOpen == 0, "trade open");

121:         require(basketHandler.isReady(), "basket not ready");

122:         require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");

187:         require(tradesOpen == 0, "trade open");

188:         require(basketHandler.isReady(), "basket not ready");

189:         require(block.timestamp >= basketHandler.timestamp() + tradingDelay, "trading delayed");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

233:         require(erc20s.length != 0 && erc20s.length == targetAmts.length, "invalid lengths");

526:         require(basketNonces.length == portions.length, "invalid lengths");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

86:         require(

90:         require(

187:         require(

209:         require(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Main.sol

62:         require(address(versionRegistry_) != address(0), "invalid registry address");

63:         require(address(versionRegistry) == address(0), "already set");

71:         require(address(registry_) != address(0), "invalid registry address");

72:         require(address(assetPluginRegistry) == address(0), "already set");

80:         require(address(feeRegistry_) != address(0), "invalid registry address");

81:         require(address(daoFeeRegistry) == address(0), "already set");

100:         require(address(versionRegistry) != address(0), "no registry");

116:         require(address(versionRegistry) != address(0), "no registry");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

191:         require(amount != 0, "Cannot redeem zero");

192:         require(amount <= balanceOf(caller), "insufficient balance");

266:         require(amount != 0, "Cannot redeem zero");

267:         require(amount <= balanceOf(_msgSender()), "insufficient balance");

357:         require(_msgSender() == address(backingManager), "not backing manager");

389:         require(caller == address(backingManager), "not backing manager");

398:         require(_msgSender() == address(backingManager), "not backing manager");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

264:         require(stakes[era][account] >= stakeAmount, "insufficient balance");

312:         require(endId <= queue.length, "index out-of-bounds");

357:         require(endId <= queue.length, "index out-of-bounds");

829:         require(fromBalance >= amount, "insufficient balance");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

121:         require(startedInSameEra(proposalId), "new era");

144:         require(startedInSameEra(proposalId), "new era");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

200:         require(bidder == address(0), "bid already received");

236:         require(bidder == address(0), "bid already received");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-12"></a>[NC-12] Unused `error` definition
Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.

*Instances (1)*:
```solidity
File: ./contracts/registry/VersionRegistry.sol

21:     error VersionRegistry__InvalidRoleRegistry();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-13"></a>[NC-13] Event missing indexed field
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. This is especially useful when it comes to filtering based on an address. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Where applicable, each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three applicable fields, all of the applicable fields should be indexed.

*Instances (4)*:
```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

24:     event AssetPluginRegistryUpdated(bytes32 versionHash, address asset, bool validity);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

25:     event DefaultFeeNumeratorSet(uint256 defaultFeeNumerator);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

24:     event VersionRegistered(bytes32 versionHash, IDeployer deployer);

25:     event VersionDeprecated(bytes32 versionHash);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-14"></a>[NC-14] Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*Instances (27)*:
```solidity
File: ./contracts/mixins/Auth.sol

198:     function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
             require(shortFreeze_ != 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
             emit ShortFreezeDurationSet(shortFreeze, shortFreeze_);

205:     function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
             require(longFreeze_ != 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
             emit LongFreezeDurationSet(longFreeze, longFreeze_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

315:     function setTradingDelay(uint48 val) public governance {
             require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");
             emit TradingDelaySet(tradingDelay, val);

322:     function setBackingBuffer(uint192 val) public governance {
             require(val <= MAX_BACKING_BUFFER, "invalid backingBuffer");
             emit BackingBufferSet(backingBuffer, val);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

284:     function setBackupConfig(
             bytes32 targetName,
             uint256 max,
             IERC20[] calldata erc20s
         ) external {
             requireGovernanceOnly();
             require(max <= MAX_BACKUP_ERC20S && erc20s.length <= MAX_BACKUP_ERC20S, "too large");
             requireValidCollArray(erc20s);
             BackupConfig storage conf = config.backups[targetName];
             conf.max = max;
             delete conf.erc20s;
     
             for (uint256 i = 0; i < erc20s.length; ++i) {
                 // This is a nice catch to have, but in general it is possible for
                 // an ERC20 in the backup config to have its asset altered.
                 assetRegistry.toColl(erc20s[i]); // reverts if not collateral
                 conf.erc20s.push(erc20s[i]);
             }
             emit BackupConfigSet(targetName, max, erc20s);

633:     function setWarmupPeriod(uint48 val) public {
             requireGovernanceOnly();
             require(val >= MIN_WARMUP_PERIOD && val <= MAX_WARMUP_PERIOD, "invalid warmupPeriod");
             emit WarmupPeriodSet(warmupPeriod, val);

641:     function setIssuancePremiumEnabled(bool val) public {
             requireGovernanceOnly();
             emit EnableIssuancePremiumSet(enableIssuancePremium, val);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

178:     function setGnosis(IGnosis newGnosis) public governance {
             require(address(newGnosis) != address(0), "invalid Gnosis address");
     
             emit GnosisSet(gnosis, newGnosis);

186:     function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {
             require(
                 address(newTradeImplementation) != address(0),
                 "invalid batchTradeImplementation address"
             );
     
             emit BatchTradeImplementationSet(batchTradeImplementation, newTradeImplementation);

197:     function setBatchAuctionLength(uint48 newAuctionLength) public governance {
             require(
                 newAuctionLength == 0 ||
                     (newAuctionLength >= MIN_AUCTION_LENGTH && newAuctionLength <= MAX_AUCTION_LENGTH),
                 "invalid batchAuctionLength"
             );
             emit BatchAuctionLengthSet(batchAuctionLength, newAuctionLength);

208:     function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {
             require(
                 address(newTradeImplementation) != address(0),
                 "invalid dutchTradeImplementation address"
             );
     
             emit DutchTradeImplementationSet(dutchTradeImplementation, newTradeImplementation);

219:     function setDutchAuctionLength(uint48 newAuctionLength) public governance {
             require(
                 newAuctionLength == 0 ||
                     (newAuctionLength >= MIN_AUCTION_LENGTH && newAuctionLength <= MAX_AUCTION_LENGTH),
                 "invalid dutchAuctionLength"
             );
             emit DutchAuctionLengthSet(dutchAuctionLength, newAuctionLength);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Furnace.sol

83:     function setRatio(uint192 ratio_) public governance {
            require(ratio_ <= MAX_RATIO, "invalid ratio");
            melt(); // cannot revert
    
            // The ratio can safely be set to 0 to turn off payouts, though it is not recommended
            emit RatioSet(ratio, ratio_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/RToken.sol

397:     function setBasketsNeeded(uint192 basketsNeeded_) external notTradingPausedOrFrozen {
             require(_msgSender() == address(backingManager), "not backing manager");
             emit BasketsNeededChanged(basketsNeeded, basketsNeeded_);

451:     function setIssuanceThrottleParams(ThrottleLib.Params calldata params) public governance {
             require(params.amtRate >= MIN_THROTTLE_RATE_AMT, "issuance amtRate too small");
             require(params.amtRate <= MAX_THROTTLE_RATE_AMT, "issuance amtRate too big");
             require(params.pctRate <= MAX_THROTTLE_PCT_AMT, "issuance pctRate too big");
             issuanceThrottle.useAvailable(totalSupply(), 0);
     
             emit IssuanceThrottleSet(issuanceThrottle.params, params);

462:     function setRedemptionThrottleParams(ThrottleLib.Params calldata params) public governance {
             require(params.amtRate >= MIN_THROTTLE_RATE_AMT, "redemption amtRate too small");
             require(params.amtRate <= MAX_THROTTLE_RATE_AMT, "redemption amtRate too big");
             require(params.pctRate <= MAX_THROTTLE_PCT_AMT, "redemption pctRate too big");
             redemptionThrottle.useAvailable(totalSupply(), 0);
     
             emit RedemptionThrottleSet(redemptionThrottle.params, params);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

970:     function setUnstakingDelay(uint48 val) public {
             _requireGovernanceOnly();
             require(val > MIN_UNSTAKING_DELAY && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
             emit UnstakingDelaySet(unstakingDelay, val);

978:     function setRewardRatio(uint192 val) public {
             _requireGovernanceOnly();
             _payoutRewards();
             require(val <= MAX_REWARD_RATIO, "invalid rewardRatio");
             emit RewardRatioSet(rewardRatio, val);

987:     function setWithdrawalLeak(uint192 val) public {
             _requireGovernanceOnly();
             require(val <= MAX_WITHDRAWAL_LEAK, "invalid withdrawalLeak");
             emit WithdrawalLeakSet(withdrawalLeak, val);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

94:     function settleTrade(IERC20 sell) public virtual nonReentrant returns (ITrade trade) {
            trade = trades[sell];
            require(address(trade) != address(0), "no trade open");
            require(trade.canSettle(), "cannot settle yet");
    
            delete trades[sell];
            tradesOpen--;
    
            // == Interactions ==
            (uint256 soldAmt, uint256 boughtAmt) = trade.settle();
            emit TradeSettled(trade, sell, trade.buy(), soldAmt, boughtAmt);

146:     function setMaxTradeSlippage(uint192 val) public governance {
             require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");
             emit MaxTradeSlippageSet(maxTradeSlippage, val);

153:     function setMinTradeVolume(uint192 val) public governance {
             require(val <= MAX_TRADE_VOLUME, "invalid minTradeVolume");
             emit MinTradeVolumeSet(minTradeVolume, val);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

51:     function updateVersionsByAsset(
            address _asset,
            bytes32[] calldata _versionHashes,
            bool[] calldata _validities
        ) external {
            if (!roleRegistry.isOwner(msg.sender)) {
                revert AssetPluginRegistry__InvalidCaller();
            }
            if (_versionHashes.length != _validities.length) {
                revert AssetPluginRegistry__LengthMismatch();
            }
    
            if (_asset == address(0)) {
                revert AssetPluginRegistry__InvalidAsset();
            }
    
            for (uint256 i = 0; i < _versionHashes.length; ++i) {
                bytes32 versionHash = _versionHashes[i];
                if (address(versionRegistry.deployments(versionHash)) == address(0)) {
                    revert AssetPluginRegistry__InvalidVersion();
                }
    
                _isValidAsset[versionHash][_asset] = _validities[i];
    
                emit AssetPluginRegistryUpdated(versionHash, _asset, _validities[i]);

79:     function updateAssetsByVersion(
            bytes32 _versionHash,
            address[] calldata _assets,
            bool[] calldata _validities
        ) external {
            if (!roleRegistry.isOwner(msg.sender)) {
                revert AssetPluginRegistry__InvalidCaller();
            }
            if (_assets.length != _validities.length) {
                revert AssetPluginRegistry__LengthMismatch();
            }
    
            if (address(versionRegistry.deployments(_versionHash)) == address(0)) {
                revert AssetPluginRegistry__InvalidVersion();
            }
    
            for (uint256 i = 0; i < _assets.length; ++i) {
                address asset = _assets[i];
                if (asset == address(0)) {
                    revert AssetPluginRegistry__InvalidAsset();
                }
    
                _isValidAsset[_versionHash][asset] = _validities[i];
    
                emit AssetPluginRegistryUpdated(_versionHash, asset, _validities[i]);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

44:     function setFeeRecipient(address feeRecipient_) external onlyOwner {
            if (feeRecipient_ == address(0)) {
                revert DAOFeeRegistry__InvalidFeeRecipient();
            }
            if (feeRecipient_ == feeRecipient) {
                revert DAOFeeRegistry__FeeRecipientAlreadySet();
            }
    
            feeRecipient = feeRecipient_;
            emit FeeRecipientSet(feeRecipient_);

56:     function setDefaultFeeNumerator(uint256 feeNumerator_) external onlyOwner {
            if (feeNumerator_ > MAX_FEE_NUMERATOR) {
                revert DAOFeeRegistry__InvalidFeeNumerator();
            }
    
            defaultFeeNumerator = feeNumerator_;
            emit DefaultFeeNumeratorSet(defaultFeeNumerator);

66:     function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {
            if (feeNumerator_ > MAX_FEE_NUMERATOR) {
                revert DAOFeeRegistry__InvalidFeeNumerator();
            }
    
            rTokenFeeNumerator[rToken] = feeNumerator_;
            rTokenFeeSet[rToken] = true;
            emit RTokenFeeNumeratorSet(rToken, feeNumerator_, true);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="NC-15"></a>[NC-15] Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*Instances (15)*:
```solidity
File: ./contracts/mixins/Auth.sol

1: 
   Current order:
   internal __Auth_init
   public grantRole
   public frozen
   public tradingPausedOrFrozen
   public issuancePausedOrFrozen
   external freezeShort
   external freezeLong
   external freezeForever
   external unfreeze
   external pauseTrading
   external unpauseTrading
   external pauseIssuance
   external unpauseIssuance
   public setShortFreeze
   public setLongFreeze
   private freezeUntil
   
   Suggested order:
   external freezeShort
   external freezeLong
   external freezeForever
   external unfreeze
   external pauseTrading
   external unpauseTrading
   external pauseIssuance
   external unpauseIssuance
   public grantRole
   public frozen
   public tradingPausedOrFrozen
   public issuancePausedOrFrozen
   public setShortFreeze
   public setLongFreeze
   internal __Auth_init
   private freezeUntil

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

1: 
   Current order:
   external init
   public refresh
   external register
   external swapRegistered
   external unregister
   external toAsset
   external toColl
   external isRegistered
   external erc20s
   public getRegistry
   external validateCurrentAssets
   external size
   internal _register
   private _registerIgnoringCollisions
   private _reserveGas
   
   Suggested order:
   external init
   external register
   external swapRegistered
   external unregister
   external toAsset
   external toColl
   external isRegistered
   external erc20s
   external validateCurrentAssets
   external size
   public refresh
   public getRegistry
   internal _register
   private _registerIgnoringCollisions
   private _reserveGas

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

1: 
   Current order:
   external init
   external grantRTokenAllowance
   public settleTrade
   external rebalance
   external forwardRevenue
   public tradingContext
   private compromiseBasketsNeeded
   public setTradingDelay
   public setBackingBuffer
   public cacheComponents
   
   Suggested order:
   external init
   external grantRTokenAllowance
   external rebalance
   external forwardRevenue
   public settleTrade
   public tradingContext
   public setTradingDelay
   public setBackingBuffer
   public cacheComponents
   private compromiseBasketsNeeded

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

1: 
   Current order:
   external init
   external disableBasket
   external refreshBasket
   public trackStatus
   external setPrimeBasket
   external forceSetPrimeBasket
   internal _setPrimeBasket
   external setBackupConfig
   public fullyCollateralized
   public status
   external isReady
   public quantity
   public quantityUnsafe
   public issuancePremium
   internal _quantity
   external price
   public price
   external quote
   public quote
   external quoteCustomRedemption
   public basketsHeldBy
   public setWarmupPeriod
   public setIssuancePremiumEnabled
   private requireGovernanceOnly
   private _switchBasket
   private requireValidCollArray
   external getHistoricalBasket
   external getPrimeBasket
   external getBackupConfig
   
   Suggested order:
   external init
   external disableBasket
   external refreshBasket
   external setPrimeBasket
   external forceSetPrimeBasket
   external setBackupConfig
   external isReady
   external price
   external quote
   external quoteCustomRedemption
   external getHistoricalBasket
   external getPrimeBasket
   external getBackupConfig
   public trackStatus
   public fullyCollateralized
   public status
   public quantity
   public quantityUnsafe
   public issuancePremium
   public price
   public quote
   public basketsHeldBy
   public setWarmupPeriod
   public setIssuancePremiumEnabled
   internal _setPrimeBasket
   internal _quantity
   private requireGovernanceOnly
   private _switchBasket
   private requireValidCollArray

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

1: 
   Current order:
   external init
   public cacheComponents
   external openTrade
   external reportViolation
   public setGnosis
   public setBatchTradeImplementation
   public setBatchAuctionLength
   public setDutchTradeImplementation
   public setDutchAuctionLength
   external enableBatchTrade
   external enableDutchTrade
   private newBatchAuction
   private newDutchAuction
   private pricedAtTimestamp
   
   Suggested order:
   external init
   external openTrade
   external reportViolation
   external enableBatchTrade
   external enableDutchTrade
   public cacheComponents
   public setGnosis
   public setBatchTradeImplementation
   public setBatchAuctionLength
   public setDutchTradeImplementation
   public setDutchAuctionLength
   private newBatchAuction
   private newDutchAuction
   private pricedAtTimestamp

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

1: 
   Current order:
   external init
   external setDistribution
   external setDistributions
   external distribute
   public totals
   internal _setDistribution
   internal _ensureSufficientTotal
   public cacheComponents
   
   Suggested order:
   external init
   external setDistribution
   external setDistributions
   external distribute
   public totals
   public cacheComponents
   internal _setDistribution
   internal _ensureSufficientTotal

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Main.sol

1: 
   Current order:
   public init
   external poke
   external setVersionRegistry
   external setAssetPluginRegistry
   external setDAOFeeRegistry
   public hasRole
   external upgradeMainTo
   external upgradeRTokenTo
   internal _authorizeUpgrade
   internal _upgradeProxy
   
   Suggested order:
   external poke
   external setVersionRegistry
   external setAssetPluginRegistry
   external setDAOFeeRegistry
   external upgradeMainTo
   external upgradeRTokenTo
   public init
   public hasRole
   internal _authorizeUpgrade
   internal _upgradeProxy

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

1: 
   Current order:
   external init
   public issue
   public issueTo
   external redeem
   public redeemTo
   external redeemCustom
   external mint
   external melt
   external dissolve
   external setBasketsNeeded
   external monetizeDonations
   external issuanceAvailable
   external redemptionAvailable
   external issuanceThrottleParams
   external redemptionThrottleParams
   public setIssuanceThrottleParams
   public setRedemptionThrottleParams
   private _scaleUp
   private _scaleDown
   internal _beforeTokenTransfer
   
   Suggested order:
   external init
   external redeem
   external redeemCustom
   external mint
   external melt
   external dissolve
   external setBasketsNeeded
   external monetizeDonations
   external issuanceAvailable
   external redemptionAvailable
   external issuanceThrottleParams
   external redemptionThrottleParams
   public issue
   public issueTo
   public redeemTo
   public setIssuanceThrottleParams
   public setRedemptionThrottleParams
   internal _beforeTokenTransfer
   private _scaleUp
   private _scaleDown

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

1: 
   Current order:
   external init
   public cacheComponents
   public settleTrade
   external distributeTokenToBuy
   external returnTokens
   external manageTokens
   internal _distributeTokenToBuy
   
   Suggested order:
   external init
   external distributeTokenToBuy
   external returnTokens
   external manageTokens
   public cacheComponents
   public settleTrade
   internal _distributeTokenToBuy

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

1: 
   Current order:
   external init
   external payoutRewards
   public stake
   external unstake
   external withdraw
   external cancelUnstake
   external seizeRSR
   external resetStakes
   public exchangeRate
   external endIdForWithdraw
   external draftQueueLen
   external getDraftRSR
   external getStakeRSR
   external getTotalDrafts
   external getDraftEra
   internal _payoutRewards
   internal pushDraft
   internal beginEra
   internal beginDraftEra
   internal rsrRewards
   private leakyRefresh
   private mintStakes
   private _requireNotTradingPausedOrFrozen
   private _requireNotFrozen
   private _requireGovernanceOnly
   public totalSupply
   public balanceOf
   public allowance
   public transfer
   public approve
   public transferFrom
   public increaseAllowance
   public decreaseAllowance
   internal _transfer
   internal _mint
   internal _burn
   internal _approve
   internal _spendAllowance
   internal _afterTokenTransfer
   internal _notZero
   internal _notZero
   public permit
   public nonces
   public delegationNonces
   external DOMAIN_SEPARATOR
   internal _useNonce
   internal _useDelegationNonce
   public setUnstakingDelay
   public setRewardRatio
   public setWithdrawalLeak
   
   Suggested order:
   external init
   external payoutRewards
   external unstake
   external withdraw
   external cancelUnstake
   external seizeRSR
   external resetStakes
   external endIdForWithdraw
   external draftQueueLen
   external getDraftRSR
   external getStakeRSR
   external getTotalDrafts
   external getDraftEra
   external DOMAIN_SEPARATOR
   public stake
   public exchangeRate
   public totalSupply
   public balanceOf
   public allowance
   public transfer
   public approve
   public transferFrom
   public increaseAllowance
   public decreaseAllowance
   public permit
   public nonces
   public delegationNonces
   public setUnstakingDelay
   public setRewardRatio
   public setWithdrawalLeak
   internal _payoutRewards
   internal pushDraft
   internal beginEra
   internal beginDraftEra
   internal rsrRewards
   internal _transfer
   internal _mint
   internal _burn
   internal _approve
   internal _spendAllowance
   internal _afterTokenTransfer
   internal _notZero
   internal _notZero
   internal _useNonce
   internal _useDelegationNonce
   private leakyRefresh
   private mintStakes
   private _requireNotTradingPausedOrFrozen
   private _requireNotFrozen
   private _requireGovernanceOnly

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

1: 
   Current order:
   internal beginEra
   public clock
   public CLOCK_MODE
   external currentEra
   public checkpoints
   public numCheckpoints
   public delegates
   public getVotes
   public getPastVotes
   public getPastTotalSupply
   public getPastEra
   private _checkpointsLookup
   public delegate
   public delegateBySig
   external stakeAndDelegate
   internal _mint
   internal _burn
   internal _afterTokenTransfer
   internal _delegate
   private _moveVotingPower
   private _writeCheckpoint
   private _add
   private _subtract
   private _requireValidTimepoint
   
   Suggested order:
   external currentEra
   external stakeAndDelegate
   public clock
   public CLOCK_MODE
   public checkpoints
   public numCheckpoints
   public delegates
   public getVotes
   public getPastVotes
   public getPastTotalSupply
   public getPastEra
   public delegate
   public delegateBySig
   internal beginEra
   internal _mint
   internal _burn
   internal _afterTokenTransfer
   internal _delegate
   private _checkpointsLookup
   private _moveVotingPower
   private _writeCheckpoint
   private _add
   private _subtract
   private _requireValidTimepoint

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

1: 
   Current order:
   internal empty
   internal setFrom
   internal add
   external nextBasket
   private goodCollateral
   external requireConstantConfigTargets
   external normalizeByPrice
   
   Suggested order:
   external nextBasket
   external requireConstantConfigTargets
   external normalizeByPrice
   internal empty
   internal setFrom
   internal add
   private goodCollateral

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

1: 
   Current order:
   internal __Trading_init
   internal requireNotTradingPausedOrFrozen
   external claimRewards
   external claimRewardsSingle
   public settleTrade
   internal tryTrade
   public setMaxTradeSlippage
   public setMinTradeVolume
   
   Suggested order:
   external claimRewards
   external claimRewardsSingle
   public settleTrade
   public setMaxTradeSlippage
   public setMinTradeVolume
   internal __Trading_init
   internal requireNotTradingPausedOrFrozen
   internal tryTrade

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

1: 
   Current order:
   public votingDelay
   public votingPeriod
   public setVotingDelay
   public proposalThreshold
   public quorum
   public state
   public propose
   public queue
   public cancel
   internal _execute
   internal _cancel
   internal _executor
   internal _getVotes
   public supportsInterface
   private startedInSameEra
   private requireValidVotingDelay
   public clock
   public CLOCK_MODE
   
   Suggested order:
   public votingDelay
   public votingPeriod
   public setVotingDelay
   public proposalThreshold
   public quorum
   public state
   public propose
   public queue
   public cancel
   public supportsInterface
   public clock
   public CLOCK_MODE
   internal _execute
   internal _cancel
   internal _executor
   internal _getVotes
   private startedInSameEra
   private requireValidVotingDelay

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

1: 
   Current order:
   external dutchTradeCallback
   public lot
   external bidAmount
   external init
   external bid
   external bidWithCallback
   external settle
   external transferToOriginAfterTradeComplete
   external canSettle
   private _price
   public _bidAmount
   
   Suggested order:
   external dutchTradeCallback
   external bidAmount
   external init
   external bid
   external bidWithCallback
   external settle
   external transferToOriginAfterTradeComplete
   external canSettle
   public lot
   public _bidAmount
   private _price

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-16"></a>[NC-16] Functions should not be longer than 50 lines
Overly complex code can make understanding functionality more difficult, try to further modularize your code to ensure readability 

*Instances (225)*:
```solidity
File: ./contracts/libraries/Allowance.sol

5:     function approve(address spender, uint256 value) external;

7:     function allowance(address owner, address spender) external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/libraries/Array.sol

9:     function allUnique(IERC20[] memory arr) internal pure returns (bool) {

21:     function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/Fixed.sol

15:     Unless a function explicitly says otherwise, it will fail on overflow.

73: function _safeWrap(uint256 x) pure returns (uint192) {

81: function toFix(uint256 x) pure returns (uint192) {

89: function shiftl_toFix(uint256 x, int8 shiftLeft) pure returns (uint192) {

117: function divFix(uint256 x, uint192 y) pure returns (uint192) {

130: function divuu(uint256 x, uint256 y) pure returns (uint192) {

136: function fixMin(uint192 x, uint192 y) pure returns (uint192) {

142: function fixMax(uint192 x, uint192 y) pure returns (uint192) {

183:     function toUint(uint192 x) internal pure returns (uint136) {

190:     function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {

198:     function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {

223:     function plus(uint192 x, uint192 y) internal pure returns (uint192) {

230:     function plusu(uint192 x, uint256 y) internal pure returns (uint192) {

237:     function minus(uint192 x, uint192 y) internal pure returns (uint192) {

244:     function minusu(uint192 x, uint256 y) internal pure returns (uint192) {

252:     function mul(uint192 x, uint192 y) internal pure returns (uint192) {

270:     function mulu(uint192 x, uint256 y) internal pure returns (uint192) {

277:     function div(uint192 x, uint192 y) internal pure returns (uint192) {

296:     function divu(uint192 x, uint256 y) internal pure returns (uint192) {

317:     function powu(uint192 x_, uint48 y) internal pure returns (uint192) {

332:     function sqrt(uint192 x) internal pure returns (uint192) {

337:     function lt(uint192 x, uint192 y) internal pure returns (bool) {

341:     function lte(uint192 x, uint192 y) internal pure returns (bool) {

345:     function gt(uint192 x, uint192 y) internal pure returns (bool) {

349:     function gte(uint192 x, uint192 y) internal pure returns (bool) {

353:     function eq(uint192 x, uint192 y) internal pure returns (bool) {

357:     function neq(uint192 x, uint192 y) internal pure returns (bool) {

380:     function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {

406:     function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {

424:     function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {

674: function fullMul(uint256 x, uint256 y) pure returns (uint256 hi, uint256 lo) {

694: function sqrt256(uint256 x) pure returns (uint256 result) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/String.sol

11:     function toLower(string memory str) internal pure returns (string memory) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

69:     function currentlyAvailable(Throttle storage throttle, uint256 limit)

80:     function hourlyLimit(Throttle storage throttle, uint256 supply)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

68:     function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

105:     function tradingPausedOrFrozen() public view returns (bool) {

111:     function issuancePausedOrFrozen() public view returns (bool) {

124:     function freezeShort() external onlyRole(SHORT_FREEZER) {

139:     function freezeLong() external onlyRole(LONG_FREEZER) {

152:     function freezeForever() external onlyRole(OWNER) {

169:     function pauseTrading() external onlyRole(PAUSER) {

176:     function unpauseTrading() external onlyRole(PAUSER) {

183:     function pauseIssuance() external onlyRole(PAUSER) {

190:     function unpauseIssuance() external onlyRole(PAUSER) {

198:     function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

205:     function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

214:     function freezeUntil(uint48 newUnfreezeAt) private {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

18:     function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {

51:     function _setAssetRegistry(IAssetRegistry val) private {

59:     function _setBasketHandler(IBasketHandler val) private {

67:     function _setBackingManager(IBackingManager val) private {

75:     function _setDistributor(IDistributor val) private {

83:     function _setRSRTrader(IRevenueTrader val) private {

91:     function _setRTokenTrader(IRevenueTrader val) private {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/mixins/Versioned.sol

14:     function version() public pure virtual override returns (string memory) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Versioned.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

42:     function init(IMain main_, IAsset[] calldata assets_) external initializer {

78:     function register(IAsset asset) external governance returns (bool) {

90:     function swapRegistered(IAsset asset) external governance returns (bool swapped) {

108:     function unregister(IAsset asset) external governance {

126:     function toAsset(IERC20 erc20) external view returns (IAsset) {

134:     function toColl(IERC20 erc20) external view returns (ICollateral) {

142:     function isRegistered(IERC20 erc20) external view returns (bool) {

148:     function erc20s() external view returns (IERC20[] memory erc20s_) {

159:     function getRegistry() public view returns (Registry memory reg) {

201:     function _register(IAsset asset) internal returns (bool registered) {

213:     function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {

253:     function _reserveGas() private view returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

69:     function grantRTokenAllowance(IERC20 erc20) external notFrozen {

85:     function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {

107:     function rebalance(TradeKind kind) external nonReentrant {

179:     function forwardRevenue(IERC20[] calldata erc20s) external nonReentrant {

273:     function tradingContext(BasketRange memory basketsHeld)

306:     function compromiseBasketsNeeded(uint192 basketsHeldBottom) private {

315:     function setTradingDelay(uint48 val) public governance {

322:     function setBackingBuffer(uint192 val) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

197:     function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {

207:     function forceSetPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {

307:     function fullyCollateralized() public view returns (bool) {

314:     function status() public view returns (CollateralStatus status_) {

343:     function quantity(IERC20 erc20) public view returns (uint192) {

359:     function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {

366:     function issuancePremium(ICollateral coll) public view returns (uint192) {

409:     function price() external view returns (uint192 low, uint192 high) {

419:     function price(bool applyIssuancePremium) public view returns (uint192 low, uint192 high) {

467:     function quote(uint192 amount, RoundingMode rounding)

610:     function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {

641:     function setIssuancePremiumEnabled(bool val) public {

651:     function requireGovernanceOnly() private governance {}

683:     function requireValidCollArray(IERC20[] calldata erc20s) private view {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

178:     function setGnosis(IGnosis newGnosis) public governance {

186:     function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

197:     function setBatchAuctionLength(uint48 newAuctionLength) public governance {

208:     function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

219:     function setDutchAuctionLength(uint48 newAuctionLength) public governance {

236:     function enableDutchTrade(IERC20Metadata erc20) external governance {

243:     function newBatchAuction(TradeRequest memory req, address caller) private returns (ITrade) {

298:     function pricedAtTimestamp(IAsset asset) private view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

74:     function implementations() external view override returns (Implementations memory) {

269:     function deployRTokenAsset(IRToken rToken, uint192 maxTradeVolume)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

44:     function init(IMain main_, RevenueShare calldata dist) external initializer {

61:     function setDistribution(address dest, RevenueShare calldata share) external governance {

81:     function setDistributions(address[] calldata dests, RevenueShare[] calldata shares)

120:     function distribute(IERC20 erc20, uint256 amount) external {

204:     function totals() public view returns (RevenueTotals memory revTotals) {

240:     function _setDistribution(address dest, RevenueShare memory share) internal {

265:     function _ensureSufficientTotal(uint24 rTokenTotal, uint24 rsrTotal) internal pure {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

33:     function init(IMain main_, uint192 ratio_) external initializer {

83:     function setRatio(uint192 ratio_) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

61:     function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {

70:     function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {

79:     function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {

99:     function upgradeMainTo(bytes32 versionHash) external onlyRole(OWNER) {

153:     function _authorizeUpgrade(address) internal view override {

157:     function _upgradeProxy(address proxy, address implementation) internal {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

105:     function issueTo(address recipient, uint256 amount) public notIssuancePausedOrFrozen {

183:     function redeemTo(address recipient, uint256 amount) public notFrozen {

397:     function setBasketsNeeded(uint192 basketsNeeded_) external notTradingPausedOrFrozen {

418:     function monetizeDonations(IERC20 erc20) external notTradingPausedOrFrozen {

429:     function issuanceAvailable() external view returns (uint256) {

434:     function redemptionAvailable() external view returns (uint256 available) {

441:     function issuanceThrottleParams() external view returns (ThrottleLib.Params memory) {

446:     function redemptionThrottleParams() external view returns (ThrottleLib.Params memory) {

451:     function setIssuanceThrottleParams(ThrottleLib.Params calldata params) public governance {

462:     function setRedemptionThrottleParams(ThrottleLib.Params calldata params) public governance {

508:     function _scaleDown(address account, uint256 amtRToken) private returns (uint192 amtBaskets) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

54:     function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {

70:     function distributeTokenToBuy() external notTradingPausedOrFrozen {

76:     function returnTokens(IERC20[] memory erc20s) external notTradingPausedOrFrozen {

109:     function manageTokens(IERC20[] calldata erc20s, TradeKind[] calldata kinds)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

304:     function withdraw(address account, uint256 endId) external {

502:     function exchangeRate() public view returns (uint192) {

512:     function endIdForWithdraw(address account) external view returns (uint256) {

539:     function draftQueueLen(uint256 era_, address account) external view returns (uint256) {

544:     function getDraftRSR() external view returns (uint256) {

549:     function getStakeRSR() external view returns (uint256) {

554:     function getTotalDrafts() external view returns (uint256) {

559:     function getDraftEra() external view returns (uint256) {

640:     function pushDraft(address account, uint256 rsrAmount)

693:     function rsrRewards() internal view returns (uint256) {

700:     function leakyRefresh(uint256 rsrWithdrawal) private {

722:     function mintStakes(address account, uint256 rsrAmount) private {

740:     function _requireNotTradingPausedOrFrozen() private notTradingPausedOrFrozen {}

748:     function _requireGovernanceOnly() private governance {}

754:     function totalSupply() public view returns (uint256) {

758:     function balanceOf(address account) public view returns (uint256) {

762:     function allowance(address owner, address spender)

772:     function transfer(address to, uint256 amount) public returns (bool) {

781:     function approve(address spender, uint256 amount) public returns (bool) {

800:     function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {

806:     function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {

842:     function _mint(address account, uint256 amount) internal virtual {

856:     function _burn(address account, uint256 amount) internal virtual {

942:     function nonces(address owner) public view returns (uint256) {

946:     function delegationNonces(address owner) public view returns (uint256) {

951:     function DOMAIN_SEPARATOR() external view returns (bytes32) {

955:     function _useNonce(address owner) internal returns (uint256 current) {

961:     function _useDelegationNonce(address owner) internal returns (uint256 current) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

66:     function CLOCK_MODE() public pure returns (string memory) {

70:     function currentEra() external view returns (uint256) {

74:     function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {

78:     function numCheckpoints(address account) public view returns (uint48) {

82:     function delegates(address account) public view returns (address) {

86:     function getVotes(address account) public view returns (uint256) {

91:     function getPastVotes(address account, uint256 timepoint) public view returns (uint256) {

98:     function getPastTotalSupply(uint256 timepoint) public view returns (uint256) {

105:     function getPastEra(uint256 timepoint) public view returns (uint256) {

112:     function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 timepoint)

187:     function stakeAndDelegate(uint256 rsrAmount, address delegatee) external {

201:     function _mint(address account, uint256 amount) internal override {

206:     function _burn(address account, uint256 amount) internal override {

220:     function _delegate(address delegator, address delegatee) internal {

259:         function(uint256, uint256) view returns (uint256) op,

283:     function _add(uint256 a, uint256 b) private pure returns (uint256) {

287:     function _subtract(uint256 a, uint256 b) private pure returns (uint256) {

291:     function _requireValidTimepoint(uint256 timepoint) private view {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

77:     function setFrom(Basket storage self, Basket storage other) internal {

112:        - the function (isGood: erc20 -> bool), implemented here by goodCollateral()

113:        - the function (targetPerRef: erc20 -> Fix) implemented by the Collateral plugin

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

33:     function __Component_init(IMain main_) internal onlyInitializing {

67:     function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

33:     function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)

108:     function basketRange(TradingContext memory ctx, Registry memory reg)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

24:     function claimRewards(IAssetRegistry reg) internal {

39:     function claimRewardsSingle(IAsset asset) internal {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

174:     function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

60:     function requireNotTradingPausedOrFrozen() internal view notTradingPausedOrFrozen {}

74:     function claimRewardsSingle(IERC20 erc20) external {

94:     function settleTrade(IERC20 sell) public virtual nonReentrant returns (ITrade trade) {

146:     function setMaxTradeSlippage(uint192 val) public governance {

153:     function setMinTradeVolume(uint192 val) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

55:     function votingDelay() public view override(IGovernor, GovernorSettings) returns (uint256) {

59:     function votingPeriod() public view override(IGovernor, GovernorSettings) returns (uint256) {

63:     function setVotingDelay(uint256 newVotingDelay) public override {

185:     function startedInSameEra(uint256 proposalId) private view returns (bool) {

192:     function requireValidVotingDelay(uint256 newVotingDelay) private pure {

196:     function clock() public view override(GovernorVotes, IGovernor) returns (uint48) {

201:     function CLOCK_MODE() public pure override(GovernorVotes, IGovernor) returns (string memory) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

139:     function bidAmount(uint48 timestamp) external view returns (uint256) {

199:     function bid() external returns (uint256 amountIn) {

235:     function bidWithCallback(bytes calldata data) external returns (uint256 amountIn) {

298:     function transferToOriginAfterTradeComplete(IERC20Metadata erc20) external {

305:     function canSettle() external view returns (bool) {

314:     function _price(uint48 timestamp) private view returns (uint192) {

365:     function _bidAmount(uint192 price) public view returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

225:     function transferToOriginAfterTradeComplete(IERC20 erc20) external {

232:     function canSettle() external view returns (bool) {

238:     function isAuctionCleared() private view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

31:     function registerAsset(address _asset, bytes32[] calldata validForVersions) external {

107:     function deprecateAsset(address _asset) external {

115:     function isValidAsset(bytes32 _versionHash, address _asset) external view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

44:     function setFeeRecipient(address feeRecipient_) external onlyOwner {

56:     function setDefaultFeeNumerator(uint256 feeNumerator_) external onlyOwner {

66:     function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {

76:     function resetRTokenFee(address rToken) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

18:     function isOwner(address account) public view returns (bool) {

22:     function isEmergencyCouncil(address account) public view returns (bool) {

26:     function isOwnerOrEmergencyCouncil(address account) public view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

37:     function registerVersion(IDeployer deployer) external {

59:     function deprecateVersion(bytes32 versionHash) external {

88:     function getImplementationForVersion(bytes32 versionHash)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-17"></a>[NC-17] Change int to int256
Throughout the code base, some variables are declared as `int`. To favor explicitness, consider changing all instances of `int` to `int256`

*Instances (9)*:
```solidity
File: ./contracts/libraries/Fixed.sol

49: uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

25:     struct Checkpoint {

74:     function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {

141:             if (ckpts[mid].fromTimepoint > timepoint) {

150:             if (ckpts[mid].fromTimepoint > timepoint) {

265:             Checkpoint memory oldCkpt = pos == 0 ? Checkpoint(0, 0) : ckpts[pos - 1];

270:             if (pos != 0 && oldCkpt.fromTimepoint == clock()) {

292:         require(timepoint < block.timestamp, "future lookup");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

186:         uint256 startTimepoint = proposalSnapshot(proposalId);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

### <a name="NC-18"></a>[NC-18] Change uint to uint256
Throughout the code base, some variables are declared as `uint`. To favor explicitness, consider changing all instances of `uint` to `uint256`

*Instances (1)*:
```solidity
File: ./contracts/libraries/Fixed.sol

397:         decimals -= 18; // shift so that toUint happens at the same time.

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

### <a name="NC-19"></a>[NC-19] Interfaces should be defined in separate files from their usage
The interfaces below should be defined in separate files, so that it's easier for future projects to import them, and to avoid duplication later on if they need to be used elsewhere in the project

*Instances (2)*:
```solidity
File: ./contracts/libraries/Allowance.sol

4: interface IERC20ApproveOnly {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

12: interface IDutchTradeCallee {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-20"></a>[NC-20] Lack of checks in setters
Be it sanity checks (like checks against `0`-values) or initial setting checks: it's best for Setter functions to have them

*Instances (7)*:
```solidity
File: ./contracts/p1/BasketHandler.sol

197:     function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {
             _setPrimeBasket(erc20s, targetAmts, false);

641:     function setIssuancePremiumEnabled(bool val) public {
             requireGovernanceOnly();
             emit EnableIssuancePremiumSet(enableIssuancePremium, val);
             enableIssuancePremium = val;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Deployer.sol

269:     function deployRTokenAsset(IRToken rToken, uint192 maxTradeVolume)
             external
             returns (IAsset rTokenAsset)
         {
             rTokenAsset = new RTokenAsset(rToken, maxTradeVolume);
             emit RTokenAssetCreated(rToken, rTokenAsset);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

61:     function setDistribution(address dest, RevenueShare calldata share) external governance {
            // solhint-disable-next-line no-empty-blocks
            try main.rsrTrader().distributeTokenToBuy() {} catch {}
            // solhint-disable-next-line no-empty-blocks
            try main.rTokenTrader().distributeTokenToBuy() {} catch {}
    
            _setDistribution(dest, share);
    
            RevenueTotals memory revTotals = totals();
            _ensureSufficientTotal(revTotals.rTokenTotal, revTotals.rsrTotal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

77:     function setFrom(Basket storage self, Basket storage other) internal {
            empty(self);
            uint256 length = other.erc20s.length;
            for (uint256 i = 0; i < length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

63:     function setVotingDelay(uint256 newVotingDelay) public override {
            requireValidVotingDelay(newVotingDelay);
            super.setVotingDelay(newVotingDelay); // has onlyGovernance modifier

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

76:     function resetRTokenFee(address rToken) external onlyOwner {
            rTokenFeeNumerator[rToken] = 0;
            rTokenFeeSet[rToken] = false;
    
            emit RTokenFeeNumeratorSet(rToken, 0, false);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="NC-21"></a>[NC-21] Missing Event for critical parameters change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes.

*Instances (13)*:
```solidity
File: ./contracts/p1/BackingManager.sol

85:     function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {
            delete tokensOut[sell];
            trade = super.settleTrade(sell); // nonReentrant
    
            // if the settler is the trade contract itself, try chaining with another rebalance()
            if (_msgSender() == address(trade)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

197:     function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts) external {
             _setPrimeBasket(erc20s, targetAmts, false);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Distributor.sol

61:     function setDistribution(address dest, RevenueShare calldata share) external governance {
            // solhint-disable-next-line no-empty-blocks
            try main.rsrTrader().distributeTokenToBuy() {} catch {}
            // solhint-disable-next-line no-empty-blocks
            try main.rTokenTrader().distributeTokenToBuy() {} catch {}
    
            _setDistribution(dest, share);
    
            RevenueTotals memory revTotals = totals();
            _ensureSufficientTotal(revTotals.rTokenTotal, revTotals.rsrTotal);

81:     function setDistributions(address[] calldata dests, RevenueShare[] calldata shares)
            external
            governance
        {
            require(dests.length == shares.length, "array length mismatch");
    
            // solhint-disable-next-line no-empty-blocks
            try main.rsrTrader().distributeTokenToBuy() {} catch {}
            // solhint-disable-next-line no-empty-blocks
            try main.rTokenTrader().distributeTokenToBuy() {} catch {}
    
            for (uint256 i = 0; i < dests.length; ++i) {
                _setDistribution(dests[i], shares[i]);
            }
    
            RevenueTotals memory revTotals = totals();
            _ensureSufficientTotal(revTotals.rTokenTotal, revTotals.rsrTotal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Main.sol

61:     function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {
            require(address(versionRegistry_) != address(0), "invalid registry address");
            require(address(versionRegistry) == address(0), "already set");
    
            versionRegistry = VersionRegistry(versionRegistry_);

70:     function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {
            require(address(registry_) != address(0), "invalid registry address");
            require(address(assetPluginRegistry) == address(0), "already set");
    
            assetPluginRegistry = AssetPluginRegistry(registry_);

79:     function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {
            require(address(feeRegistry_) != address(0), "invalid registry address");
            require(address(daoFeeRegistry) == address(0), "already set");
    
            daoFeeRegistry = DAOFeeRegistry(feeRegistry_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

54:     function settleTrade(IERC20 sell) public override(ITrading, TradingP1) returns (ITrade trade) {
            trade = super.settleTrade(sell); // nonReentrant
    
            // solhint-disable-next-line no-empty-blocks
            try this.distributeTokenToBuy() {} catch (bytes memory errData) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

490:     function resetStakes() external {
             _requireGovernanceOnly();
             require(
                 stakeRate <= MIN_SAFE_STAKE_RATE || stakeRate >= MAX_SAFE_STAKE_RATE,
                 "rate still safe"
             );
     
             beginEra();
             beginDraftEra();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

63:     function setVotingDelay(uint256 newVotingDelay) public override {
            requireValidVotingDelay(newVotingDelay);
            super.setVotingDelay(newVotingDelay); // has onlyGovernance modifier

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

274:     function settle()
             external
             stateTransition(TradeStatus.OPEN, TradeStatus.CLOSED)
             returns (uint256 soldAmt, uint256 boughtAmt)
         {
             require(msg.sender == address(origin), "only origin can settle");
             require(bidder != address(0) || block.timestamp > endTime, "auction not over");
     
             if (bidType == BidType.CALLBACK) {
                 soldAmt = lot(); // {qSellTok}
             } else if (bidType == BidType.TRANSFER) {
                 soldAmt = lot(); // {qSellTok}
                 sell.safeTransfer(bidder, soldAmt); // {qSellTok}
             }
     
             // Transfer remaining balances back to origin
             boughtAmt = buy.balanceOf(address(this)); // {qBuyTok}
             buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}
             sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

175:     function settle()
             external
             stateTransition(TradeStatus.OPEN, TradeStatus.CLOSED)
             returns (uint256 soldAmt, uint256 boughtAmt)
         {
             require(msg.sender == origin, "only origin can settle");
     
             // Optionally process settlement of the auction in Gnosis
             if (!isAuctionCleared()) {
                 // By design, we don't rely on this return value at all, just the
                 // "cleared" state of the auction, and the token balances this contract owns.
                 // slither-disable-next-line unused-return
                 gnosis.settleAuction(auctionId);
                 assert(isAuctionCleared());
             }
     
             // At this point we know the auction has cleared
     
             // Transfer balances to origin
             uint256 sellBal = sell.balanceOf(address(this));
     
             // As raised in C4's review, this balance can be manupulated by a frontrunner
             // It won't really affect the outcome of the trade, as protocol still gets paid
             // and it just gets a better clearing price than expected.
             // Fixing it would require some complex logic, as SimpleAuction does not expose
             // the amount of tokens bought by the auction after the tokens are settled.
             // So we will live with this for now. Worst case, there will be a mismatch between
             // the trades recorded by the IDO contracts and on our side.
             boughtAmt = buy.balanceOf(address(this));
     
             if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);
             if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);
     
             // Check clearing prices
             if (sellBal < initBal) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

107:     function deprecateAsset(address _asset) external {
             if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {
                 revert AssetPluginRegistry__InvalidCaller();
             }
     
             isDeprecated[_asset] = true;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

### <a name="NC-22"></a>[NC-22] NatSpec is completely non-existent on functions that should have them
Public and external functions that aren't view or pure should have NatSpec comments

*Instances (34)*:
```solidity
File: ./contracts/libraries/Allowance.sol

5:     function approve(address spender, uint256 value) external;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/mixins/Auth.sol

87:     function grantRole(bytes32 role, address account)

169:     function pauseTrading() external onlyRole(PAUSER) {

176:     function unpauseTrading() external onlyRole(PAUSER) {

183:     function pauseIssuance() external onlyRole(PAUSER) {

190:     function unpauseIssuance() external onlyRole(PAUSER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

49:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

108:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

73:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

44:     function init(IMain main_, RevenueShare calldata dist) external initializer {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

33:     function init(IMain main_, uint192 ratio_) external initializer {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

111:     function upgradeRTokenTo(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

61:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

27:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

175:     function init(

772:     function transfer(address to, uint256 amount) public returns (bool) {

800:     function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {

806:     function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {

922:     function permit(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

162:     function delegate(address delegatee) public {

166:     function delegateBySig(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

63:     function setVotingDelay(uint256 newVotingDelay) public override {

104:     function propose(

114:     function queue(

124:     function cancel(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

13:     function dutchTradeCallback(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

31:     function registerAsset(address _asset, bytes32[] calldata validForVersions) external {

51:     function updateVersionsByAsset(

79:     function updateAssetsByVersion(

107:     function deprecateAsset(address _asset) external {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

44:     function setFeeRecipient(address feeRecipient_) external onlyOwner {

56:     function setDefaultFeeNumerator(uint256 feeNumerator_) external onlyOwner {

76:     function resetRTokenFee(address rToken) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

59:     function deprecateVersion(bytes32 versionHash) external {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-23"></a>[NC-23] Incomplete NatSpec: `@param` is missing on actually documented functions
The following functions are missing `@param` NatSpec comments.

*Instances (50)*:
```solidity
File: ./contracts/mixins/Auth.sol

197:     /// @custom:governance
         function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

204:     /// @custom:governance
         function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

40:     /// Initialize the AssetRegistry with assets
        // effects: assets' = {a.erc20(): a for a in assets_}
        function init(IMain main_, IAsset[] calldata assets_) external initializer {

71:     /// Register `asset`
        /// If either the erc20 address or the asset was already registered, fail
        /// @return true if the erc20 address was not already registered.
        /// @custom:governance
        // checks: asset.erc20() not in keys(assets) or assets[asset.erc20] == asset
        // effects: assets' = assets.set(asset.erc20(), asset)
        // returns: (asset.erc20 not in keys(assets))
        function register(IAsset asset) external governance returns (bool) {

82:     /// Register `asset` if and only if its erc20 address is already registered.
        /// If the erc20 address was not registered, revert.
        /// @return swapped If the asset was swapped for a previously-registered asset
        /// @custom:governance
        // contract
        // checks: asset.erc20() in assets
        // effects: assets' = assets + {asset.erc20(): asset}
        // actions: if asset.erc20() is in basketHandler's basket then basketHandler.disableBasket()
        function swapRegistered(IAsset asset) external governance returns (bool swapped) {

102:     /// Unregister an asset, requiring that it is already registered
         /// Rewards are NOT claimed by default when unregistering due to security concerns.
         /// If the collateral is secure, governance should claim rewards before unregistering.
         /// @custom:governance
         // checks: assets[asset.erc20()] == asset
         // effects: assets' = assets - {asset.erc20():_} + {asset.erc20(), asset}
         function unregister(IAsset asset) external governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

64:     /// Give RToken max allowance over the registered token `erc20`
        /// @custom:interaction CEI
        // checks: erc20 in assetRegistry
        // action: set allowance on erc20 for rToken to UINT_MAX
        // Using two safeApprove calls instead of safeIncreaseAllowance to support USDT
        function grantRTokenAllowance(IERC20 erc20) external notFrozen {

314:     /// @custom:governance
         function setTradingDelay(uint48 val) public governance {

321:     /// @custom:governance
         function setBackingBuffer(uint192 val) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

275:     /// Set the backup configuration for some target name
         /// @custom:governance
         // checks:
         //   caller is OWNER
         //   erc20s is a valid collateral array
         //   for all i, erc20[i] is in AssetRegistry as collateral
         //
         // effects:
         //   config'.backups[targetName] = {max: max, erc20s: erc20s}
         function setBackupConfig(
             bytes32 targetName,
             uint256 max,
             IERC20[] calldata erc20s

632:     /// @custom:governance
         function setWarmupPeriod(uint48 val) public {

640:     /// @custom:governance
         function setIssuancePremiumEnabled(bool val) public {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

113:     /// Handle a trade request by deploying a customized disposable trading contract
         /// @param kind TradeKind.DUTCH_AUCTION or TradeKind.BATCH_AUCTION
         /// @dev Requires setting an allowance in advance
         /// @custom:protected and @custom:interaction CEI
         // checks:
         //   caller is a system Trader
         // effects:
         //   Deploys a new trade clone, `trade`
         //   trades'[trade] = true
         // actions:
         //   Transfers req.sellAmount of req.sell.erc20 from caller to `trade`
         //   Calls trade.init() with appropriate parameters
         function openTrade(
             TradeKind kind,
             TradeRequest memory req,
             TradePrices memory prices

177:     /// @custom:governance
         function setGnosis(IGnosis newGnosis) public governance {

185:     /// @custom:main
         function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

196:     /// @custom:governance
         function setBatchAuctionLength(uint48 newAuctionLength) public governance {

207:     /// @custom:main
         function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

218:     /// @custom:governance
         function setDutchAuctionLength(uint48 newAuctionLength) public governance {

235:     /// @custom:governance
         function enableDutchTrade(IERC20Metadata erc20) external governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

266:     /// Deploys a new RTokenAsset instance. Not needed during normal deployment flow
         /// @param maxTradeVolume {UoA} The maximum trade volume for the RTokenAsset
         /// @return rTokenAsset The address of the newly deployed RTokenAsset
         function deployRTokenAsset(IRToken rToken, uint192 maxTradeVolume)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

53:     /// Set the RevenueShare for destination `dest`. Destinations `FURNACE` and `ST_RSR` refer to
        /// main.furnace() and main.stRSR().
        /// Consider calling `BackingManager.forwardRevenue()` before to ensure fair past distribution
        /// @custom:governance
        // checks: invariants hold in post-state
        // effects:
        //   destinations' = destinations.add(dest)
        //   distribution' = distribution.set(dest, share)
        function setDistribution(address dest, RevenueShare calldata share) external governance {

73:     /// Set RevenueShares for destinations. Destinations `FURNACE` and `ST_RSR` refer to
        /// main.furnace() and main.stRSR().
        /// Consider calling `BackingManager.forwardRevenue()` before to ensure fair past distribution
        /// @custom:governance
        // checks: invariants hold in post-state
        // effects:
        //   destinations' = destinations.add(dests[i]) for i < dests.length
        //   distribution' = distribution.set(dests[i], shares[i]) for i < dests.length
        function setDistributions(address[] calldata dests, RevenueShare[] calldata shares)

105:     /// Distribute revenue, in rsr or rtoken, per the distribution table.
         /// Requires that this contract has an allowance of at least
         /// `amount` tokens, from `from`, of the token at `erc20`.
         /// Only callable by RevenueTraders
         /// @custom:protected CEI
         // let:
         //   w = the map such that w[dest] = distribution[dest].{erc20}Shares
         //   tokensPerShare = floor(amount / sum(values(w)))
         //   addrOf(dest) = 1 -> furnace | 2 -> stRSR | x -> x
         // checks:
         //   erc20 is in {rsr, rToken}
         //   sum(values(w)) > 0
         // actions:
         //   for dest where w[dest] != 0:
         //     erc20.transferFrom(from, addrOf(dest), tokensPerShare * w[dest])
         function distribute(IERC20 erc20, uint256 amount) external {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

81:     /// Ratio setting
        /// @custom:governance
        function setRatio(uint192 ratio_) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

59:     /// Set Version Registry
        /// @dev Can only be called once.
        function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {

68:     /// Set Asset Plugin Registry
        /// @dev Can only be called once.
        function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {

77:     /// Set DAO Fee Registry
        /// @dev Can only be called once.
        function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {

95:     /**
         * @dev When upgrading from a prior version to 4.0.0,
         *      this must happen in the Governance proposal.
         */
        function upgradeMainTo(bytes32 versionHash) external onlyRole(OWNER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

393:     /// An affordance of last resort for Main in order to ensure re-capitalization
         /// @custom:protected
         // checks: caller is backingManager
         // effects: basketsNeeded' = basketsNeeded_
         function setBasketsNeeded(uint192 basketsNeeded_) external notTradingPausedOrFrozen {

416:     /// Sends all token balance of erc20 (if it is registered) to the BackingManager
         /// @custom:interaction
         function monetizeDonations(IERC20 erc20) external notTradingPausedOrFrozen {

450:     /// @custom:governance
         function setIssuanceThrottleParams(ThrottleLib.Params calldata params) public governance {

461:     /// @custom:governance
         function setRedemptionThrottleParams(ThrottleLib.Params calldata params) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

74:     /// Return registered ERC20s to the BackingManager if distribution for tokenToBuy is 0
        /// @custom:interaction
        function returnTokens(IERC20[] memory erc20s) external notTradingPausedOrFrozen {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

283:     /// Complete an account's unstaking; callable by anyone
         /// @custom:interaction CEIC - Warning: violates CEI; has checks at the end
         // Let:
         //   r = draft[account]
         //   draftAmount = r.queue[endId - 1].drafts - r.queue[r.left-1].drafts
         //
         // checks:
         //   RToken is fully collateralized and the basket is sound.
         //   The system is not paused (trading) or frozen.
         //   endId <= r.right
         //   r.queue[endId - 1].availableAt <= now
         //
         // effects:
         //   r'.left = max(endId, r.left)
         //   draftSum'(account) = draftSum(account) + draftAmount)
         //   r'.right = r.right
         //   totalDrafts' = totalDrafts - draftAmount
         //   draftRSR' = ceil(totalDrafts' * 1e18 / draftRate)
         //
         // actions:
         //   rsr.transfer(account, rsrOut)
         function withdraw(address account, uint256 endId) external {

344:     /// Cancel an ongoing unstaking; resume staking
         /// @custom:interaction CEI
         function cancelUnstake(uint256 endId) external {

777:     /**
          * NOTE: If `amount` is the maximum `uint256`, the allowance is not updated on
          * `transferFrom`. This is semantically equivalent to an infinite approval.
          */
         function approve(address spender, uint256 amount) public returns (bool) {

786:     /**
          * NOTE: Does not update the allowance if the current allowance
          * is the maximum `uint256`.
          */
         function transferFrom(
             address from,
             address to,
             uint256 amount

969:     /// @custom:governance
         function setUnstakingDelay(uint48 val) public {

977:     /// @custom:governance
         function setRewardRatio(uint192 val) public {

986:     /// @custom:governance
         function setWithdrawalLeak(uint192 val) public {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

185:     /// Stakes an RSR `amount` on the corresponding RToken and allows to delegate
         /// votes from the sender to `delegatee` or self
         function stakeAndDelegate(uint256 rsrAmount, address delegatee) external {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

158:     /// Select next reference basket from basket config
         /// Works in-place on `newBasket`
         /// @param targetNames Scratch space for computation; initial value unused
         /// @param newBasket Scratch space for computation; initial value unused
         /// @param config The current basket configuration
         /// @return success result; i.e newBasket can be expected to contain a valid reference basket
         function nextBasket(
             Basket storage newBasket,
             EnumerableSet.Bytes32Set storage targetNames,
             BasketConfig storage config,
             IAssetRegistry assetRegistry

314:     /// Require that erc20s and targetAmts preserve the current config targets
         /// @param _targetAmts Scratch space for computation; assumed to be empty
         function requireConstantConfigTargets(
             IAssetRegistry assetRegistry,
             BasketConfig storage config,
             EnumerableMap.Bytes32ToUintMap storage _targetAmts,
             IERC20[] calldata erc20s,
             uint192[] calldata targetAmts

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

145:     /// @custom:governance
         function setMaxTradeSlippage(uint192 val) public governance {

152:     /// @custom:governance
         function setMinTradeVolume(uint192 val) public governance {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

151:     /// @param origin_ The Trader that originated the trade
         /// @param sell_ The asset being sold by the protocol
         /// @param buy_ The asset being bought by the protocol
         /// @param sellAmount_ {qSellTok} The amount to sell in the auction, in token quanta
         /// @param auctionLength {s} How many seconds the dutch auction should run for
         function init(
             ITrading origin_,
             IAsset sell_,
             IAsset buy_,
             uint256 sellAmount_,
             uint48 auctionLength,
             TradePrices memory prices

295:     /// Anyone can transfer any ERC20 back to the origin after the trade has been closed
         /// @dev Escape hatch in case of accidentally transferred tokens after auction end
         /// @custom:interaction CEI (and respects the state lock)
         function transferToOriginAfterTradeComplete(IERC20Metadata erc20) external {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

70:     /// Constructor function, can only be called once
        /// @dev Expects sell tokens to already be present
        /// @custom:interaction reentrancy-safe b/c state-locking
        // checks:
        //   state is NOT_STARTED
        //   req.sellAmount <= our balance of sell tokens < 2**96
        //   req.minBuyAmount < 2**96
        // effects:
        //   state' is OPEN
        //   correctly sets all Metadata and Economic parameters of this contract
        //
        // actions:
        //   increases the `req.sell` allowance for `gnosis` by the amount needed to fund the auction
        //   calls gnosis.initiateAuction(...) to launch the requested auction.
        function init(
            IBroker broker_,
            address origin_,
            IGnosis gnosis_,
            uint48 batchAuctionLength,
            TradeRequest calldata req

222:     /// Anyone can transfer any ERC20 back to the origin after the trade has been closed
         /// @dev Escape hatch in case trading partner freezes up, or other unexpected events
         /// @custom:interaction CEI (and respects the state lock)
         function transferToOriginAfterTradeComplete(IERC20 erc20) external {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

65:     /// @dev A fee below 1% not recommended due to poor precision in the Distributor
        function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="NC-24"></a>[NC-24] Incomplete NatSpec: `@return` is missing on actually documented functions
The following functions are missing `@return` NatSpec comments.

*Instances (4)*:
```solidity
File: ./contracts/p1/Broker.sol

113:     /// Handle a trade request by deploying a customized disposable trading contract
         /// @param kind TradeKind.DUTCH_AUCTION or TradeKind.BATCH_AUCTION
         /// @dev Requires setting an allowance in advance
         /// @custom:protected and @custom:interaction CEI
         // checks:
         //   caller is a system Trader
         // effects:
         //   Deploys a new trade clone, `trade`
         //   trades'[trade] = true
         // actions:
         //   Transfers req.sellAmount of req.sell.erc20 from caller to `trade`
         //   Calls trade.init() with appropriate parameters
         function openTrade(
             TradeKind kind,
             TradeRequest memory req,
             TradePrices memory prices
         ) external returns (ITrade) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/StRSR.sol

777:     /**
          * NOTE: If `amount` is the maximum `uint256`, the allowance is not updated on
          * `transferFrom`. This is semantically equivalent to an infinite approval.
          */
         function approve(address spender, uint256 amount) public returns (bool) {

786:     /**
          * NOTE: Does not update the allowance if the current allowance
          * is the maximum `uint256`.
          */
         function transferFrom(
             address from,
             address to,
             uint256 amount
         ) public returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

159:     /// Settle trade, transfer tokens to trader, and report bad trade if needed
         /// @dev boughtAmt can be manipulated upwards; soldAmt upwards
         /// @custom:interaction reentrancy-safe b/c state-locking
         // checks:
         //   state is OPEN
         //   caller is `origin`
         //   now >= endTime
         // actions:
         //   (if not already called) call gnosis.settleAuction(auctionID), which:
         //     settles the Gnosis Auction
         //     transfers the resulting tokens back to this address
         //   if the auction's clearing price was below what we assert it should be,
         //     then broker.reportViolation()
         //   transfer all balancess of `buy` and `sell` at this address to `origin`
         // effects:
         //   state' is CLOSED
         function settle()
             external
             stateTransition(TradeStatus.OPEN, TradeStatus.CLOSED)
             returns (uint256 soldAmt, uint256 boughtAmt)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="NC-25"></a>[NC-25] Use a `modifier` instead of a `require/if` statement for a special `msg.sender` actor
If a function is supposed to be access-controlled, a `modifier` should be used instead of a `require/if` statement for more readability.

*Instances (10)*:
```solidity
File: ./contracts/p1/Main.sol

154:         require(msg.sender == address(this), "not self");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

279:         require(msg.sender == address(origin), "only origin can settle");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

180:         require(msg.sender == origin, "only origin can settle");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

32:         if (!roleRegistry.isOwner(msg.sender)) {

56:         if (!roleRegistry.isOwner(msg.sender)) {

84:         if (!roleRegistry.isOwner(msg.sender)) {

108:         if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

29:         if (!roleRegistry.isOwner(msg.sender)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

38:         if (!roleRegistry.isOwner(msg.sender)) {

60:         if (!roleRegistry.isOwnerOrEmergencyCouncil(msg.sender)) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-26"></a>[NC-26] Constant state variables defined more than once
Rather than redefining state variable constant, consider using a library to store all constants as this will prevent data redundancy

*Instances (4)*:
```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

95:     TradeKind public constant KIND = TradeKind.DUTCH_AUCTION;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

24:     TradeKind public constant KIND = TradeKind.BATCH_AUCTION;

25:     uint256 public constant FEE_DENOMINATOR = 1000;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

7: uint256 constant FEE_DENOMINATOR = 100_00;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="NC-27"></a>[NC-27] Consider using named mappings
Consider moving to solidity version 0.8.18 or later, and using [named mappings](https://ethereum.stackexchange.com/questions/51629/how-to-name-the-arguments-in-mapping/145555#145555) to make it easier to understand the purpose of each mapping

*Instances (30)*:
```solidity
File: ./contracts/mixins/Auth.sol

36:     mapping(address => uint256) public longFreezes;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

26:     mapping(IERC20 => IAsset) private assets;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

41:     mapping(TradeKind => uint48) private tradeEnd; // {s} last endTime() of an auction per kind

44:     mapping(IERC20 => uint192) private tokensOut; // {tok} token balances out in ITrades

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

80:     mapping(uint48 => Basket) private basketHistory;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

52:     mapping(address => bool) private trades;

63:     mapping(IERC20Metadata => bool) public dutchTradeDisabled;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

18:     mapping(address => RevenueShare) public distribution;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/StRSR.sol

63:     mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}

71:     mapping(uint256 => mapping(address => mapping(address => uint256))) private _allowances;

84:     mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {qDrafts}

85:     mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index

126:     mapping(address => CountersUpgradeable.Counter) private _nonces;

128:     mapping(address => CountersUpgradeable.Counter) private _delegationNonces;

827:         mapping(address => uint256) storage eraStakes = stakes[era];

861:         mapping(address => uint256) storage eraStakes = stakes[era];

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

34:     mapping(address => address) private _delegates;

41:     mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}

43:     mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

36:     mapping(IERC20 => uint192) targetAmts;

38:     mapping(IERC20 => bytes32) targetNames;

40:     mapping(bytes32 => BackupConfig) backups;

50:     mapping(IERC20 => uint192) refAmts; // {ref/BU}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

29:     mapping(IERC20 => ITrade) public trades;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

16:     mapping(bytes32 => mapping(address => bool)) private _isValidAsset;

17:     mapping(address => bool) public isDeprecated;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

15:     mapping(address => uint256) private rTokenFeeNumerator;

16:     mapping(address => bool) private rTokenFeeSet;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

13:     mapping(bytes32 => IDeployer) public deployments;

14:     mapping(bytes32 => bool) public isDeprecated;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-28"></a>[NC-28] Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*Instances (1)*:
```solidity
File: ./contracts/p1/BasketHandler.sol

32:     uint48 public constant MIN_WARMUP_PERIOD = 60; // {s} 1 minute

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

### <a name="NC-29"></a>[NC-29] Adding a `return` statement when the function defines a named return variable, is redundant

*Instances (20)*:
```solidity
File: ./contracts/libraries/Fixed.sol

557:     /// Multiplies two fixes and divide by a third
         /// @param a First to multiply
         /// @param b Second to multiply
         /// @param c Denominator
         function safeMulDiv(
             uint192 a,
             uint192 b,
             uint192 c,
             RoundingMode rounding
         ) internal pure returns (uint192 result) {
             if (a == 0 || b == 0) return 0;
             if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
     
             uint256 result_256;
             unchecked {
                 (uint256 hi, uint256 lo) = fullMul(a, b);
                 if (hi >= c) return FIX_MAX;
                 uint256 mm = mulmod(a, b, c);
                 if (mm > lo) hi -= 1;
                 lo -= mm;
                 uint256 pow2 = c & (0 - c);
     
                 uint256 c_256 = uint256(c);
                 // Warning: Should not access c below this line
     
                 c_256 /= pow2;
                 lo /= pow2;
                 lo += hi * ((0 - pow2) / pow2 + 1);
                 uint256 r = 1;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 result_256 = lo * r;
     
                 // Apply rounding
                 if (rounding == CEIL) {
                     if (mm != 0) result_256 += 1;
                 } else if (rounding == ROUND) {
                     if (mm > ((c_256 - 1) / 2)) result_256 += 1;
                 }
             }
     
             if (result_256 >= FIX_MAX) return FIX_MAX;
             return uint192(result_256);

557:     /// Multiplies two fixes and divide by a third
         /// @param a First to multiply
         /// @param b Second to multiply
         /// @param c Denominator
         function safeMulDiv(
             uint192 a,
             uint192 b,
             uint192 c,
             RoundingMode rounding
         ) internal pure returns (uint192 result) {
             if (a == 0 || b == 0) return 0;

557:     /// Multiplies two fixes and divide by a third
         /// @param a First to multiply
         /// @param b Second to multiply
         /// @param c Denominator
         function safeMulDiv(
             uint192 a,
             uint192 b,
             uint192 c,
             RoundingMode rounding
         ) internal pure returns (uint192 result) {
             if (a == 0 || b == 0) return 0;
             if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;

557:     /// Multiplies two fixes and divide by a third
         /// @param a First to multiply
         /// @param b Second to multiply
         /// @param c Denominator
         function safeMulDiv(
             uint192 a,
             uint192 b,
             uint192 c,
             RoundingMode rounding
         ) internal pure returns (uint192 result) {
             if (a == 0 || b == 0) return 0;
             if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
     
             uint256 result_256;
             unchecked {
                 (uint256 hi, uint256 lo) = fullMul(a, b);
                 if (hi >= c) return FIX_MAX;
                 uint256 mm = mulmod(a, b, c);
                 if (mm > lo) hi -= 1;
                 lo -= mm;
                 uint256 pow2 = c & (0 - c);
     
                 uint256 c_256 = uint256(c);
                 // Warning: Should not access c below this line
     
                 c_256 /= pow2;
                 lo /= pow2;
                 lo += hi * ((0 - pow2) / pow2 + 1);
                 uint256 r = 1;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 r *= 2 - c_256 * r;
                 result_256 = lo * r;
     
                 // Apply rounding
                 if (rounding == CEIL) {
                     if (mm != 0) result_256 += 1;
                 } else if (rounding == ROUND) {
                     if (mm > ((c_256 - 1) / 2)) result_256 += 1;
                 }
             }
     
             if (result_256 >= FIX_MAX) return FIX_MAX;

557:     /// Multiplies two fixes and divide by a third
         /// @param a First to multiply
         /// @param b Second to multiply
         /// @param c Denominator
         function safeMulDiv(
             uint192 a,
             uint192 b,
             uint192 c,
             RoundingMode rounding
         ) internal pure returns (uint192 result) {
             if (a == 0 || b == 0) return 0;
             if (a == FIX_MAX || b == FIX_MAX || c == 0) return FIX_MAX;
     
             uint256 result_256;
             unchecked {
                 (uint256 hi, uint256 lo) = fullMul(a, b);
                 if (hi >= c) return FIX_MAX;

684: /// @notice Calculates the square root of x using the Babylonian method.
     ///
     /// @dev See https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Babylonian_method.
     ///
     /// Notes:
     /// - If x is not a perfect square, the result is rounded down.
     /// - Credits to OpenZeppelin for the explanations in comments below.
     ///
     /// @param x The uint256 number for which to calculate the square root.
     /// @return result The result as a uint256.
     function sqrt256(uint256 x) pure returns (uint256 result) {
         if (x == 0) {
             return 0;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

210:     /// Register an asset, unregistering any previous asset with the same ERC20.
         // effects: assets' = assets.set(asset.erc20(), asset)
         // returns: assets[asset.erc20()] != asset
         function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
             if (asset.isCollateral()) {
                 require(
                     ICollateral(address(asset)).status() == CollateralStatus.SOUND,
                     "collateral not sound"
                 );
             }
     
             AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();
             if (address(assetPluginRegistry) != address(0)) {
                 require(
                     main.assetPluginRegistry().isValidAsset(
                         keccak256(abi.encodePacked(this.version())),
                         address(asset)
                     ),
                     "unsupported asset"
                 );
             }
     
             IERC20Metadata erc20 = asset.erc20();
             if (_erc20s.contains(address(erc20))) {
                 if (assets[erc20] == asset) return false;
                 else emit AssetUnregistered(erc20, assets[erc20]);
             } else {
                 _erc20s.add(address(erc20));
             }
     
             assets[erc20] = asset;
             emit AssetRegistered(erc20, asset);
     
             // Refresh to ensure it does not revert, and to save a recent lastPrice
             asset.refresh();
     
             if (!main.frozen()) {
                 backingManager.grantRTokenAllowance(erc20);
             }
     
             return true;

210:     /// Register an asset, unregistering any previous asset with the same ERC20.
         // effects: assets' = assets.set(asset.erc20(), asset)
         // returns: assets[asset.erc20()] != asset
         function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
             if (asset.isCollateral()) {
                 require(
                     ICollateral(address(asset)).status() == CollateralStatus.SOUND,
                     "collateral not sound"
                 );
             }
     
             AssetPluginRegistry assetPluginRegistry = main.assetPluginRegistry();
             if (address(assetPluginRegistry) != address(0)) {
                 require(
                     main.assetPluginRegistry().isValidAsset(
                         keccak256(abi.encodePacked(this.version())),
                         address(asset)
                     ),
                     "unsupported asset"
                 );
             }
     
             IERC20Metadata erc20 = asset.erc20();
             if (_erc20s.contains(address(erc20))) {
                 if (assets[erc20] == asset) return false;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

312:     /// @return status_ The status of the basket
         // returns DISABLED if disabled == true, and worst(status(coll)) otherwise
         function status() public view returns (CollateralStatus status_) {
             uint256 size = basket.erc20s.length;
     
             // untestable:
             //      disabled is only set in _switchBasket, and only if size > 0.
             if (disabled || size == 0) return CollateralStatus.DISABLED;

312:     /// @return status_ The status of the basket
         // returns DISABLED if disabled == true, and worst(status(coll)) otherwise
         function status() public view returns (CollateralStatus status_) {
             uint256 size = basket.erc20s.length;
     
             // untestable:
             //      disabled is only set in _switchBasket, and only if size > 0.
             if (disabled || size == 0) return CollateralStatus.DISABLED;
     
             for (uint256 i = 0; i < size; ++i) {
                 CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();
                 if (s.worseThan(status_)) {
                     if (s == CollateralStatus.DISABLED) return CollateralStatus.DISABLED;

403:     /// Returns the price of a BU (including issuance premium)
         /// Included for backwards compatibility with <4.0.0
         /// Should not revert
         /// @return low {UoA/BU} The lower end of the price estimate
         /// @return high {UoA/BU} The upper end of the price estimate
         // returns sum(quantity(erc20) * price(erc20) for erc20 in basket.erc20s)
         function price() external view returns (uint192 low, uint192 high) {
             return price(true);

460:     /// Return the current issuance/redemption quantities for `amount` BUs
         /// Included for backwards compatibility with <4.0.0
         /// @param rounding If CEIL, apply issuance premium
         /// @param amount {BU}
         /// @return erc20s The backing collateral erc20s
         /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
         // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])
         function quote(uint192 amount, RoundingMode rounding)
             external
             view
             returns (address[] memory erc20s, uint256[] memory quantities)
         {
             return quote(amount, rounding == CEIL, rounding);

603:     /// @return baskets {BU}
         ///          .top The number of partial basket units: e.g max(coll.map((c) => c.balAsBUs())
         ///          .bottom The number of whole basket units held by the account
         /// @dev Returns (FIX_ZERO, FIX_MAX) for an empty or DISABLED basket
         // Returns:
         //    (0, 0), if (basket.erc20s is empty) or (disabled is true) or (status() is DISABLED)
         //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
         function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {
             uint256 length = basket.erc20s.length;
             if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);

603:     /// @return baskets {BU}
         ///          .top The number of partial basket units: e.g max(coll.map((c) => c.balAsBUs())
         ///          .bottom The number of whole basket units held by the account
         /// @dev Returns (FIX_ZERO, FIX_MAX) for an empty or DISABLED basket
         // Returns:
         //    (0, 0), if (basket.erc20s is empty) or (disabled is true) or (status() is DISABLED)
         //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
         function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {
             uint256 length = basket.erc20s.length;
             if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);
             baskets.bottom = FIX_MAX;
     
             for (uint256 i = 0; i < length; ++i) {
                 ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);
                 if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);

603:     /// @return baskets {BU}
         ///          .top The number of partial basket units: e.g max(coll.map((c) => c.balAsBUs())
         ///          .bottom The number of whole basket units held by the account
         /// @dev Returns (FIX_ZERO, FIX_MAX) for an empty or DISABLED basket
         // Returns:
         //    (0, 0), if (basket.erc20s is empty) or (disabled is true) or (status() is DISABLED)
         //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
         function basketsHeldBy(address account) public view returns (BasketRange memory baskets) {
             uint256 length = basket.erc20s.length;
             if (length == 0 || disabled) return BasketRange(FIX_ZERO, FIX_MAX);
             baskets.bottom = FIX_MAX;
     
             for (uint256 i = 0; i < length; ++i) {
                 ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);
                 if (coll.status() == CollateralStatus.DISABLED) return BasketRange(FIX_ZERO, FIX_MAX);
     
                 // {tok/BU}
                 uint192 q = _quantity(basket.erc20s[i], coll, CEIL);
                 if (q == FIX_MAX) return BasketRange(FIX_ZERO, FIX_MAX);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

24:     /// Select and prepare a trade that moves us closer to capitalization, using the
        /// basket range to avoid overeager/duplicate trading.
        /// The basket range is the full range of projected outcomes for the rebalancing process.
        // This is the "main loop" for recollateralization trading:
        // actions:
        //   let range = basketRange(...)
        //   let trade = nextTradePair(...)
        //   if trade.sell is not a defaulted collateral, prepareTradeToCoverDeficit(...)
        //   otherwise, prepareTradeSell(...) taking the minBuyAmount as the dependent variable
        function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)
            external
            view
            returns (
                bool doTrade,
                TradeRequest memory req,
                TradePrices memory prices
            )
        {
            // Compute a target basket range for trading -  {BU}
            // The basket range is the full range of projected outcomes for the rebalancing process
            BasketRange memory range = basketRange(ctx, reg);
    
            // Select a pair to trade next, if one exists
            TradeInfo memory trade = nextTradePair(ctx, reg, range);
    
            // Don't trade if no pair is selected
            if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
                return (false, req, prices);
            }
    
            // If we are selling a fully unpriced asset or UNSOUND collateral, do not cover deficit
            // untestable:
            //     sellLow will not be zero, those assets are skipped in nextTradePair
            if (
                trade.prices.sellLow == 0 ||
                (trade.sell.isCollateral() &&
                    ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)
            ) {
                // Emergency case
                // Set minBuyAmount as a function of sellAmount
                (doTrade, req) = trade.prepareTradeSell(ctx.minTradeVolume, ctx.maxTradeSlippage);
            } else {
                // Normal case
                // Set sellAmount as a function of minBuyAmount
                (doTrade, req) = trade.prepareTradeToCoverDeficit(
                    ctx.minTradeVolume,
                    ctx.maxTradeSlippage
                );
            }
    
            // At this point doTrade _must_ be true, otherwise nextTradePair assumptions are broken
            assert(doTrade);
    
            return (doTrade, req, trade.prices);

24:     /// Select and prepare a trade that moves us closer to capitalization, using the
        /// basket range to avoid overeager/duplicate trading.
        /// The basket range is the full range of projected outcomes for the rebalancing process.
        // This is the "main loop" for recollateralization trading:
        // actions:
        //   let range = basketRange(...)
        //   let trade = nextTradePair(...)
        //   if trade.sell is not a defaulted collateral, prepareTradeToCoverDeficit(...)
        //   otherwise, prepareTradeSell(...) taking the minBuyAmount as the dependent variable
        function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)
            external
            view
            returns (
                bool doTrade,
                TradeRequest memory req,
                TradePrices memory prices
            )
        {
            // Compute a target basket range for trading -  {BU}
            // The basket range is the full range of projected outcomes for the rebalancing process
            BasketRange memory range = basketRange(ctx, reg);
    
            // Select a pair to trade next, if one exists
            TradeInfo memory trade = nextTradePair(ctx, reg, range);
    
            // Don't trade if no pair is selected
            if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
                return (false, req, prices);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

26:     /// Prepare a trade to sell `trade.sellAmount` that guarantees a reasonable closing price,
        /// without explicitly aiming at a particular buy amount.
        /// @param trade:
        ///   sell != 0, sellAmount >= 0 {sellTok}, prices.sellLow >= 0 {UoA/sellTok}
        ///   buy != 0, buyAmount (unused) {buyTok}, prices.buyHigh > 0 {UoA/buyTok}
        /// @return notDust True when the trade is larger than the dust amount
        /// @return req The prepared trade request to send to the Broker
        //
        // If notDust is true, then the returned trade request satisfies:
        //   req.sell == trade.sell and req.buy == trade.buy,
        //   req.minBuyAmount * trade.prices.buyHigh ~=
        //        trade.sellAmount * trade.prices.sellLow * (1-maxTradeSlippage),
        //   req.sellAmount == min(trade.sell.maxTradeSize(), trade.sellAmount)
        //   1 < req.sellAmount
        //
        // If notDust is false, no trade exists that satisfies those constraints.
        function prepareTradeSell(
            TradeInfo memory trade,
            uint192 minTradeVolume,
            uint192 maxTradeSlippage
        ) internal view returns (bool notDust, TradeRequest memory req) {
            // checked for in RevenueTrader / CollateralizatlionLib
            assert(
                trade.prices.buyHigh != 0 &&
                    trade.prices.buyHigh != FIX_MAX &&
                    trade.prices.sellLow != FIX_MAX
            );
    
            notDust = isEnoughToSell(
                trade.sell,
                trade.sellAmount,
                trade.prices.sellLow,
                minTradeVolume
            );
    
            // Cap sell amount using the high price
            // Under price decay trade.prices.sellHigh can become up to 3x the savedHighPrice before
            // becoming FIX_MAX after the full price timeout
            uint192 s = trade.sellAmount;
            if (trade.prices.sellHigh != FIX_MAX) {
                // {sellTok}
                uint192 maxSell = maxTradeSize(trade.sell, trade.buy, trade.prices.sellHigh);
                require(maxSell > 1, "trade sizing error");
                if (s > maxSell) s = maxSell;
            } else {
                require(trade.prices.sellLow == 0, "trade pricing error");
            }
    
            // Calculate equivalent buyAmount within [0, FIX_MAX]
            // {buyTok} = {sellTok} * {1} * {UoA/sellTok} / {UoA/buyTok}
            uint192 b = s.mul(FIX_ONE.minus(maxTradeSlippage)).safeMulDiv(
                trade.prices.sellLow,
                trade.prices.buyHigh,
                CEIL
            );
    
            // {*tok} => {q*Tok}
            req.sellAmount = s.shiftl_toUint(int8(trade.sell.erc20Decimals()), FLOOR);
            req.minBuyAmount = b.shiftl_toUint(int8(trade.buy.erc20Decimals()), CEIL);
            req.sell = trade.sell;
            req.buy = trade.buy;
    
            return (notDust, req);

91:     /// Assuming we have `trade.sellAmount` sell tokens available, prepare a trade to cover as
        /// much of our deficit of `trade.buyAmount` buy tokens as possible, given expected trade
        /// slippage and maxTradeVolume().
        /// @param trade:
        ///   sell != 0
        ///   buy != 0
        ///   sellAmount (unused) {sellTok}
        ///   buyAmount >= 0 {buyTok}
        ///   prices.sellLow > 0 {UoA/sellTok}
        ///   prices.buyHigh > 0 {UoA/buyTok}
        /// @return notDust Whether the prepared trade is large enough to be worth trading
        /// @return req The prepared trade request to send to the Broker
        //
        // Returns prepareTradeSell(trade, rules), where
        //   req.sellAmount = min(trade.sellAmount,
        //                trade.buyAmount * (buyHigh / sellLow) / (1-maxTradeSlippage))
        //   i.e, the minimum of trade.sellAmount and (a sale amount that, at current prices and
        //   maximum slippage, will yield at least the requested trade.buyAmount)
        //
        // Which means we should get that, if notDust is true, then:
        //   req.sell = sell and req.buy = buy
        //
        //   1 <= req.minBuyAmount <= max(trade.buyAmount, buy.minTradeSize()))
        //   1 < req.sellAmount <= min(trade.sellAmount, sell.maxTradeSize())
        //   req.minBuyAmount ~= trade.sellAmount * sellLow / buyHigh * (1-maxTradeSlippage)
        //
        //   req.sellAmount (and req.minBuyAmount) are maximal satisfying all these conditions
        function prepareTradeToCoverDeficit(
            TradeInfo memory trade,
            uint192 minTradeVolume,
            uint192 maxTradeSlippage
        ) internal view returns (bool notDust, TradeRequest memory req) {
            assert(
                trade.prices.sellLow != 0 &&
                    trade.prices.sellLow != FIX_MAX &&
                    trade.prices.buyHigh != 0 &&
                    trade.prices.buyHigh != FIX_MAX
            );
    
            // Don't buy dust.
            trade.buyAmount = fixMax(
                trade.buyAmount,
                minTradeSize(minTradeVolume, trade.prices.buyHigh)
            );
    
            // {sellTok} = {buyTok} * {UoA/buyTok} / {UoA/sellTok}
            uint192 exactSellAmount = trade.buyAmount.mulDiv(
                trade.prices.buyHigh,
                trade.prices.sellLow,
                CEIL
            );
            // exactSellAmount: Amount to sell to buy `deficitAmount` if there's no slippage
    
            // slippedSellAmount: Amount needed to sell to buy `deficitAmount`, counting slippage
            uint192 slippedSellAmount = exactSellAmount.div(FIX_ONE.minus(maxTradeSlippage), CEIL);
    
            trade.sellAmount = fixMin(slippedSellAmount, trade.sellAmount); // {sellTok}
            return prepareTradeSell(trade, minTradeVolume, maxTradeSlippage);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

104:     function propose(
             address[] memory targets,
             uint256[] memory values,
             bytes[] memory calldatas,
             string memory description
         ) public override(Governor, IGovernor) returns (uint256 proposalId) {
             // The super call checks that getVotes() >= proposalThreshold()
             return super.propose(targets, values, calldatas, description);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

### <a name="NC-30"></a>[NC-30] `require()` / `revert()` statements should have descriptive reason strings

*Instances (1)*:
```solidity
File: ./contracts/libraries/Fixed.sol

318:         require(x_ <= FIX_ONE);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

### <a name="NC-31"></a>[NC-31] Take advantage of Custom Error's return value property
An important feature of Custom Error is that values such as address, tokenID, msg.value can be written inside the () sign, this kind of approach provides a serious advantage in debugging and examining the revert details of dapps such as tenderly.

*Instances (34)*:
```solidity
File: ./contracts/libraries/Fixed.sol

74:     if (FIX_MAX < x) revert UIntOutOfBounds();

103:     if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

214:         if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0

395:         if (96 <= decimals) revert UIntOutOfBounds();

626:         if (hi >= z) revert UIntOutOfBounds();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

97:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

450:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

579:                         if (errData.length == 0) revert(); // solhint-disable-line reason-string

728:                 if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

62:             if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

33:             revert AssetPluginRegistry__InvalidCaller();

36:             revert AssetPluginRegistry__InvalidAsset();

42:                 revert AssetPluginRegistry__InvalidVersion();

57:             revert AssetPluginRegistry__InvalidCaller();

60:             revert AssetPluginRegistry__LengthMismatch();

64:             revert AssetPluginRegistry__InvalidAsset();

70:                 revert AssetPluginRegistry__InvalidVersion();

85:             revert AssetPluginRegistry__InvalidCaller();

88:             revert AssetPluginRegistry__LengthMismatch();

92:             revert AssetPluginRegistry__InvalidVersion();

98:                 revert AssetPluginRegistry__InvalidAsset();

109:             revert AssetPluginRegistry__InvalidCaller();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

30:             revert DAOFeeRegistry__InvalidCaller();

37:             revert DAOFeeRegistry__InvalidRoleRegistry();

46:             revert DAOFeeRegistry__InvalidFeeRecipient();

49:             revert DAOFeeRegistry__FeeRecipientAlreadySet();

58:             revert DAOFeeRegistry__InvalidFeeNumerator();

68:             revert DAOFeeRegistry__InvalidFeeNumerator();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

29:             revert VersionRegistry__ZeroAddress();

39:             revert VersionRegistry__InvalidCaller();

43:             revert VersionRegistry__ZeroAddress();

50:             revert VersionRegistry__InvalidRegistration();

61:             revert VersionRegistry__InvalidCaller();

65:             revert VersionRegistry__AlreadyDeprecated();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-32"></a>[NC-32] Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While this won't save gas in the recent solidity versions, this is shorter and more readable (this is especially true in calculations).

*Instances (2)*:
```solidity
File: ./contracts/libraries/Fixed.sol

102:     if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5

103:     if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

### <a name="NC-33"></a>[NC-33] Use scientific notation for readability reasons for large multiples of ten
The more a number has zeros, the harder it becomes to see with the eyes if it's the intended value. To ease auditing and bug bounty hunting, consider using the scientific notation

*Instances (1)*:
```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

50: uint192 constant MAX_EXP = 6502287e18; // {1} (1000000/999999)^6502287 = ~666.6667

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-34"></a>[NC-34] Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be:

1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions

However, the contract(s) below do not follow this ordering

*Instances (18)*:
```solidity
File: ./contracts/libraries/Fixed.sol

1: 
   Current order:
   FunctionDefinition.toUint
   FunctionDefinition.toUint
   FunctionDefinition.shiftl
   FunctionDefinition.shiftl
   FunctionDefinition.plus
   FunctionDefinition.plusu
   FunctionDefinition.minus
   FunctionDefinition.minusu
   FunctionDefinition.mul
   FunctionDefinition.mul
   FunctionDefinition.mulu
   FunctionDefinition.div
   FunctionDefinition.div
   FunctionDefinition.divu
   FunctionDefinition.divu
   VariableDeclaration.FIX_HALF
   FunctionDefinition.powu
   FunctionDefinition.sqrt
   FunctionDefinition.lt
   FunctionDefinition.lte
   FunctionDefinition.gt
   FunctionDefinition.gte
   FunctionDefinition.eq
   FunctionDefinition.neq
   FunctionDefinition.near
   FunctionDefinition.shiftl_toUint
   FunctionDefinition.shiftl_toUint
   FunctionDefinition.mulu_toUint
   FunctionDefinition.mulu_toUint
   FunctionDefinition.mul_toUint
   FunctionDefinition.mul_toUint
   FunctionDefinition.muluDivu
   FunctionDefinition.muluDivu
   FunctionDefinition.mulDiv
   FunctionDefinition.mulDiv
   FunctionDefinition.safeMul
   FunctionDefinition.safeDiv
   FunctionDefinition.safeMulDiv
   
   Suggested order:
   VariableDeclaration.FIX_HALF
   FunctionDefinition.toUint
   FunctionDefinition.toUint
   FunctionDefinition.shiftl
   FunctionDefinition.shiftl
   FunctionDefinition.plus
   FunctionDefinition.plusu
   FunctionDefinition.minus
   FunctionDefinition.minusu
   FunctionDefinition.mul
   FunctionDefinition.mul
   FunctionDefinition.mulu
   FunctionDefinition.div
   FunctionDefinition.div
   FunctionDefinition.divu
   FunctionDefinition.divu
   FunctionDefinition.powu
   FunctionDefinition.sqrt
   FunctionDefinition.lt
   FunctionDefinition.lte
   FunctionDefinition.gt
   FunctionDefinition.gte
   FunctionDefinition.eq
   FunctionDefinition.neq
   FunctionDefinition.near
   FunctionDefinition.shiftl_toUint
   FunctionDefinition.shiftl_toUint
   FunctionDefinition.mulu_toUint
   FunctionDefinition.mulu_toUint
   FunctionDefinition.mul_toUint
   FunctionDefinition.mul_toUint
   FunctionDefinition.muluDivu
   FunctionDefinition.muluDivu
   FunctionDefinition.mulDiv
   FunctionDefinition.mulDiv
   FunctionDefinition.safeMul
   FunctionDefinition.safeDiv
   FunctionDefinition.safeMulDiv

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/mixins/Auth.sol

1: 
   Current order:
   VariableDeclaration.OWNER_ROLE
   VariableDeclaration.SHORT_FREEZER_ROLE
   VariableDeclaration.LONG_FREEZER_ROLE
   VariableDeclaration.PAUSER_ROLE
   VariableDeclaration.longFreezes
   VariableDeclaration.unfreezeAt
   VariableDeclaration.shortFreeze
   VariableDeclaration.longFreeze
   VariableDeclaration.tradingPaused
   VariableDeclaration.issuancePaused
   FunctionDefinition.__Auth_init
   FunctionDefinition.grantRole
   FunctionDefinition.frozen
   FunctionDefinition.tradingPausedOrFrozen
   FunctionDefinition.issuancePausedOrFrozen
   FunctionDefinition.freezeShort
   FunctionDefinition.freezeLong
   FunctionDefinition.freezeForever
   FunctionDefinition.unfreeze
   FunctionDefinition.pauseTrading
   FunctionDefinition.unpauseTrading
   FunctionDefinition.pauseIssuance
   FunctionDefinition.unpauseIssuance
   FunctionDefinition.setShortFreeze
   FunctionDefinition.setLongFreeze
   FunctionDefinition.freezeUntil
   VariableDeclaration.__gap
   
   Suggested order:
   VariableDeclaration.OWNER_ROLE
   VariableDeclaration.SHORT_FREEZER_ROLE
   VariableDeclaration.LONG_FREEZER_ROLE
   VariableDeclaration.PAUSER_ROLE
   VariableDeclaration.longFreezes
   VariableDeclaration.unfreezeAt
   VariableDeclaration.shortFreeze
   VariableDeclaration.longFreeze
   VariableDeclaration.tradingPaused
   VariableDeclaration.issuancePaused
   VariableDeclaration.__gap
   FunctionDefinition.__Auth_init
   FunctionDefinition.grantRole
   FunctionDefinition.frozen
   FunctionDefinition.tradingPausedOrFrozen
   FunctionDefinition.issuancePausedOrFrozen
   FunctionDefinition.freezeShort
   FunctionDefinition.freezeLong
   FunctionDefinition.freezeForever
   FunctionDefinition.unfreeze
   FunctionDefinition.pauseTrading
   FunctionDefinition.unpauseTrading
   FunctionDefinition.pauseIssuance
   FunctionDefinition.unpauseIssuance
   FunctionDefinition.setShortFreeze
   FunctionDefinition.setLongFreeze
   FunctionDefinition.freezeUntil

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

1: 
   Current order:
   FunctionDefinition.__ComponentRegistry_init
   VariableDeclaration.rToken
   FunctionDefinition._setRToken
   VariableDeclaration.stRSR
   FunctionDefinition._setStRSR
   VariableDeclaration.assetRegistry
   FunctionDefinition._setAssetRegistry
   VariableDeclaration.basketHandler
   FunctionDefinition._setBasketHandler
   VariableDeclaration.backingManager
   FunctionDefinition._setBackingManager
   VariableDeclaration.distributor
   FunctionDefinition._setDistributor
   VariableDeclaration.rsrTrader
   FunctionDefinition._setRSRTrader
   VariableDeclaration.rTokenTrader
   FunctionDefinition._setRTokenTrader
   VariableDeclaration.furnace
   FunctionDefinition._setFurnace
   VariableDeclaration.broker
   FunctionDefinition._setBroker
   VariableDeclaration.__gap
   
   Suggested order:
   VariableDeclaration.rToken
   VariableDeclaration.stRSR
   VariableDeclaration.assetRegistry
   VariableDeclaration.basketHandler
   VariableDeclaration.backingManager
   VariableDeclaration.distributor
   VariableDeclaration.rsrTrader
   VariableDeclaration.rTokenTrader
   VariableDeclaration.furnace
   VariableDeclaration.broker
   VariableDeclaration.__gap
   FunctionDefinition.__ComponentRegistry_init
   FunctionDefinition._setRToken
   FunctionDefinition._setStRSR
   FunctionDefinition._setAssetRegistry
   FunctionDefinition._setBasketHandler
   FunctionDefinition._setBackingManager
   FunctionDefinition._setDistributor
   FunctionDefinition._setRSRTrader
   FunctionDefinition._setRTokenTrader
   FunctionDefinition._setFurnace
   FunctionDefinition._setBroker

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

1: 
   Current order:
   UsingForDirective.EnumerableSet.AddressSet
   VariableDeclaration.GAS_FOR_BH_QTY
   VariableDeclaration.GAS_FOR_DISABLE_BASKET
   VariableDeclaration.basketHandler
   VariableDeclaration.backingManager
   VariableDeclaration._erc20s
   VariableDeclaration.assets
   VariableDeclaration.lastRefresh
   FunctionDefinition.init
   FunctionDefinition.refresh
   FunctionDefinition.register
   FunctionDefinition.swapRegistered
   FunctionDefinition.unregister
   FunctionDefinition.toAsset
   FunctionDefinition.toColl
   FunctionDefinition.isRegistered
   FunctionDefinition.erc20s
   FunctionDefinition.getRegistry
   FunctionDefinition.validateCurrentAssets
   FunctionDefinition.size
   FunctionDefinition._register
   FunctionDefinition._registerIgnoringCollisions
   FunctionDefinition._reserveGas
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.EnumerableSet.AddressSet
   VariableDeclaration.GAS_FOR_BH_QTY
   VariableDeclaration.GAS_FOR_DISABLE_BASKET
   VariableDeclaration.basketHandler
   VariableDeclaration.backingManager
   VariableDeclaration._erc20s
   VariableDeclaration.assets
   VariableDeclaration.lastRefresh
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.refresh
   FunctionDefinition.register
   FunctionDefinition.swapRegistered
   FunctionDefinition.unregister
   FunctionDefinition.toAsset
   FunctionDefinition.toColl
   FunctionDefinition.isRegistered
   FunctionDefinition.erc20s
   FunctionDefinition.getRegistry
   FunctionDefinition.validateCurrentAssets
   FunctionDefinition.size
   FunctionDefinition._register
   FunctionDefinition._registerIgnoringCollisions
   FunctionDefinition._reserveGas

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

1: 
   Current order:
   UsingForDirective.FixLib
   UsingForDirective.IERC20
   VariableDeclaration.assetRegistry
   VariableDeclaration.basketHandler
   VariableDeclaration.distributor
   VariableDeclaration.rToken
   VariableDeclaration.rsr
   VariableDeclaration.stRSR
   VariableDeclaration.rsrTrader
   VariableDeclaration.rTokenTrader
   VariableDeclaration.MAX_TRADING_DELAY
   VariableDeclaration.MAX_BACKING_BUFFER
   VariableDeclaration.tradingDelay
   VariableDeclaration.backingBuffer
   VariableDeclaration.furnace
   VariableDeclaration.tradeEnd
   VariableDeclaration.tokensOut
   FunctionDefinition.init
   FunctionDefinition.grantRTokenAllowance
   FunctionDefinition.settleTrade
   FunctionDefinition.rebalance
   FunctionDefinition.forwardRevenue
   FunctionDefinition.tradingContext
   FunctionDefinition.compromiseBasketsNeeded
   FunctionDefinition.setTradingDelay
   FunctionDefinition.setBackingBuffer
   FunctionDefinition.cacheComponents
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.FixLib
   UsingForDirective.IERC20
   VariableDeclaration.assetRegistry
   VariableDeclaration.basketHandler
   VariableDeclaration.distributor
   VariableDeclaration.rToken
   VariableDeclaration.rsr
   VariableDeclaration.stRSR
   VariableDeclaration.rsrTrader
   VariableDeclaration.rTokenTrader
   VariableDeclaration.MAX_TRADING_DELAY
   VariableDeclaration.MAX_BACKING_BUFFER
   VariableDeclaration.tradingDelay
   VariableDeclaration.backingBuffer
   VariableDeclaration.furnace
   VariableDeclaration.tradeEnd
   VariableDeclaration.tokensOut
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.grantRTokenAllowance
   FunctionDefinition.settleTrade
   FunctionDefinition.rebalance
   FunctionDefinition.forwardRevenue
   FunctionDefinition.tradingContext
   FunctionDefinition.compromiseBasketsNeeded
   FunctionDefinition.setTradingDelay
   FunctionDefinition.setBackingBuffer
   FunctionDefinition.cacheComponents

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

1: 
   Current order:
   UsingForDirective.Basket
   UsingForDirective.CollateralStatus
   UsingForDirective.EnumerableMap.Bytes32ToUintMap
   UsingForDirective.EnumerableSet.Bytes32Set
   UsingForDirective.FixLib
   VariableDeclaration.MAX_TARGET_AMT
   VariableDeclaration.MIN_WARMUP_PERIOD
   VariableDeclaration.MAX_WARMUP_PERIOD
   VariableDeclaration.MAX_BACKUP_ERC20S
   VariableDeclaration.assetRegistry
   VariableDeclaration.backingManager
   VariableDeclaration.rsr
   VariableDeclaration.rToken
   VariableDeclaration.stRSR
   VariableDeclaration.config
   VariableDeclaration.basket
   VariableDeclaration.nonce
   VariableDeclaration.timestamp
   VariableDeclaration.disabled
   VariableDeclaration._targetNames
   VariableDeclaration._newBasket
   VariableDeclaration.warmupPeriod
   VariableDeclaration.lastStatusTimestamp
   VariableDeclaration.lastStatus
   VariableDeclaration.basketHistory
   VariableDeclaration._targetAmts
   VariableDeclaration.reweightable
   VariableDeclaration.lastCollateralized
   VariableDeclaration.enableIssuancePremium
   FunctionDefinition.init
   FunctionDefinition.disableBasket
   FunctionDefinition.refreshBasket
   FunctionDefinition.trackStatus
   FunctionDefinition.setPrimeBasket
   FunctionDefinition.forceSetPrimeBasket
   FunctionDefinition._setPrimeBasket
   FunctionDefinition.setBackupConfig
   FunctionDefinition.fullyCollateralized
   FunctionDefinition.status
   FunctionDefinition.isReady
   FunctionDefinition.quantity
   FunctionDefinition.quantityUnsafe
   FunctionDefinition.issuancePremium
   FunctionDefinition._quantity
   FunctionDefinition.price
   FunctionDefinition.price
   FunctionDefinition.quote
   FunctionDefinition.quote
   FunctionDefinition.quoteCustomRedemption
   FunctionDefinition.basketsHeldBy
   FunctionDefinition.setWarmupPeriod
   FunctionDefinition.setIssuancePremiumEnabled
   FunctionDefinition.requireGovernanceOnly
   FunctionDefinition._switchBasket
   FunctionDefinition.requireValidCollArray
   FunctionDefinition.getHistoricalBasket
   FunctionDefinition.getPrimeBasket
   FunctionDefinition.getBackupConfig
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.Basket
   UsingForDirective.CollateralStatus
   UsingForDirective.EnumerableMap.Bytes32ToUintMap
   UsingForDirective.EnumerableSet.Bytes32Set
   UsingForDirective.FixLib
   VariableDeclaration.MAX_TARGET_AMT
   VariableDeclaration.MIN_WARMUP_PERIOD
   VariableDeclaration.MAX_WARMUP_PERIOD
   VariableDeclaration.MAX_BACKUP_ERC20S
   VariableDeclaration.assetRegistry
   VariableDeclaration.backingManager
   VariableDeclaration.rsr
   VariableDeclaration.rToken
   VariableDeclaration.stRSR
   VariableDeclaration.config
   VariableDeclaration.basket
   VariableDeclaration.nonce
   VariableDeclaration.timestamp
   VariableDeclaration.disabled
   VariableDeclaration._targetNames
   VariableDeclaration._newBasket
   VariableDeclaration.warmupPeriod
   VariableDeclaration.lastStatusTimestamp
   VariableDeclaration.lastStatus
   VariableDeclaration.basketHistory
   VariableDeclaration._targetAmts
   VariableDeclaration.reweightable
   VariableDeclaration.lastCollateralized
   VariableDeclaration.enableIssuancePremium
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.disableBasket
   FunctionDefinition.refreshBasket
   FunctionDefinition.trackStatus
   FunctionDefinition.setPrimeBasket
   FunctionDefinition.forceSetPrimeBasket
   FunctionDefinition._setPrimeBasket
   FunctionDefinition.setBackupConfig
   FunctionDefinition.fullyCollateralized
   FunctionDefinition.status
   FunctionDefinition.isReady
   FunctionDefinition.quantity
   FunctionDefinition.quantityUnsafe
   FunctionDefinition.issuancePremium
   FunctionDefinition._quantity
   FunctionDefinition.price
   FunctionDefinition.price
   FunctionDefinition.quote
   FunctionDefinition.quote
   FunctionDefinition.quoteCustomRedemption
   FunctionDefinition.basketsHeldBy
   FunctionDefinition.setWarmupPeriod
   FunctionDefinition.setIssuancePremiumEnabled
   FunctionDefinition.requireGovernanceOnly
   FunctionDefinition._switchBasket
   FunctionDefinition.requireValidCollArray
   FunctionDefinition.getHistoricalBasket
   FunctionDefinition.getPrimeBasket
   FunctionDefinition.getBackupConfig

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

1: 
   Current order:
   UsingForDirective.EnumerableSet.AddressSet
   UsingForDirective.FixLib
   UsingForDirective.IERC20Upgradeable
   UsingForDirective.Clones
   VariableDeclaration.MAX_AUCTION_LENGTH
   VariableDeclaration.MIN_AUCTION_LENGTH
   VariableDeclaration.backingManager
   VariableDeclaration.rsrTrader
   VariableDeclaration.rTokenTrader
   VariableDeclaration.batchTradeImplementation
   VariableDeclaration.gnosis
   VariableDeclaration.batchAuctionLength
   VariableDeclaration.batchTradeDisabled
   VariableDeclaration.trades
   VariableDeclaration.dutchTradeImplementation
   VariableDeclaration.dutchAuctionLength
   VariableDeclaration.dutchTradeDisabled
   VariableDeclaration.rToken
   FunctionDefinition.init
   FunctionDefinition.cacheComponents
   FunctionDefinition.openTrade
   FunctionDefinition.reportViolation
   FunctionDefinition.setGnosis
   FunctionDefinition.setBatchTradeImplementation
   FunctionDefinition.setBatchAuctionLength
   FunctionDefinition.setDutchTradeImplementation
   FunctionDefinition.setDutchAuctionLength
   FunctionDefinition.enableBatchTrade
   FunctionDefinition.enableDutchTrade
   FunctionDefinition.newBatchAuction
   FunctionDefinition.newDutchAuction
   FunctionDefinition.pricedAtTimestamp
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.EnumerableSet.AddressSet
   UsingForDirective.FixLib
   UsingForDirective.IERC20Upgradeable
   UsingForDirective.Clones
   VariableDeclaration.MAX_AUCTION_LENGTH
   VariableDeclaration.MIN_AUCTION_LENGTH
   VariableDeclaration.backingManager
   VariableDeclaration.rsrTrader
   VariableDeclaration.rTokenTrader
   VariableDeclaration.batchTradeImplementation
   VariableDeclaration.gnosis
   VariableDeclaration.batchAuctionLength
   VariableDeclaration.batchTradeDisabled
   VariableDeclaration.trades
   VariableDeclaration.dutchTradeImplementation
   VariableDeclaration.dutchAuctionLength
   VariableDeclaration.dutchTradeDisabled
   VariableDeclaration.rToken
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.cacheComponents
   FunctionDefinition.openTrade
   FunctionDefinition.reportViolation
   FunctionDefinition.setGnosis
   FunctionDefinition.setBatchTradeImplementation
   FunctionDefinition.setBatchAuctionLength
   FunctionDefinition.setDutchTradeImplementation
   FunctionDefinition.setDutchAuctionLength
   FunctionDefinition.enableBatchTrade
   FunctionDefinition.enableDutchTrade
   FunctionDefinition.newBatchAuction
   FunctionDefinition.newDutchAuction
   FunctionDefinition.pricedAtTimestamp

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

1: 
   Current order:
   UsingForDirective.IERC20Upgradeable
   UsingForDirective.FixLib
   UsingForDirective.EnumerableSet.AddressSet
   VariableDeclaration.destinations
   VariableDeclaration.distribution
   VariableDeclaration.FURNACE
   VariableDeclaration.ST_RSR
   VariableDeclaration.MAX_DESTINATIONS_ALLOWED
   VariableDeclaration.rsr
   VariableDeclaration.rToken
   VariableDeclaration.furnace
   VariableDeclaration.stRSR
   VariableDeclaration.rTokenTrader
   VariableDeclaration.rsrTrader
   FunctionDefinition.init
   FunctionDefinition.setDistribution
   FunctionDefinition.setDistributions
   StructDefinition.Transfer
   FunctionDefinition.distribute
   FunctionDefinition.totals
   FunctionDefinition._setDistribution
   FunctionDefinition._ensureSufficientTotal
   FunctionDefinition.cacheComponents
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.IERC20Upgradeable
   UsingForDirective.FixLib
   UsingForDirective.EnumerableSet.AddressSet
   VariableDeclaration.destinations
   VariableDeclaration.distribution
   VariableDeclaration.FURNACE
   VariableDeclaration.ST_RSR
   VariableDeclaration.MAX_DESTINATIONS_ALLOWED
   VariableDeclaration.rsr
   VariableDeclaration.rToken
   VariableDeclaration.furnace
   VariableDeclaration.stRSR
   VariableDeclaration.rTokenTrader
   VariableDeclaration.rsrTrader
   VariableDeclaration.__gap
   StructDefinition.Transfer
   FunctionDefinition.init
   FunctionDefinition.setDistribution
   FunctionDefinition.setDistributions
   FunctionDefinition.distribute
   FunctionDefinition.totals
   FunctionDefinition._setDistribution
   FunctionDefinition._ensureSufficientTotal
   FunctionDefinition.cacheComponents

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

1: 
   Current order:
   UsingForDirective.FixLib
   VariableDeclaration.MAX_RATIO
   VariableDeclaration.rToken
   VariableDeclaration.ratio
   VariableDeclaration.lastPayout
   VariableDeclaration.lastPayoutBal
   FunctionDefinition.init
   FunctionDefinition.melt
   FunctionDefinition.setRatio
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.FixLib
   VariableDeclaration.MAX_RATIO
   VariableDeclaration.rToken
   VariableDeclaration.ratio
   VariableDeclaration.lastPayout
   VariableDeclaration.lastPayoutBal
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.melt
   FunctionDefinition.setRatio

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

1: 
   Current order:
   VariableDeclaration.rsr
   VariableDeclaration.versionRegistry
   VariableDeclaration.assetPluginRegistry
   VariableDeclaration.daoFeeRegistry
   FunctionDefinition.constructor
   FunctionDefinition.init
   FunctionDefinition.poke
   FunctionDefinition.setVersionRegistry
   FunctionDefinition.setAssetPluginRegistry
   FunctionDefinition.setDAOFeeRegistry
   FunctionDefinition.hasRole
   FunctionDefinition.upgradeMainTo
   FunctionDefinition.upgradeRTokenTo
   FunctionDefinition._authorizeUpgrade
   FunctionDefinition._upgradeProxy
   VariableDeclaration.__gap
   
   Suggested order:
   VariableDeclaration.rsr
   VariableDeclaration.versionRegistry
   VariableDeclaration.assetPluginRegistry
   VariableDeclaration.daoFeeRegistry
   VariableDeclaration.__gap
   FunctionDefinition.constructor
   FunctionDefinition.init
   FunctionDefinition.poke
   FunctionDefinition.setVersionRegistry
   FunctionDefinition.setAssetPluginRegistry
   FunctionDefinition.setDAOFeeRegistry
   FunctionDefinition.hasRole
   FunctionDefinition.upgradeMainTo
   FunctionDefinition.upgradeRTokenTo
   FunctionDefinition._authorizeUpgrade
   FunctionDefinition._upgradeProxy

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

1: 
   Current order:
   UsingForDirective.FixLib
   UsingForDirective.ThrottleLib.Throttle
   UsingForDirective.IERC20Upgradeable
   VariableDeclaration.MIN_THROTTLE_RATE_AMT
   VariableDeclaration.MAX_THROTTLE_RATE_AMT
   VariableDeclaration.MAX_THROTTLE_PCT_AMT
   VariableDeclaration.MIN_EXCHANGE_RATE
   VariableDeclaration.MAX_EXCHANGE_RATE
   VariableDeclaration.mandate
   VariableDeclaration.assetRegistry
   VariableDeclaration.basketHandler
   VariableDeclaration.backingManager
   VariableDeclaration.furnace
   VariableDeclaration.basketsNeeded
   VariableDeclaration.issuanceThrottle
   VariableDeclaration.redemptionThrottle
   FunctionDefinition.init
   FunctionDefinition.issue
   FunctionDefinition.issueTo
   FunctionDefinition.redeem
   FunctionDefinition.redeemTo
   FunctionDefinition.redeemCustom
   FunctionDefinition.mint
   FunctionDefinition.melt
   FunctionDefinition.dissolve
   FunctionDefinition.setBasketsNeeded
   FunctionDefinition.monetizeDonations
   FunctionDefinition.issuanceAvailable
   FunctionDefinition.redemptionAvailable
   FunctionDefinition.issuanceThrottleParams
   FunctionDefinition.redemptionThrottleParams
   FunctionDefinition.setIssuanceThrottleParams
   FunctionDefinition.setRedemptionThrottleParams
   FunctionDefinition._scaleUp
   FunctionDefinition._scaleDown
   FunctionDefinition._beforeTokenTransfer
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.FixLib
   UsingForDirective.ThrottleLib.Throttle
   UsingForDirective.IERC20Upgradeable
   VariableDeclaration.MIN_THROTTLE_RATE_AMT
   VariableDeclaration.MAX_THROTTLE_RATE_AMT
   VariableDeclaration.MAX_THROTTLE_PCT_AMT
   VariableDeclaration.MIN_EXCHANGE_RATE
   VariableDeclaration.MAX_EXCHANGE_RATE
   VariableDeclaration.mandate
   VariableDeclaration.assetRegistry
   VariableDeclaration.basketHandler
   VariableDeclaration.backingManager
   VariableDeclaration.furnace
   VariableDeclaration.basketsNeeded
   VariableDeclaration.issuanceThrottle
   VariableDeclaration.redemptionThrottle
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.issue
   FunctionDefinition.issueTo
   FunctionDefinition.redeem
   FunctionDefinition.redeemTo
   FunctionDefinition.redeemCustom
   FunctionDefinition.mint
   FunctionDefinition.melt
   FunctionDefinition.dissolve
   FunctionDefinition.setBasketsNeeded
   FunctionDefinition.monetizeDonations
   FunctionDefinition.issuanceAvailable
   FunctionDefinition.redemptionAvailable
   FunctionDefinition.issuanceThrottleParams
   FunctionDefinition.redemptionThrottleParams
   FunctionDefinition.setIssuanceThrottleParams
   FunctionDefinition.setRedemptionThrottleParams
   FunctionDefinition._scaleUp
   FunctionDefinition._scaleDown
   FunctionDefinition._beforeTokenTransfer

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

1: 
   Current order:
   UsingForDirective.FixLib
   UsingForDirective.IERC20
   VariableDeclaration.tokenToBuy
   VariableDeclaration.assetRegistry
   VariableDeclaration.distributor
   VariableDeclaration.backingManager
   VariableDeclaration.furnace
   VariableDeclaration.rToken
   VariableDeclaration.rsr
   FunctionDefinition.init
   FunctionDefinition.cacheComponents
   FunctionDefinition.settleTrade
   FunctionDefinition.distributeTokenToBuy
   FunctionDefinition.returnTokens
   FunctionDefinition.manageTokens
   FunctionDefinition._distributeTokenToBuy
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.FixLib
   UsingForDirective.IERC20
   VariableDeclaration.tokenToBuy
   VariableDeclaration.assetRegistry
   VariableDeclaration.distributor
   VariableDeclaration.backingManager
   VariableDeclaration.furnace
   VariableDeclaration.rToken
   VariableDeclaration.rsr
   VariableDeclaration.__gap
   FunctionDefinition.init
   FunctionDefinition.cacheComponents
   FunctionDefinition.settleTrade
   FunctionDefinition.distributeTokenToBuy
   FunctionDefinition.returnTokens
   FunctionDefinition.manageTokens
   FunctionDefinition._distributeTokenToBuy

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

1: 
   Current order:
   UsingForDirective.CountersUpgradeable.Counter
   UsingForDirective.IERC20Upgradeable
   VariableDeclaration.MIN_UNSTAKING_DELAY
   VariableDeclaration.MAX_UNSTAKING_DELAY
   VariableDeclaration.MAX_REWARD_RATIO
   VariableDeclaration.name
   VariableDeclaration.symbol
   VariableDeclaration.decimals
   VariableDeclaration.assetRegistry
   VariableDeclaration.backingManager
   VariableDeclaration.basketHandler
   VariableDeclaration.rsr
   VariableDeclaration.era
   VariableDeclaration.stakes
   VariableDeclaration.totalStakes
   VariableDeclaration.stakeRSR
   VariableDeclaration.stakeRate
   VariableDeclaration.MAX_STAKE_RATE
   VariableDeclaration._allowances
   VariableDeclaration.draftEra
   StructDefinition.CumulativeDraft
   VariableDeclaration.draftQueues
   VariableDeclaration.firstRemainingDraft
   VariableDeclaration.totalDrafts
   VariableDeclaration.draftRSR
   VariableDeclaration.draftRate
   VariableDeclaration.MAX_DRAFT_RATE
   VariableDeclaration._nonces
   VariableDeclaration._delegationNonces
   VariableDeclaration._PERMIT_TYPEHASH
   VariableDeclaration.unstakingDelay
   VariableDeclaration.rewardRatio
   VariableDeclaration.payoutLastPaid
   VariableDeclaration.rsrRewardsAtLastPayout
   VariableDeclaration.MAX_WITHDRAWAL_LEAK
   VariableDeclaration.leaked
   VariableDeclaration.lastWithdrawRefresh
   VariableDeclaration.withdrawalLeak
   VariableDeclaration.MAX_SAFE_STAKE_RATE
   VariableDeclaration.MIN_SAFE_STAKE_RATE
   FunctionDefinition.init
   FunctionDefinition.payoutRewards
   FunctionDefinition.stake
   FunctionDefinition.unstake
   FunctionDefinition.withdraw
   FunctionDefinition.cancelUnstake
   FunctionDefinition.seizeRSR
   FunctionDefinition.resetStakes
   FunctionDefinition.exchangeRate
   FunctionDefinition.endIdForWithdraw
   FunctionDefinition.draftQueueLen
   FunctionDefinition.getDraftRSR
   FunctionDefinition.getStakeRSR
   FunctionDefinition.getTotalDrafts
   FunctionDefinition.getDraftEra
   FunctionDefinition._payoutRewards
   FunctionDefinition.pushDraft
   FunctionDefinition.beginEra
   FunctionDefinition.beginDraftEra
   FunctionDefinition.rsrRewards
   FunctionDefinition.leakyRefresh
   FunctionDefinition.mintStakes
   FunctionDefinition._requireNotTradingPausedOrFrozen
   FunctionDefinition._requireNotFrozen
   FunctionDefinition._requireGovernanceOnly
   FunctionDefinition.totalSupply
   FunctionDefinition.balanceOf
   FunctionDefinition.allowance
   FunctionDefinition.transfer
   FunctionDefinition.approve
   FunctionDefinition.transferFrom
   FunctionDefinition.increaseAllowance
   FunctionDefinition.decreaseAllowance
   FunctionDefinition._transfer
   FunctionDefinition._mint
   FunctionDefinition._burn
   FunctionDefinition._approve
   FunctionDefinition._spendAllowance
   FunctionDefinition._afterTokenTransfer
   FunctionDefinition._notZero
   FunctionDefinition._notZero
   FunctionDefinition.permit
   FunctionDefinition.nonces
   FunctionDefinition.delegationNonces
   FunctionDefinition.DOMAIN_SEPARATOR
   FunctionDefinition._useNonce
   FunctionDefinition._useDelegationNonce
   FunctionDefinition.setUnstakingDelay
   FunctionDefinition.setRewardRatio
   FunctionDefinition.setWithdrawalLeak
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.CountersUpgradeable.Counter
   UsingForDirective.IERC20Upgradeable
   VariableDeclaration.MIN_UNSTAKING_DELAY
   VariableDeclaration.MAX_UNSTAKING_DELAY
   VariableDeclaration.MAX_REWARD_RATIO
   VariableDeclaration.name
   VariableDeclaration.symbol
   VariableDeclaration.decimals
   VariableDeclaration.assetRegistry
   VariableDeclaration.backingManager
   VariableDeclaration.basketHandler
   VariableDeclaration.rsr
   VariableDeclaration.era
   VariableDeclaration.stakes
   VariableDeclaration.totalStakes
   VariableDeclaration.stakeRSR
   VariableDeclaration.stakeRate
   VariableDeclaration.MAX_STAKE_RATE
   VariableDeclaration._allowances
   VariableDeclaration.draftEra
   VariableDeclaration.draftQueues
   VariableDeclaration.firstRemainingDraft
   VariableDeclaration.totalDrafts
   VariableDeclaration.draftRSR
   VariableDeclaration.draftRate
   VariableDeclaration.MAX_DRAFT_RATE
   VariableDeclaration._nonces
   VariableDeclaration._delegationNonces
   VariableDeclaration._PERMIT_TYPEHASH
   VariableDeclaration.unstakingDelay
   VariableDeclaration.rewardRatio
   VariableDeclaration.payoutLastPaid
   VariableDeclaration.rsrRewardsAtLastPayout
   VariableDeclaration.MAX_WITHDRAWAL_LEAK
   VariableDeclaration.leaked
   VariableDeclaration.lastWithdrawRefresh
   VariableDeclaration.withdrawalLeak
   VariableDeclaration.MAX_SAFE_STAKE_RATE
   VariableDeclaration.MIN_SAFE_STAKE_RATE
   VariableDeclaration.__gap
   StructDefinition.CumulativeDraft
   FunctionDefinition.init
   FunctionDefinition.payoutRewards
   FunctionDefinition.stake
   FunctionDefinition.unstake
   FunctionDefinition.withdraw
   FunctionDefinition.cancelUnstake
   FunctionDefinition.seizeRSR
   FunctionDefinition.resetStakes
   FunctionDefinition.exchangeRate
   FunctionDefinition.endIdForWithdraw
   FunctionDefinition.draftQueueLen
   FunctionDefinition.getDraftRSR
   FunctionDefinition.getStakeRSR
   FunctionDefinition.getTotalDrafts
   FunctionDefinition.getDraftEra
   FunctionDefinition._payoutRewards
   FunctionDefinition.pushDraft
   FunctionDefinition.beginEra
   FunctionDefinition.beginDraftEra
   FunctionDefinition.rsrRewards
   FunctionDefinition.leakyRefresh
   FunctionDefinition.mintStakes
   FunctionDefinition._requireNotTradingPausedOrFrozen
   FunctionDefinition._requireNotFrozen
   FunctionDefinition._requireGovernanceOnly
   FunctionDefinition.totalSupply
   FunctionDefinition.balanceOf
   FunctionDefinition.allowance
   FunctionDefinition.transfer
   FunctionDefinition.approve
   FunctionDefinition.transferFrom
   FunctionDefinition.increaseAllowance
   FunctionDefinition.decreaseAllowance
   FunctionDefinition._transfer
   FunctionDefinition._mint
   FunctionDefinition._burn
   FunctionDefinition._approve
   FunctionDefinition._spendAllowance
   FunctionDefinition._afterTokenTransfer
   FunctionDefinition._notZero
   FunctionDefinition._notZero
   FunctionDefinition.permit
   FunctionDefinition.nonces
   FunctionDefinition.delegationNonces
   FunctionDefinition.DOMAIN_SEPARATOR
   FunctionDefinition._useNonce
   FunctionDefinition._useDelegationNonce
   FunctionDefinition.setUnstakingDelay
   FunctionDefinition.setRewardRatio
   FunctionDefinition.setWithdrawalLeak

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

1: 
   Current order:
   StructDefinition.Checkpoint
   VariableDeclaration._DELEGATE_TYPEHASH
   VariableDeclaration._delegates
   VariableDeclaration._eras
   VariableDeclaration._checkpoints
   VariableDeclaration._totalSupplyCheckpoints
   FunctionDefinition.beginEra
   FunctionDefinition.clock
   FunctionDefinition.CLOCK_MODE
   FunctionDefinition.currentEra
   FunctionDefinition.checkpoints
   FunctionDefinition.numCheckpoints
   FunctionDefinition.delegates
   FunctionDefinition.getVotes
   FunctionDefinition.getPastVotes
   FunctionDefinition.getPastTotalSupply
   FunctionDefinition.getPastEra
   FunctionDefinition._checkpointsLookup
   FunctionDefinition.delegate
   FunctionDefinition.delegateBySig
   FunctionDefinition.stakeAndDelegate
   FunctionDefinition._mint
   FunctionDefinition._burn
   FunctionDefinition._afterTokenTransfer
   FunctionDefinition._delegate
   FunctionDefinition._moveVotingPower
   FunctionDefinition._writeCheckpoint
   FunctionDefinition._add
   FunctionDefinition._subtract
   FunctionDefinition._requireValidTimepoint
   VariableDeclaration.__gap
   
   Suggested order:
   VariableDeclaration._DELEGATE_TYPEHASH
   VariableDeclaration._delegates
   VariableDeclaration._eras
   VariableDeclaration._checkpoints
   VariableDeclaration._totalSupplyCheckpoints
   VariableDeclaration.__gap
   StructDefinition.Checkpoint
   FunctionDefinition.beginEra
   FunctionDefinition.clock
   FunctionDefinition.CLOCK_MODE
   FunctionDefinition.currentEra
   FunctionDefinition.checkpoints
   FunctionDefinition.numCheckpoints
   FunctionDefinition.delegates
   FunctionDefinition.getVotes
   FunctionDefinition.getPastVotes
   FunctionDefinition.getPastTotalSupply
   FunctionDefinition.getPastEra
   FunctionDefinition._checkpointsLookup
   FunctionDefinition.delegate
   FunctionDefinition.delegateBySig
   FunctionDefinition.stakeAndDelegate
   FunctionDefinition._mint
   FunctionDefinition._burn
   FunctionDefinition._afterTokenTransfer
   FunctionDefinition._delegate
   FunctionDefinition._moveVotingPower
   FunctionDefinition._writeCheckpoint
   FunctionDefinition._add
   FunctionDefinition._subtract
   FunctionDefinition._requireValidTimepoint

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

1: 
   Current order:
   VariableDeclaration.main
   FunctionDefinition.constructor
   FunctionDefinition.__Component_init
   ModifierDefinition.notTradingPausedOrFrozen
   ModifierDefinition.notIssuancePausedOrFrozen
   ModifierDefinition.notFrozen
   ModifierDefinition.governance
   ModifierDefinition.onlyMain
   FunctionDefinition._authorizeUpgrade
   VariableDeclaration.__gap
   
   Suggested order:
   VariableDeclaration.main
   VariableDeclaration.__gap
   ModifierDefinition.notTradingPausedOrFrozen
   ModifierDefinition.notIssuancePausedOrFrozen
   ModifierDefinition.notFrozen
   ModifierDefinition.governance
   ModifierDefinition.onlyMain
   FunctionDefinition.constructor
   FunctionDefinition.__Component_init
   FunctionDefinition._authorizeUpgrade

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

1: 
   Current order:
   UsingForDirective.FixLib
   UsingForDirective.TradeInfo
   UsingForDirective.IBackingManager
   FunctionDefinition.prepareRecollateralizationTrade
   FunctionDefinition.basketRange
   StructDefinition.MaxSurplusDeficit
   FunctionDefinition.nextTradePair
   FunctionDefinition.isBetterSurplus
   
   Suggested order:
   UsingForDirective.FixLib
   UsingForDirective.TradeInfo
   UsingForDirective.IBackingManager
   StructDefinition.MaxSurplusDeficit
   FunctionDefinition.prepareRecollateralizationTrade
   FunctionDefinition.basketRange
   FunctionDefinition.nextTradePair
   FunctionDefinition.isBetterSurplus

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

1: 
   Current order:
   UsingForDirective.FixLib
   VariableDeclaration.MAX_TRADE_VOLUME
   VariableDeclaration.MAX_TRADE_SLIPPAGE
   VariableDeclaration.broker
   VariableDeclaration.trades
   VariableDeclaration.tradesOpen
   VariableDeclaration.maxTradeSlippage
   VariableDeclaration.minTradeVolume
   VariableDeclaration.tradesNonce
   FunctionDefinition.__Trading_init
   FunctionDefinition.requireNotTradingPausedOrFrozen
   FunctionDefinition.claimRewards
   FunctionDefinition.claimRewardsSingle
   FunctionDefinition.settleTrade
   FunctionDefinition.tryTrade
   FunctionDefinition.setMaxTradeSlippage
   FunctionDefinition.setMinTradeVolume
   VariableDeclaration.__gap
   
   Suggested order:
   UsingForDirective.FixLib
   VariableDeclaration.MAX_TRADE_VOLUME
   VariableDeclaration.MAX_TRADE_SLIPPAGE
   VariableDeclaration.broker
   VariableDeclaration.trades
   VariableDeclaration.tradesOpen
   VariableDeclaration.maxTradeSlippage
   VariableDeclaration.minTradeVolume
   VariableDeclaration.tradesNonce
   VariableDeclaration.__gap
   FunctionDefinition.__Trading_init
   FunctionDefinition.requireNotTradingPausedOrFrozen
   FunctionDefinition.claimRewards
   FunctionDefinition.claimRewardsSingle
   FunctionDefinition.settleTrade
   FunctionDefinition.tryTrade
   FunctionDefinition.setMaxTradeSlippage
   FunctionDefinition.setMinTradeVolume

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

1: 
   Current order:
   FunctionDefinition.dutchTradeCallback
   UsingForDirective.FixLib
   UsingForDirective.IERC20Metadata
   VariableDeclaration.KIND
   VariableDeclaration.bidType
   VariableDeclaration.status
   VariableDeclaration.broker
   VariableDeclaration.origin
   VariableDeclaration.sell
   VariableDeclaration.buy
   VariableDeclaration.sellAmount
   VariableDeclaration.startTime
   VariableDeclaration.endTime
   VariableDeclaration.bestPrice
   VariableDeclaration.worstPrice
   VariableDeclaration.bidder
   ModifierDefinition.stateTransition
   FunctionDefinition.lot
   FunctionDefinition.bidAmount
   FunctionDefinition.constructor
   FunctionDefinition.init
   FunctionDefinition.bid
   FunctionDefinition.bidWithCallback
   FunctionDefinition.settle
   FunctionDefinition.transferToOriginAfterTradeComplete
   FunctionDefinition.canSettle
   FunctionDefinition._price
   FunctionDefinition._bidAmount
   
   Suggested order:
   UsingForDirective.FixLib
   UsingForDirective.IERC20Metadata
   VariableDeclaration.KIND
   VariableDeclaration.bidType
   VariableDeclaration.status
   VariableDeclaration.broker
   VariableDeclaration.origin
   VariableDeclaration.sell
   VariableDeclaration.buy
   VariableDeclaration.sellAmount
   VariableDeclaration.startTime
   VariableDeclaration.endTime
   VariableDeclaration.bestPrice
   VariableDeclaration.worstPrice
   VariableDeclaration.bidder
   ModifierDefinition.stateTransition
   FunctionDefinition.dutchTradeCallback
   FunctionDefinition.lot
   FunctionDefinition.bidAmount
   FunctionDefinition.constructor
   FunctionDefinition.init
   FunctionDefinition.bid
   FunctionDefinition.bidWithCallback
   FunctionDefinition.settle
   FunctionDefinition.transferToOriginAfterTradeComplete
   FunctionDefinition.canSettle
   FunctionDefinition._price
   FunctionDefinition._bidAmount

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-35"></a>[NC-35] Some require descriptions are not clear
1. It does not comply with the general require error description model of the project (Either all of them should be debugged in this way, or all of them should be explained with a string not exceeding 32 bytes.)
2. For debug dapps like Tenderly, these debug messages are important, this allows the user to see the reasons for revert practically.

*Instances (1)*:
```solidity
File: ./contracts/p1/StRSR.sol

429:         require(caller == address(backingManager), "!bm");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

### <a name="NC-36"></a>[NC-36] Use Underscores for Number Literals (add an underscore every 3 digits)

*Instances (11)*:
```solidity
File: ./contracts/libraries/Throttle.sol

6: uint48 constant ONE_HOUR = 3600; // {seconds/hour}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

9: uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month

10: uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

12: uint256 constant ONE_DAY = 86400; // {s}

36:     uint256 public constant MIN_VOTING_DELAY = 86400; // {s} ONE_DAY

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

50: uint192 constant MAX_EXP = 6502287e18; // {1} (1000000/999999)^6502287 = ~666.6667

51: uint192 constant BASE = 999999e12; // {1} (999999/1000000)

168:         require(prices.sellLow != 0 && prices.sellHigh < FIX_MAX / 1000, "bad sell pricing");

169:         require(prices.buyLow != 0 && prices.buyHigh < FIX_MAX / 1000, "bad buy pricing");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

25:     uint256 public constant FEE_DENOMINATOR = 1000;

29:     uint96 public constant MAX_ORDERS = 5000; // bounded to avoid going beyond block gas limit

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="NC-37"></a>[NC-37] Internal and private variables and functions names should begin with an underscore
According to the Solidity Style Guide, Non-`external` variable and function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)

*Instances (162)*:
```solidity
File: ./contracts/libraries/Allowance.sol

17:     function safeApproveFallbackToMax(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/libraries/Array.sol

9:     function allUnique(IERC20[] memory arr) internal pure returns (bool) {

21:     function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/Fixed.sol

81: function toFix(uint256 x) pure returns (uint192) {

89: function shiftl_toFix(uint256 x, int8 shiftLeft) pure returns (uint192) {

95: function shiftl_toFix(

117: function divFix(uint256 x, uint192 y) pure returns (uint192) {

130: function divuu(uint256 x, uint256 y) pure returns (uint192) {

136: function fixMin(uint192 x, uint192 y) pure returns (uint192) {

142: function fixMax(uint192 x, uint192 y) pure returns (uint192) {

148: function abs(int256 x) pure returns (uint256) {

183:     function toUint(uint192 x) internal pure returns (uint136) {

190:     function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {

198:     function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {

206:     function shiftl(

223:     function plus(uint192 x, uint192 y) internal pure returns (uint192) {

230:     function plusu(uint192 x, uint256 y) internal pure returns (uint192) {

237:     function minus(uint192 x, uint192 y) internal pure returns (uint192) {

244:     function minusu(uint192 x, uint256 y) internal pure returns (uint192) {

252:     function mul(uint192 x, uint192 y) internal pure returns (uint192) {

259:     function mul(

270:     function mulu(uint192 x, uint256 y) internal pure returns (uint192) {

277:     function div(uint192 x, uint192 y) internal pure returns (uint192) {

284:     function div(

296:     function divu(uint192 x, uint256 y) internal pure returns (uint192) {

303:     function divu(

317:     function powu(uint192 x_, uint48 y) internal pure returns (uint192) {

332:     function sqrt(uint192 x) internal pure returns (uint192) {

337:     function lt(uint192 x, uint192 y) internal pure returns (bool) {

341:     function lte(uint192 x, uint192 y) internal pure returns (bool) {

345:     function gt(uint192 x, uint192 y) internal pure returns (bool) {

349:     function gte(uint192 x, uint192 y) internal pure returns (bool) {

353:     function eq(uint192 x, uint192 y) internal pure returns (bool) {

357:     function neq(uint192 x, uint192 y) internal pure returns (bool) {

364:     function near(

380:     function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {

387:     function shiftl_toUint(

406:     function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {

413:     function mulu_toUint(

424:     function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {

431:     function mul_toUint(

443:     function muluDivu(

455:     function muluDivu(

468:     function mulDiv(

480:     function mulDiv(

494:     function safeMul(

544:     function safeDiv(

561:     function safeMulDiv(

619: function mulDiv256(

650: function mulDiv256(

674: function fullMul(uint256 x, uint256 y) pure returns (uint256 hi, uint256 lo) {

694: function sqrt256(uint256 x) pure returns (uint256 result) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Permit.sol

10:     function requireSignature(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/libraries/String.sol

11:     function toLower(string memory str) internal pure returns (string memory) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

37:     function useAvailable(

69:     function currentlyAvailable(Throttle storage throttle, uint256 limit)

80:     function hourlyLimit(Throttle storage throttle, uint256 supply)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/mixins/Auth.sol

214:     function freezeUntil(uint48 newUnfreezeAt) private {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

19:     IBasketHandler private basketHandler;

20:     IBackingManager private backingManager;

26:     mapping(IERC20 => IAsset) private assets;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

25:     IAssetRegistry private assetRegistry;

26:     IBasketHandler private basketHandler;

27:     IDistributor private distributor;

28:     IRToken private rToken;

29:     IERC20 private rsr;

30:     IStRSR private stRSR;

31:     IRevenueTrader private rsrTrader;

32:     IRevenueTrader private rTokenTrader;

40:     IFurnace private furnace;

41:     mapping(TradeKind => uint48) private tradeEnd; // {s} last endTime() of an auction per kind

44:     mapping(IERC20 => uint192) private tokensOut; // {tok} token balances out in ITrades

306:     function compromiseBasketsNeeded(uint192 basketsHeldBottom) private {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

37:     IAssetRegistry private assetRegistry;

38:     IBackingManager private backingManager;

39:     IERC20 private rsr;

40:     IRToken private rToken;

41:     IStRSR private stRSR;

45:     BasketConfig private config;

49:     Basket private basket;

56:     bool private disabled;

73:     uint48 private lastStatusTimestamp;

74:     CollateralStatus private lastStatus;

80:     mapping(uint48 => Basket) private basketHistory;

651:     function requireGovernanceOnly() private governance {}

683:     function requireValidCollArray(IERC20[] calldata erc20s) private view {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

30:     IBackingManager private backingManager;

31:     IRevenueTrader private rsrTrader;

32:     IRevenueTrader private rTokenTrader;

52:     mapping(address => bool) private trades;

67:     IRToken private rToken;

243:     function newBatchAuction(TradeRequest memory req, address caller) private returns (ITrade) {

268:     function newDutchAuction(

298:     function pricedAtTimestamp(IAsset asset) private view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

17:     EnumerableSet.AddressSet internal destinations;

37:     IERC20 private rsr;

38:     IERC20 private rToken;

39:     IFurnace private furnace;

40:     IStRSR private stRSR;

41:     address private rTokenTrader;

42:     address private rsrTrader;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

17:     IRToken private rToken;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/RToken.sol

47:     IAssetRegistry private assetRegistry;

48:     IBasketHandler private basketHandler;

49:     IBackingManager private backingManager;

50:     IFurnace private furnace;

58:     ThrottleLib.Throttle private issuanceThrottle;

59:     ThrottleLib.Throttle private redemptionThrottle;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

20:     IAssetRegistry private assetRegistry;

21:     IDistributor private distributor;

22:     IBackingManager private backingManager;

23:     IFurnace private furnace;

24:     IRToken private rToken;

25:     IERC20 private rsr;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

51:     IAssetRegistry private assetRegistry;

52:     IBackingManager private backingManager;

53:     IBasketHandler private basketHandler;

54:     IERC20 private rsr;

59:     uint256 internal era;

63:     mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}

64:     uint256 private totalStakes; // Total of all stakes {qStRSR}

65:     uint256 private stakeRSR; // Amount of RSR backing all stakes {qRSR}

66:     uint192 private stakeRate; // The exchange rate between stakes and RSR. D18{qStRSR/qRSR}

76:     uint256 internal draftEra; // {draftEra}

86:     uint256 private totalDrafts; // Total of all drafts {qDrafts}

87:     uint256 private draftRSR; // Amount of RSR backing all drafts {qRSR}

150:     uint48 private payoutLastPaid;

153:     uint256 private rsrRewardsAtLastPayout;

159:     uint192 private leaked; // {1} stake fraction that has withdrawn without a refresh

160:     uint48 private lastWithdrawRefresh; // {s} timestamp of last refresh() during withdraw()

640:     function pushDraft(address account, uint256 rsrAmount)

671:     function beginEra() internal virtual {

683:     function beginDraftEra() internal virtual {

693:     function rsrRewards() internal view returns (uint256) {

700:     function leakyRefresh(uint256 rsrWithdrawal) private {

722:     function mintStakes(address account, uint256 rsrAmount) private {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

52:     function beginEra() internal override {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

70:     function empty(Basket storage self) internal {

77:     function setFrom(Basket storage self, Basket storage other) internal {

90:     function add(

289:     function goodCollateral(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

108:     function basketRange(TradingContext memory ctx, Registry memory reg)

274:     function nextTradePair(

381:     function isBetterSurplus(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

24:     function claimRewards(IAssetRegistry reg) internal {

39:     function claimRewardsSingle(IAsset asset) internal {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

42:     function prepareTradeSell(

118:     function prepareTradeToCoverDeficit(

156:     function isEnoughToSell(

174:     function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

182:     function maxTradeSize(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

26:     IBroker private broker;

60:     function requireNotTradingPausedOrFrozen() internal view notTradingPausedOrFrozen {}

120:     function tryTrade(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

185:     function startedInSameEra(uint256 proposalId) private view returns (bool) {

192:     function requireValidVotingDelay(uint256 newVotingDelay) private pure {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

238:     function isAuctionCleared() private view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

12:     address private feeRecipient;

13:     uint256 private defaultFeeNumerator; // 0%

15:     mapping(address => uint256) private rTokenFeeNumerator;

16:     mapping(address => bool) private rTokenFeeSet;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

15:     bytes32 private latestVersion;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-38"></a>[NC-38] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Instances (5)*:
```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

24:     event AssetPluginRegistryUpdated(bytes32 versionHash, address asset, bool validity);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

25:     event DefaultFeeNumeratorSet(uint256 defaultFeeNumerator);

26:     event RTokenFeeNumeratorSet(address indexed rToken, uint256 feeNumerator, bool isActive);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

24:     event VersionRegistered(bytes32 versionHash, IDeployer deployer);

25:     event VersionDeprecated(bytes32 versionHash);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="NC-39"></a>[NC-39] Constants should be defined rather than using magic numbers

*Instances (5)*:
```solidity
File: ./contracts/libraries/Fixed.sol

103:     if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

214:         if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0

395:         if (96 <= decimals) revert UIntOutOfBounds();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

260:             gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

51: uint192 constant BASE = 999999e12; // {1} (999999/1000000)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="NC-40"></a>[NC-40] `public` functions not called by the contract should be declared `external` instead

*Instances (33)*:
```solidity
File: ./contracts/mixins/Auth.sol

99:     function frozen() public view returns (bool) {

105:     function tradingPausedOrFrozen() public view returns (bool) {

111:     function issuancePausedOrFrozen() public view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

58:     function refresh() public {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

343:     function quantity(IERC20 erc20) public view returns (uint192) {

359:     function quantityUnsafe(IERC20 erc20, IAsset asset) public view returns (uint192) {

641:     function setIssuancePremiumEnabled(bool val) public {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

186:     function setBatchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

208:     function setDutchTradeImplementation(ITrade newTradeImplementation) public onlyMain {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/RToken.sol

94:     function issue(uint256 amount) public {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

227:     function stake(uint256 rsrAmount) public {

754:     function totalSupply() public view returns (uint256) {

758:     function balanceOf(address account) public view returns (uint256) {

772:     function transfer(address to, uint256 amount) public returns (bool) {

781:     function approve(address spender, uint256 amount) public returns (bool) {

790:     function transferFrom(

800:     function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {

806:     function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {

922:     function permit(

942:     function nonces(address owner) public view returns (uint256) {

946:     function delegationNonces(address owner) public view returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

66:     function CLOCK_MODE() public pure returns (string memory) {

74:     function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {

78:     function numCheckpoints(address account) public view returns (uint48) {

86:     function getVotes(address account) public view returns (uint256) {

91:     function getPastVotes(address account, uint256 timepoint) public view returns (uint256) {

98:     function getPastTotalSupply(uint256 timepoint) public view returns (uint256) {

105:     function getPastEra(uint256 timepoint) public view returns (uint256) {

162:     function delegate(address delegatee) public {

166:     function delegateBySig(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

18:     function isOwner(address account) public view returns (bool) {

22:     function isEmergencyCouncil(address account) public view returns (bool) {

26:     function isOwnerOrEmergencyCouncil(address account) public view returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

### <a name="NC-41"></a>[NC-41] Variables need not be initialized to zero
The default value for variables is zero, so initializing them to zero is superfluous.

*Instances (62)*:
```solidity
File: ./contracts/libraries/Array.sol

12:             for (uint256 j = 0; j < i; ++j) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/String.sol

14:         for (uint256 i = 0; i < bStr.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

48:         for (uint256 i = 0; i < length; ++i) {

63:         for (uint256 i = 0; i < length; ++i) {

151:         for (uint256 i = 0; i < length; ++i) {

163:         for (uint256 i = 0; i < length; ++i) {

176:             for (uint256 i = 0; i < assetLen; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

236:         for (uint256 i = 0; i < length; ++i) {

289:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

294:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

142:         for (uint256 i = 0; i < len; ++i) refAmts[i] = basket.refAmts[basket.erc20s[i]];

251:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

260:         for (uint256 i = 0; i < erc20s.length; ++i) {

296:         for (uint256 i = 0; i < erc20s.length; ++i) {

321:         for (uint256 i = 0; i < size; ++i) {

424:         for (uint256 i = 0; i < len; ++i) {

491:         for (uint256 i = 0; i < length; ++i) {

535:         for (uint48 i = 0; i < basketNonces.length; ++i) {

547:             for (uint256 j = 0; j < b.erc20s.length; ++j) {

554:                 for (uint256 k = 0; k < len; ++k) {

593:         for (uint256 i = 0; i < len; ++i) {

615:         for (uint256 i = 0; i < length; ++i) {

676:         for (uint256 i = 0; i < len; ++i) {

684:         for (uint256 i = 0; i < erc20s.length; ++i) {

713:         for (uint256 i = 0; i < b.erc20s.length; ++i) {

751:         for (uint256 i = 0; i < erc20s.length; ++i) {

769:         for (uint256 i = 0; i < erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Distributor.sol

92:         for (uint256 i = 0; i < dests.length; ++i) {

145:         for (uint256 i = 0; i < destinations.length(); ++i) {

169:         for (uint256 i = 0; i < numTransfers; ++i) {

206:         for (uint256 i = 0; i < length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

148:         for (uint256 i = 0; i < erc20s.length; ++i) {

214:         for (uint256 i = 0; i < erc20s.length; ++i) {

269:         for (uint256 i = 0; i < portions.length; ++i) {

297:         for (uint256 i = 0; i < erc20s.length; ++i) {

311:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

321:             for (uint256 i = 0; i < erc20s.length; ++i) {

339:         for (uint256 i = 0; i < expectedERC20sOut.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

89:         for (uint256 i = 0; i < len; ++i) {

127:         for (uint256 i = 0; i < len; ++i) {

142:             for (uint256 i = 0; i < len; ++i) {

153:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

136:         uint256 low = 0;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

72:         for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

80:         for (uint256 i = 0; i < length; ++i) {

180:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

198:         for (uint256 i = 0; i < config.erc20s.length; ++i) {

241:         for (uint256 i = 0; i < targetsLength; ++i) {

248:             uint256 size = 0; // backup basket size

252:             for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {

260:             uint256 assigned = 0;

263:             for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {

325:         for (uint256 i = 0; i < len; ++i) {

337:         for (uint256 i = 0; i < len; ++i) {

363:         for (uint256 i = 0; i < len; ++i) {

380:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

139:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

288:         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

27:         for (uint256 i = 0; i < len; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

39:         for (uint256 i = 0; i < validForVersions.length; ++i) {

67:         for (uint256 i = 0; i < _versionHashes.length; ++i) {

95:         for (uint256 i = 0; i < _assets.length; ++i) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | NFT contract redefines `_mint()`/`_safeMint()`, but not both | 3 |
| [L-2](#L-2) | `approve()`/`safeApprove()` may revert if the current approval is not zero | 7 |
| [L-3](#L-3) | Some tokens may revert when zero value transfers are made | 24 |
| [L-4](#L-4) | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 7 |
| [L-5](#L-5) | `decimals()` is not a part of the ERC-20 standard | 6 |
| [L-6](#L-6) | `decimals()` should be of type `uint8` | 3 |
| [L-7](#L-7) | Deprecated approve() function | 3 |
| [L-8](#L-8) | Do not use deprecated library functions | 5 |
| [L-9](#L-9) | `safeApprove()` is deprecated | 4 |
| [L-10](#L-10) | Deprecated _setupRole() function | 1 |
| [L-11](#L-11) | Division by zero not prevented | 29 |
| [L-12](#L-12) | `domainSeparator()` isn't protected against replay attacks in case of a future chain split  | 2 |
| [L-13](#L-13) | Empty Function Body - Consider commenting why | 5 |
| [L-14](#L-14) | External calls in an un-bounded `for-`loop may result in a DOS | 29 |
| [L-15](#L-15) | External call recipient may consume all transaction gas | 1 |
| [L-16](#L-16) | Fallback lacking `payable` | 3 |
| [L-17](#L-17) | Initializers could be front-run | 59 |
| [L-18](#L-18) | Signature use at deadlines should be allowed | 4 |
| [L-19](#L-19) | Prevent accidentally burning tokens | 7 |
| [L-20](#L-20) | Possible rounding issue | 8 |
| [L-21](#L-21) | Loss of precision | 25 |
| [L-22](#L-22) | Solidity version 0.8.20+ may not work on other chains due to `PUSH0` | 26 |
| [L-23](#L-23) | Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership` | 2 |
| [L-24](#L-24) | Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when downcasting | 13 |
| [L-25](#L-25) | Unsafe ERC20 operation(s) | 3 |
| [L-26](#L-26) | Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions | 72 |
| [L-27](#L-27) | Upgradeable contract not initialized | 107 |
| [L-28](#L-28) | Use `initializer` for public-facing functions only. Replace with `onlyInitializing` on internal functions. | 1 |
### <a name="L-1"></a>[L-1] NFT contract redefines `_mint()`/`_safeMint()`, but not both
If one of the functions is re-implemented, or has new arguments, the other should be as well. The `_mint()` variant is supposed to skip `onERC721Received()` checks, whereas `_safeMint()` does not. Not having both points to a possible issue with spec-compatibility.

*Instances (3)*:
```solidity
File: ./contracts/p1/StRSRVotes.sol

201:     function _mint(address account, uint256 amount) internal override {
             super._mint(account, amount);
             _writeCheckpoint(_totalSupplyCheckpoints[era], _add, amount);

206:     function _burn(address account, uint256 amount) internal override {
             super._burn(account, amount);
             _writeCheckpoint(_totalSupplyCheckpoints[era], _subtract, amount);

211:     function _afterTokenTransfer(
             address from,
             address to,
             uint256 amount
         ) internal override {
             super._afterTokenTransfer(from, to, amount);
             _moveVotingPower(delegates(from), delegates(to), amount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

### <a name="L-2"></a>[L-2] `approve()`/`safeApprove()` may revert if the current approval is not zero
- Some tokens (like the *very popular* USDT) do not work when changing the allowance from an existing non-zero allowance value (it will revert if the current approval is not zero to protect against front-running changes of approvals). These tokens must first be approved for zero and then the actual allowance can be approved.
- Furthermore, OZ's implementation of safeApprove would throw an error if an approve is attempted from a non-zero value (`"SafeERC20: approve from non-zero to non-zero allowance"`)

Set the allowance to zero immediately before each of the existing allowance calls

*Instances (7)*:
```solidity
File: ./contracts/libraries/Allowance.sol

25:         token.approve(spender, 0);

34:         try token.approve(spender, value) {

41:             token.approve(spender, type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

72:         IERC20(address(erc20)).safeApprove(address(rToken), 0);

73:         IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

191:         tokenToBuy.safeApprove(address(distributor), 0);

192:         tokenToBuy.safeApprove(address(distributor), bal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

### <a name="L-3"></a>[L-3] Some tokens may revert when zero value transfers are made
Example: https://github.com/d-xo/weird-erc20#revert-on-zero-value-transfers.

In spite of the fact that EIP-20 [states](https://github.com/ethereum/EIPs/blob/46b9b698815abbfa628cd1097311deee77dd45c5/EIPS/eip-20.md?plain=1#L116) that zero-valued transfers must be accepted, some tokens, such as LEND will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.

*Instances (24)*:
```solidity
File: ./contracts/p1/BackingManager.sol

215:             IERC20(address(rsr)).safeTransfer(address(stRSR), rsr.balanceOf(address(this)));

254:                     erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);

257:                     erc20s[i].safeTransfer(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/Broker.sol

259:         IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

287:         IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

170:             IERC20Upgradeable(address(erc20)).safeTransferFrom(

183:                     IERC20Upgradeable(address(erc20)).safeTransferFrom(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

149:             IERC20Upgradeable(erc20s[i]).safeTransferFrom(

219:             IERC20Upgradeable(erc20s[i]).safeTransferFrom(

327:                 IERC20Upgradeable(erc20s[i]).safeTransferFrom(

420:         IERC20Upgradeable(address(erc20)).safeTransfer(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

91:             erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

237:         IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);

337:         IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);

477:         IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

219:         buy.safeTransferFrom(msg.sender, address(this), amountIn);

255:         sell.safeTransfer(bidder, lot()); // {qSellTok}

286:             sell.safeTransfer(bidder, soldAmt); // {qSellTok}

291:         buy.safeTransfer(address(origin), boughtAmt); // {qBuyTok}

292:         sell.safeTransfer(address(origin), sell.balanceOf(address(this))); // {qSellTok}

300:         erc20.safeTransfer(address(origin), erc20.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

205:         if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);

206:         if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

227:         IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-4"></a>[L-4] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*Instances (7)*:
```solidity
File: ./contracts/p1/AssetRegistry.sol

181:                         keccak256(abi.encodePacked(this.version())),

225:                     keccak256(abi.encodePacked(this.version())),

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/Deployer.sol

228:             string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));

229:             string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Main.sol

108:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade mismatch");

117:         require(keccak256(abi.encodePacked(this.version())) == versionHash, "upgrade main first");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

47:         bytes32 versionHash = keccak256(abi.encodePacked(version));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="L-5"></a>[L-5] `decimals()` is not a part of the ERC-20 standard
The `decimals()` function is not a part of the [ERC-20 standard](https://eips.ethereum.org/EIPS/eip-20), and was added later as an [optional extension](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/IERC20Metadata.sol). As such, some valid ERC20 tokens do not support this interface, so it is unsafe to blindly cast all tokens to this interface, and then call this function.

*Instances (6)*:
```solidity
File: ./contracts/p1/BasketHandler.sol

508:                 int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

133:         return sellAmount.shiftl_toUint(int8(sell.decimals()));

177:         sellAmount = shiftl_toFix(sellAmount_, -int8(sell.decimals()), FLOOR); // {sellTok}

367:         return sellAmount.mul(price, CEIL).shiftl_toUint(int8(buy.decimals()), CEIL);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

96:         sellAmount = shiftl_toFix(req.sellAmount, -int8(sell.decimals()), FLOOR); // {sellTok}

127:             DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-6"></a>[L-6] `decimals()` should be of type `uint8`

*Instances (3)*:
```solidity
File: ./contracts/libraries/Fixed.sol

198:     function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {

380:     function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

127:             DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-7"></a>[L-7] Deprecated approve() function
Due to the inheritance of ERC20's approve function, there's a vulnerability to the ERC20 approve and double spend front running attack. Briefly, an authorized spender could spend both allowances by front running an allowance-changing transaction. Consider implementing OpenZeppelin's `.safeApprove()` function to help mitigate this.

*Instances (3)*:
```solidity
File: ./contracts/libraries/Allowance.sol

25:         token.approve(spender, 0);

34:         try token.approve(spender, value) {

41:             token.approve(spender, type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

### <a name="L-8"></a>[L-8] Do not use deprecated library functions

*Instances (5)*:
```solidity
File: ./contracts/p1/BackingManager.sol

72:         IERC20(address(erc20)).safeApprove(address(rToken), 0);

73:         IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

191:         tokenToBuy.safeApprove(address(distributor), 0);

192:         tokenToBuy.safeApprove(address(distributor), bal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

15:         _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

### <a name="L-9"></a>[L-9] `safeApprove()` is deprecated
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead. The function may currently work, but if a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to no longer has this function, you'll encounter unnecessary delays in porting and testing replacement contracts.

*Instances (4)*:
```solidity
File: ./contracts/p1/BackingManager.sol

72:         IERC20(address(erc20)).safeApprove(address(rToken), 0);

73:         IERC20(address(erc20)).safeApprove(address(rToken), type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

191:         tokenToBuy.safeApprove(address(distributor), 0);

192:         tokenToBuy.safeApprove(address(distributor), bal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

### <a name="L-10"></a>[L-10] Deprecated _setupRole() function

*Instances (1)*:
```solidity
File: ./contracts/registry/RoleRegistry.sol

15:         _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

### <a name="L-11"></a>[L-11] Division by zero not prevented
The divisions below take an input parameter which does not have any zero-value checks, which may lead to the functions reverting when zero is passed.

*Instances (29)*:
```solidity
File: ./contracts/libraries/Fixed.sol

121:         return _safeWrap(uint256(x * FIX_SCALE_SQ) / y);

160:     uint256 result = numerator / divisor;

511:             if (rawDelta / b != a) return FIX_MAX;

584:             lo += hi * ((0 - pow2) / pow2 + 1);

633:         lo += hi * ((0 - pow2) / pow2 + 1);

757:         result = (result + x / result) >> 1;

758:         result = (result + x / result) >> 1;

759:         result = (result + x / result) >> 1;

760:         result = (result + x / result) >> 1;

761:         result = (result + x / result) >> 1;

762:         result = (result + x / result) >> 1;

763:         result = (result + x / result) >> 1;

766:         uint256 roundedResult = x / result;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

247:                 uint256 tokensPerShare = delta / (totals.rTokenTotal + totals.rsrTotal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/Distributor.sol

133:             if (totalShares != 0) tokensPerShare = amount / totalShares;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

409:         uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}

410:         uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

274:         uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;

326:         uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;

373:         uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;

441:         uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

448:             stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

456:         uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

463:             draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

472:         seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

504:         return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

623:             : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

705:         uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

131:                          then unsoundPrimeWt(tgt) / len(Backups(tgt))

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

### <a name="L-12"></a>[L-12] `domainSeparator()` isn't protected against replay attacks in case of a future chain split 
Severity: Low.
Description: See <https://eips.ethereum.org/EIPS/eip-2612#security-considerations>.
Remediation: Consider using the [implementation](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/EIP712.sol#L77-L90) from OpenZeppelin, which recalculates the domain separator if the current `block.chainid` is not the cached chain ID.
Past occurrences of this issue:
- [Reality Cards Contest](https://github.com/code-423n4/2021-06-realitycards-findings/issues/166)
- [Swivel Contest](https://github.com/code-423n4/2021-09-swivel-findings/issues/98)
- [Malt Finance Contest](https://github.com/code-423n4/2021-11-malt-findings/issues/349)

*Instances (2)*:
```solidity
File: ./contracts/p1/StRSR.sol

951:     function DOMAIN_SEPARATOR() external view returns (bytes32) {

952:         return _domainSeparatorV4();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

### <a name="L-13"></a>[L-13] Empty Function Body - Consider commenting why

*Instances (5)*:
```solidity
File: ./contracts/p1/BasketHandler.sol

651:     function requireGovernanceOnly() private governance {}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/StRSR.sol

740:     function _requireNotTradingPausedOrFrozen() private notTradingPausedOrFrozen {}

744:     function _requireNotFrozen() private notFrozen {}

748:     function _requireGovernanceOnly() private governance {}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

67:     function _authorizeUpgrade(address newImplementation) internal view override onlyMain {}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

### <a name="L-14"></a>[L-14] External calls in an un-bounded `for-`loop may result in a DOS
Consider limiting the number of iterations in for-loops that make external calls

*Instances (29)*:
```solidity
File: ./contracts/p1/AssetRegistry.sol

64:             assets[IERC20(_erc20s.at(i))].refresh();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

254:                     erc20s[i].safeTransfer(address(rsrTrader), tokensPerShare * totals.rsrTotal);

257:                     erc20s[i].safeTransfer(

295:             ctx.bals[i] = reg.assets[i].bal(address(this)) + tokensOut[reg.erc20s[i]];

298:             if (reg.erc20s[i] == rsr) ctx.bals[i] += reg.assets[i].bal(address(stRSR));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

562:                 uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);

562:                 uint192 amt = portions[i].mul(b.refAmts[b.erc20s[j]], FLOOR);

598:             .safeMulDiv(refAmtsAll[i], collsAll[i].refPerTok(), FLOOR)

599:             .shiftl_toUint(int8(collsAll[i].erc20Decimals()), FLOOR);

720:                 quantities[i] = b

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

91:             erc20s[i].safeTransfer(address(backingManager), erc20s[i].balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

211:             totalWeights[targetIndex] = totalWeights[targetIndex].plus(targetWeight);

217:                 goodWeights[targetIndex] = goodWeights[targetIndex].plus(targetWeight);

242:             if (totalWeights[i].lte(goodWeights[i])) continue; // Don't need any backup weight

265:                     uint192 backupWeight = totalWeights[i].minus(goodWeights[i]).div(

265:                     uint192 backupWeight = totalWeights[i].minus(goodWeights[i]).div(

371:             newPrice += targetAmts[i].mulDiv(

382:             newTargetAmts[i] = targetAmts[i].mulDiv(price, newPrice, CEIL);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

143:             (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/tok}

161:                 uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.top, CEIL);

185:                 uint192 anchor = ctx.quantities[i].mul(ctx.basketsHeld.bottom, FLOOR);

299:             if (ctx.bals[i].gt(needed)) {

300:                 (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/sellTok}

305:                 uint192 delta = ctx.bals[i].minus(needed).mul(low, FLOOR);

309:                 if (reg.assets[i].isCollateral()) {

319:                         ctx.bals[i].minus(needed),

325:                     trade.sellAmount = ctx.bals[i].minus(needed);

336:                 if (ctx.bals[i].lt(needed)) {

338:                     (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

### <a name="L-15"></a>[L-15] External call recipient may consume all transaction gas
There is no limit specified on the amount of gas used, so the recipient can use up all of the transaction's gas, causing it to revert. Use `addr.call{gas: <amount>}("")` or [this](https://github.com/nomad-xyz/ExcessivelySafeCall) library instead.

*Instances (1)*:
```solidity
File: ./contracts/p1/Main.sol

158:         (bool success, ) = proxy.call(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

### <a name="L-16"></a>[L-16] Fallback lacking `payable`

*Instances (3)*:
```solidity
File: ./contracts/libraries/Allowance.sol

17:     function safeApproveFallbackToMax(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

133:         AllowanceLib.safeApproveFallbackToMax(address(sell), address(broker), req.sellAmount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

142:         AllowanceLib.safeApproveFallbackToMax(address(sell), address(gnosis), req.sellAmount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-17"></a>[L-17] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (59)*:
```solidity
File: ./contracts/mixins/Auth.sol

68:     function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

69:         __AccessControl_init();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

18:     function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

42:     function init(IMain main_, IAsset[] calldata assets_) external initializer {

43:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

49:     function init(

55:     ) external initializer {

56:         __Component_init(main_);

57:         __Trading_init(main_, maxTradeSlippage_, minTradeVolume_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

108:     function init(

113:     ) external initializer {

114:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

73:     function init(

80:     ) external initializer {

81:         __Component_init(main_);

264:         trade.init(this, caller, gnosis, batchAuctionLength, req);

293:         trade.init(caller, req.sell, req.buy, req.sellAmount, dutchAuctionLength, prices);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

183:         main.init(components, rsr, params.shortFreeze, params.longFreeze);

186:         components.backingManager.init(

195:         components.basketHandler.init(

203:         components.rsrTrader.init(main, rsr, params.maxTradeSlippage, params.minTradeVolume);

204:         components.rTokenTrader.init(

212:         components.distributor.init(main, params.dist);

215:         components.furnace.init(main, params.rewardRatio);

217:         components.broker.init(

230:             main.stRSR().init(

241:         components.rToken.init(

256:         components.assetRegistry.init(main, assets);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

44:     function init(IMain main_, RevenueShare calldata dist) external initializer {

45:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

33:     function init(IMain main_, uint192 ratio_) external initializer {

34:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

30:     constructor() initializer {}

33:     function init(

38:     ) public virtual initializer {

40:         __Auth_init(shortFreeze_, longFreeze_);

41:         __ComponentRegistry_init(components);

42:         __UUPSUpgradeable_init();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

61:     function init(

68:     ) external initializer {

72:         __Component_init(main_);

73:         __ERC20_init(name_, symbol_);

74:         __ERC20Permit_init(name_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

27:     function init(

32:     ) external initializer {

34:         __Component_init(main_);

35:         __Trading_init(main_, maxTradeSlippage_, minTradeVolume_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

175:     function init(

182:     ) external initializer {

185:         __Component_init(main_);

186:         __EIP712_init(name_, VERSION);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

25:     constructor() initializer {}

33:     function __Component_init(IMain main_) internal onlyInitializing {

35:         __UUPSUpgradeable_init();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

48:     function __Trading_init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

110:     uint48 public startTime; // {s} when the dutch auction begins (one block after init()) lossy!

156:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

49:     uint256 public initBal; // {qSellTok}, this trade's balance of `sell` when init() was called

84:     function init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-18"></a>[L-18] Signature use at deadlines should be allowed
According to [EIP-2612](https://github.com/ethereum/EIPs/blob/71dc97318013bf2ac572ab63fab530ac9ef419ca/EIPS/eip-2612.md?plain=1#L58), signatures used on exactly the deadline timestamp are supposed to be allowed. While the signature may or may not be used for the exact EIP-2612 use case (transfer approvals), for consistency's sake, all deadlines should follow this semantic. If the timestamp is an expiration rather than a deadline, consider whether it makes more sense to include the expiration timestamp as a valid timestamp, as is done for deadlines.

*Instances (4)*:
```solidity
File: ./contracts/p1/BackingManager.sol

116:             _msgSender() == address(this) || tradeEnd[kind] < block.timestamp,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/StRSR.sol

313:         require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

292:         require(timepoint < block.timestamp, "future lookup");

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

233:         return status == TradeStatus.OPEN && endTime <= block.timestamp;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-19"></a>[L-19] Prevent accidentally burning tokens
Minting and burning tokens to address(0) prevention

*Instances (7)*:
```solidity
File: ./contracts/p1/RToken.sol

373:         _burn(caller, amtRToken);

496:         _mint(recipient, amtRToken);

515:         _burn(account, amtRToken);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

234:         mintStakes(caller, rsrAmount);

271:         _burn(account, stakeAmount);

387:         mintStakes(account, rsrAmount);

734:         _mint(account, stakeAmount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

### <a name="L-20"></a>[L-20] Possible rounding issue
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator. Also, there is indication of multiplication and division without the use of parenthesis which could result in issues.

*Instances (8)*:
```solidity
File: ./contracts/p1/BackingManager.sol

247:                 uint256 tokensPerShare = delta / (totals.rTokenTotal + totals.rsrTotal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/Distributor.sol

133:             if (totalShares != 0) tokensPerShare = amount / totalShares;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

409:         uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}

410:         uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

441:         uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

456:         uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

472:         seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

705:         uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

### <a name="L-21"></a>[L-21] Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*Instances (25)*:
```solidity
File: ./contracts/libraries/Fixed.sol

49: uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

120:     if (x < uint256(type(uint256).max / FIX_SCALE_SQ)) {

324:             if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

327:             x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

329:         return _safeWrap(result / FIX_SCALE);

511:             if (rawDelta / b != a) return FIX_MAX;

534:             if (shiftDelta / FIX_ONE > FIX_MAX) return FIX_MAX;

537:             return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

75:         available = throttle.lastAvailable + (limit * delta) / ONE_HOUR;

88:         limit = (supply * params.pctRate) / FIX_ONE_256; // {qRTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

260:             gas > (64 * GAS_FOR_BH_QTY) / 63 + GAS_FOR_DISABLE_BASKET,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

247:                 uint256 tokensPerShare = delta / (totals.rTokenTotal + totals.rsrTotal);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/Distributor.sol

133:             if (totalShares != 0) tokensPerShare = amount / totalShares;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/RToken.sol

409:         uint256 low = (FIX_ONE_256 * basketsNeeded_) / supply; // D18{BU/rTok}

410:         uint256 high = (FIX_ONE_256 * basketsNeeded_ + (supply - 1)) / supply; // D18{BU/rTok}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

448:             stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

463:             draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

504:         return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

609:             payout = (payoutRatio * rsrRewardsAtLastPayout) / FIX_ONE;

623:             : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

648:         uint256 newTotalDrafts = (draftRate * draftRSR) / FIX_ONE;

705:         uint192 withdrawal = _safeWrap((rsrWithdrawal * FIX_ONE + totalRSR - 1) / totalRSR); // {1}

729:         uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

82:         return (asMicroPercent * pastSupply + (ONE_HUNDRED_PERCENT - 1)) / ONE_HUNDRED_PERCENT;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

126:             minBuyAmount / MAX_ORDERS,

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-22"></a>[L-22] Solidity version 0.8.20+ may not work on other chains due to `PUSH0`
The compiler for Solidity 0.8.20 switches the default target EVM version to [Shanghai](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note), which includes the new `PUSH0` op code. This op code may not yet be implemented on all L2s, so deployment on these chains will fail. To work around this issue, use an earlier [EVM](https://docs.soliditylang.org/en/v0.8.20/using-the-compiler.html?ref=zaryabs.com#setting-the-evm-version-to-target) [version](https://book.getfoundry.sh/reference/config/solidity-compiler#evm_version). While the project itself may or may not compile with 0.8.20, other projects with which it integrates, or which extend this project may, and those projects will have problems deploying these contracts/libraries.

*Instances (26)*:
```solidity
File: ./contracts/libraries/Array.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Array.sol)

```solidity
File: ./contracts/libraries/Fixed.sol

4: pragma solidity ^0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/libraries/Permit.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/libraries/String.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/String.sol)

```solidity
File: ./contracts/libraries/Throttle.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Throttle.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Deployer.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Deployer.sol)

```solidity
File: ./contracts/p1/Distributor.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
File: ./contracts/p1/mixins/RewardableLib.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RewardableLib.sol)

```solidity
File: ./contracts/p1/mixins/TradeLib.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/TradeLib.sol)

```solidity
File: ./contracts/plugins/governance/Governance.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

```solidity
File: ./contracts/registry/VersionRegistry.sol

2: pragma solidity 0.8.19;

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/VersionRegistry.sol)

### <a name="L-23"></a>[L-23] Use `Ownable2Step.transferOwnership` instead of `Ownable.transferOwnership`
Use [Ownable2Step.transferOwnership](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol) which is safer. Use it as it is more secure due to 2-stage ownership transfer.

**Recommended Mitigation Steps**

Use <a href="https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol">Ownable2Step.sol</a>
  
  ```solidity
      function acceptOwnership() external {
          address sender = _msgSender();
          require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
          _transferOwnership(sender);
      }
```

*Instances (2)*:
```solidity
File: ./contracts/p1/Main.sol

6: import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

### <a name="L-24"></a>[L-24] Consider using OpenZeppelin's SafeCast library to prevent unexpected overflows when downcasting
Downcasting from `uint256`/`int256` in Solidity does not revert on overflow. This can result in undesired exploitation or bugs, since developers usually assume that overflows raise errors. [OpenZeppelin's SafeCast library](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) restores this intuition by reverting the transaction when such an operation overflows. Using this library eliminates an entire class of bugs, so it's recommended to use it always. Some exceptions are acceptable like with the classic `uint256(uint160(address(variable)))`

*Instances (13)*:
```solidity
File: ./contracts/libraries/Fixed.sol

75:     return uint192(x);

537:             return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}

554:         return uint192(raw); // don't need _safeWrap

605:         return uint192(result_256);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Fixed.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

456:         low = low256 >= FIX_MAX ? FIX_MAX : uint192(low256);

457:         high = high256 >= FIX_MAX ? FIX_MAX : uint192(high256);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Distributor.sol

221:                 revTotals.rsrTotal += uint24(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/StRSR.sol

448:             stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

463:             draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

623:             : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

663:         queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

114:         uint96 _sellAmount = uint96(

123:         uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-25"></a>[L-25] Unsafe ERC20 operation(s)

*Instances (3)*:
```solidity
File: ./contracts/libraries/Allowance.sol

25:         token.approve(spender, 0);

34:         try token.approve(spender, value) {

41:             token.approve(spender, type(uint256).max);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

### <a name="L-26"></a>[L-26] Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions
See [this](https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps) link for a description of this storage variable. While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

*Instances (72)*:
```solidity
File: ./contracts/libraries/Permit.sol

4: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

17:         if (AddressUpgradeable.isContract(owner)) {

19:                 IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==

25:                 SignatureCheckerUpgradeable.isValidSignatureNow(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/mixins/Auth.sol

4: import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

16: abstract contract Auth is AccessControlUpgradeable, IAuth {

89:         override(AccessControlUpgradeable, IAccessControlUpgradeable)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/Broker.sol

4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

22:     using SafeERC20Upgradeable for IERC20Upgradeable;

259:         IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

287:         IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

13:     using SafeERC20Upgradeable for IERC20Upgradeable;

170:             IERC20Upgradeable(address(erc20)).safeTransferFrom(

183:                     IERC20Upgradeable(address(erc20)).safeTransferFrom(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Main.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

22: contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {

42:         __UUPSUpgradeable_init();

89:         override(IAccessControlUpgradeable, AccessControlUpgradeable)

159:             abi.encodeWithSelector(UUPSUpgradeable.upgradeTo.selector, implementation)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

5: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

10: import "../vendor/ERC20PermitUpgradeable.sol";

17: contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {

20:     using SafeERC20Upgradeable for IERC20Upgradeable;

149:             IERC20Upgradeable(erc20s[i]).safeTransferFrom(

219:             IERC20Upgradeable(erc20s[i]).safeTransferFrom(

327:                 IERC20Upgradeable(erc20s[i]).safeTransferFrom(

420:         IERC20Upgradeable(address(erc20)).safeTransfer(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/StRSR.sol

4: import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

7: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

8: import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";

34: abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {

35:     using CountersUpgradeable for CountersUpgradeable.Counter;

36:     using SafeERC20Upgradeable for IERC20Upgradeable;

126:     mapping(address => CountersUpgradeable.Counter) private _nonces;

128:     mapping(address => CountersUpgradeable.Counter) private _delegationNonces;

237:         IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);

337:         IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);

477:         IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);

956:         CountersUpgradeable.Counter storage nonce = _nonces[owner];

962:         CountersUpgradeable.Counter storage nonce = _delegationNonces[owner];

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

4: import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol";

15: contract StRSRP1Votes is StRSRP1, IERC5805Upgradeable, IStRSRVotes {

59:         return SafeCastUpgradeable.toUint48(block.timestamp);

79:         return SafeCastUpgradeable.toUint48(_checkpoints[era][account].length);

140:             uint256 mid = length - MathUpgradeable.sqrt(length);

149:             uint256 mid = MathUpgradeable.average(low, high);

175:         address signer = ECDSAUpgradeable.recover(

271:                 ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);

276:                         val: SafeCastUpgradeable.toUint224(newWeight)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";

17:     ContextUpgradeable,

18:     UUPSUpgradeable,

35:         __UUPSUpgradeable_init();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

4: import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

19: abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

6: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

21:     using SafeERC20Upgradeable for IERC20Upgradeable;

205:         if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);

206:         if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

227:         IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-27"></a>[L-27] Upgradeable contract not initialized
Upgradeable contracts are initialized via an initializer function rather than by a constructor. Leaving such a contract uninitialized may lead to it being taken over by a malicious user

*Instances (107)*:
```solidity
File: ./contracts/libraries/Permit.sol

4: import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

17:         if (AddressUpgradeable.isContract(owner)) {

19:                 IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==

25:                 SignatureCheckerUpgradeable.isValidSignatureNow(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Permit.sol)

```solidity
File: ./contracts/mixins/Auth.sol

4: import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

16: abstract contract Auth is AccessControlUpgradeable, IAuth {

68:     function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

69:         __AccessControl_init();

89:         override(AccessControlUpgradeable, IAccessControlUpgradeable)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

18:     function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/AssetRegistry.sol

42:     function init(IMain main_, IAsset[] calldata assets_) external initializer {

43:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/AssetRegistry.sol)

```solidity
File: ./contracts/p1/BackingManager.sol

55:     ) external initializer {

56:         __Component_init(main_);

57:         __Trading_init(main_, maxTradeSlippage_, minTradeVolume_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BackingManager.sol)

```solidity
File: ./contracts/p1/BasketHandler.sol

113:     ) external initializer {

114:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/BasketHandler.sol)

```solidity
File: ./contracts/p1/Broker.sol

4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

22:     using SafeERC20Upgradeable for IERC20Upgradeable;

80:     ) external initializer {

81:         __Component_init(main_);

259:         IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

287:         IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Broker.sol)

```solidity
File: ./contracts/p1/Distributor.sol

4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

13:     using SafeERC20Upgradeable for IERC20Upgradeable;

44:     function init(IMain main_, RevenueShare calldata dist) external initializer {

45:         __Component_init(main_);

170:             IERC20Upgradeable(address(erc20)).safeTransferFrom(

183:                     IERC20Upgradeable(address(erc20)).safeTransferFrom(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Distributor.sol)

```solidity
File: ./contracts/p1/Furnace.sol

33:     function init(IMain main_, uint192 ratio_) external initializer {

34:         __Component_init(main_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Furnace.sol)

```solidity
File: ./contracts/p1/Main.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

22: contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {

30:     constructor() initializer {}

38:     ) public virtual initializer {

40:         __Auth_init(shortFreeze_, longFreeze_);

41:         __ComponentRegistry_init(components);

42:         __UUPSUpgradeable_init();

45:         emit MainInitialized();

89:         override(IAccessControlUpgradeable, AccessControlUpgradeable)

159:             abi.encodeWithSelector(UUPSUpgradeable.upgradeTo.selector, implementation)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/p1/RToken.sol

5: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

10: import "../vendor/ERC20PermitUpgradeable.sol";

17: contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {

20:     using SafeERC20Upgradeable for IERC20Upgradeable;

68:     ) external initializer {

72:         __Component_init(main_);

73:         __ERC20_init(name_, symbol_);

74:         __ERC20Permit_init(name_);

149:             IERC20Upgradeable(erc20s[i]).safeTransferFrom(

219:             IERC20Upgradeable(erc20s[i]).safeTransferFrom(

327:                 IERC20Upgradeable(erc20s[i]).safeTransferFrom(

420:         IERC20Upgradeable(address(erc20)).safeTransfer(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RToken.sol)

```solidity
File: ./contracts/p1/RevenueTrader.sol

32:     ) external initializer {

34:         __Component_init(main_);

35:         __Trading_init(main_, maxTradeSlippage_, minTradeVolume_);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/RevenueTrader.sol)

```solidity
File: ./contracts/p1/StRSR.sol

4: import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

7: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

8: import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";

34: abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {

35:     using CountersUpgradeable for CountersUpgradeable.Counter;

36:     using SafeERC20Upgradeable for IERC20Upgradeable;

126:     mapping(address => CountersUpgradeable.Counter) private _nonces;

128:     mapping(address => CountersUpgradeable.Counter) private _delegationNonces;

182:     ) external initializer {

185:         __Component_init(main_);

186:         __EIP712_init(name_, VERSION);

237:         IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);

337:         IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);

477:         IERC20Upgradeable(address(rsr)).safeTransfer(caller, seizedRSR);

956:         CountersUpgradeable.Counter storage nonce = _nonces[owner];

962:         CountersUpgradeable.Counter storage nonce = _delegationNonces[owner];

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/p1/StRSRVotes.sol

4: import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

5: import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/interfaces/IERC5805Upgradeable.sol";

15: contract StRSRP1Votes is StRSRP1, IERC5805Upgradeable, IStRSRVotes {

59:         return SafeCastUpgradeable.toUint48(block.timestamp);

79:         return SafeCastUpgradeable.toUint48(_checkpoints[era][account].length);

140:             uint256 mid = length - MathUpgradeable.sqrt(length);

149:             uint256 mid = MathUpgradeable.average(low, high);

175:         address signer = ECDSAUpgradeable.recover(

271:                 ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);

276:                         val: SafeCastUpgradeable.toUint224(newWeight)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSRVotes.sol)

```solidity
File: ./contracts/p1/mixins/Component.sol

4: import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5: import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6: import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";

17:     ContextUpgradeable,

18:     UUPSUpgradeable,

25:     constructor() initializer {}

33:     function __Component_init(IMain main_) internal onlyInitializing {

35:         __UUPSUpgradeable_init();

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)

```solidity
File: ./contracts/p1/mixins/Trading.sol

4: import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

19: abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {

48:     function __Trading_init(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Trading.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

102:     ITrading public origin; // the address that initialized the contract

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

```solidity
File: ./contracts/plugins/trading/GnosisTrade.sol

6: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

21:     using SafeERC20Upgradeable for IERC20Upgradeable;

205:         if (sellBal != 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);

206:         if (boughtAmt != 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

227:         IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/GnosisTrade.sol)

### <a name="L-28"></a>[L-28] Use `initializer` for public-facing functions only. Replace with `onlyInitializing` on internal functions.
See [What's the difference between onlyInitializing and initializer](https://forum.openzeppelin.com/t/whats-the-difference-between-onlyinitializing-and-initialzer/25789) and https://docs.openzeppelin.com/contracts/4.x/api/proxy#Initializable-onlyInitializing--

*Instances (1)*:
```solidity
File: ./contracts/p1/mixins/Component.sol

25:     constructor() initializer {}

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/Component.sol)


## Medium Issues


| |Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | Contracts are vulnerable to fee-on-transfer accounting-related issues | 2 |
| [M-2](#M-2) | Centralization Risk for trusted owners | 26 |
| [M-3](#M-3) | Fees can be set to be greater than 100%. | 1 |
| [M-4](#M-4) | `increaseAllowance/decreaseAllowance` won't work on mainnet for USDT | 2 |
| [M-5](#M-5) | Library function isn't `internal` or `private` | 6 |
| [M-6](#M-6) | Direct `supportsInterface()` calls may cause caller to revert | 1 |
### <a name="M-1"></a>[M-1] Contracts are vulnerable to fee-on-transfer accounting-related issues
Consistently check account balance before and after transfers for Fee-On-Transfer discrepancies. As arbitrary ERC20 tokens can be used, the amount here should be calculated every time to take into consideration a possible fee-on-transfer or deflation.
Also, it's a good practice for the future of the solution.

Use the balance before and after the transfer to calculate the received amount instead of assuming that it would be equal to the amount passed as a parameter. Or explicitly document that such tokens shouldn't be used and won't be supported

*Instances (2)*:
```solidity
File: ./contracts/p1/StRSR.sol

237:         IERC20Upgradeable(address(rsr)).safeTransferFrom(caller, address(this), rsrAmount);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

```solidity
File: ./contracts/plugins/trading/DutchTrade.sol

219:         buy.safeTransferFrom(msg.sender, address(this), amountIn);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/trading/DutchTrade.sol)

### <a name="M-2"></a>[M-2] Centralization Risk for trusted owners

#### Impact:
Contracts have owners with privileged rights to perform admin tasks and need to be trusted to not perform malicious updates or drain funds.

*Instances (26)*:
```solidity
File: ./contracts/mixins/Auth.sol

16: abstract contract Auth is AccessControlUpgradeable, IAuth {

90:         onlyRole(getRoleAdmin(role))

124:     function freezeShort() external onlyRole(SHORT_FREEZER) {

139:     function freezeLong() external onlyRole(LONG_FREEZER) {

152:     function freezeForever() external onlyRole(OWNER) {

161:     function unfreeze() external onlyRole(OWNER) {

169:     function pauseTrading() external onlyRole(PAUSER) {

176:     function unpauseTrading() external onlyRole(PAUSER) {

183:     function pauseIssuance() external onlyRole(PAUSER) {

190:     function unpauseIssuance() external onlyRole(PAUSER) {

198:     function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

205:     function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/Auth.sol)

```solidity
File: ./contracts/mixins/ComponentRegistry.sol

12: abstract contract ComponentRegistry is Initializable, Auth, IComponentRegistry {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/mixins/ComponentRegistry.sol)

```solidity
File: ./contracts/p1/Main.sol

22: contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {

61:     function setVersionRegistry(VersionRegistry versionRegistry_) external onlyRole(OWNER) {

70:     function setAssetPluginRegistry(AssetPluginRegistry registry_) external onlyRole(OWNER) {

79:     function setDAOFeeRegistry(DAOFeeRegistry feeRegistry_) external onlyRole(OWNER) {

99:     function upgradeMainTo(bytes32 versionHash) external onlyRole(OWNER) {

115:     ) external onlyRole(OWNER) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/Main.sol)

```solidity
File: ./contracts/registry/AssetPluginRegistry.sol

4: import { Ownable } from "@openzeppelin/contracts/access/Ownable.sol";

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/AssetPluginRegistry.sol)

```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

44:     function setFeeRecipient(address feeRecipient_) external onlyOwner {

56:     function setDefaultFeeNumerator(uint256 feeNumerator_) external onlyOwner {

66:     function setRTokenFeeNumerator(address rToken, uint256 feeNumerator_) external onlyOwner {

76:     function resetRTokenFee(address rToken) external onlyOwner {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

```solidity
File: ./contracts/registry/RoleRegistry.sol

5: import { AccessControlEnumerable } from "@openzeppelin/contracts/access/AccessControlEnumerable.sol";

11: contract RoleRegistry is AccessControlEnumerable {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/RoleRegistry.sol)

### <a name="M-3"></a>[M-3] Fees can be set to be greater than 100%.
There should be an upper limit to reasonable fees.
A malicious owner can keep the fee rate at zero, but if a large value transfer enters the mempool, the owner can jack the rate up to the maximum and sandwich attack a user.

*Instances (1)*:
```solidity
File: ./contracts/registry/DAOFeeRegistry.sol

76:     function resetRTokenFee(address rToken) external onlyOwner {
            rTokenFeeNumerator[rToken] = 0;
            rTokenFeeSet[rToken] = false;
    
            emit RTokenFeeNumeratorSet(rToken, 0, false);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/registry/DAOFeeRegistry.sol)

### <a name="M-4"></a>[M-4] `increaseAllowance/decreaseAllowance` won't work on mainnet for USDT
On mainnet, the mitigation to be compatible with `increaseAllowance/decreaseAllowance` isn't applied: https://etherscan.io/token/0xdac17f958d2ee523a2206206994597c13d831ec7#code, meaning it reverts on setting a non-zero & non-max allowance, unless the allowance is already zero.

*Instances (2)*:
```solidity
File: ./contracts/p1/StRSR.sol

800:     function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {

806:     function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/StRSR.sol)

### <a name="M-5"></a>[M-5] Library function isn't `internal` or `private`
In a library, using an external or public visibility means that we won't be going through the library with a DELEGATECALL but with a CALL. This changes the context and should be done carefully.

*Instances (6)*:
```solidity
File: ./contracts/libraries/Allowance.sol

5:     function approve(address spender, uint256 value) external;

7:     function allowance(address owner, address spender) external view returns (uint256);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/libraries/Allowance.sol)

```solidity
File: ./contracts/p1/mixins/BasketLib.sol

164:     function nextBasket(

316:     function requireConstantConfigTargets(

351:     function normalizeByPrice(

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/BasketLib.sol)

```solidity
File: ./contracts/p1/mixins/RecollateralizationLib.sol

33:     function prepareRecollateralizationTrade(TradingContext memory ctx, Registry memory reg)

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/p1/mixins/RecollateralizationLib.sol)

### <a name="M-6"></a>[M-6] Direct `supportsInterface()` calls may cause caller to revert
Calling `supportsInterface()` on a contract that doesn't implement the ERC-165 standard will result in the call reverting. Even if the caller does support the function, the contract may be malicious and consume all of the transaction's available gas. Call it via a low-level [staticcall()](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/f959d7e4e6ee0b022b41e5b644c79369869d8411/contracts/utils/introspection/ERC165Checker.sol#L119), with a fixed amount of gas, and check the return code, or use OpenZeppelin's [`ERC165Checker.supportsInterface()`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/f959d7e4e6ee0b022b41e5b644c79369869d8411/contracts/utils/introspection/ERC165Checker.sol#L36-L39).

*Instances (1)*:
```solidity
File: ./contracts/plugins/governance/Governance.sol

180:         return super.supportsInterface(interfaceId);

```
[Link to code](https://github.com/code-423n4/2024-07-reserve/blob/main/contracts/plugins/governance/Governance.sol)

