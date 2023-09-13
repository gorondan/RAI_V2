***
# Iliquid staking token as collateral for GEB based stablecoin systems
This material introduces for public discussion the following concepts:
- #### iliquid staking and integration with GEB stablecoin systems
- #### minimal governance RAI V2 as a single collateral HAI fork

<!-- 
CREDITS:
 @gorondan
 @fabiohild 
 @geode-main 
 @wei3erHase
 @bkellerman
  -->

What is iliquid staking?  
Iliquid staking can be explained as staking ETH with a deposit contract and receiving a staking receipt token that has no liquidity, thus is does not unlock the value of the underlying Ether. *What it does*, is hold the receipt of the staked ETH and the immutable right to withdraw that ETH. It is also transferable and fractional. 

What is iliquid staking token (iST)?  
Iliquid staking token - an ERC1155 token/NFT with the following two params: `Balances` and `pricePerShare`

- `Balances` represents the amount of ETH staked by an address in a staking contract like a private or public staking pool

- `pricePerShare` represents the amount of underlying ETH each Balances unit holds 
    
    can be > 1.00 thanks to staking rewards accumulation, or 

    can be < 1.00 if the validator is slashed - however key security management lowers that risk

> The defining parameters of the iliquid staking token are inherited from the permissionless staking infrastructure dSentra is integrating with and testing on Goerli test-net (provided by Geodefi)
****
It's paramount for the staking framework to be able to offer full ownership for the iliquid staking protocol and codebase integrability. Also features like optional interfaces and middlewares as external contracts, limited upgradability, pool segregation, etc.
****

Practical applications:
### i.  vertical integration of staking ETH and stablecoin minting. 

>*Iliquid staking* can be compatible with the MoneyGod ecosystem stablecoins built on-top of the GEB framework, provided a `CollateralERC1155Join` is created to on-board the collateral, so the `collateralType` within the safeEngine is determined by the ERC1155 address and a numerical ID  

User calls the GEB infrastructure to mint stablecoin, locking staking receipt tokens as collateral.

Maintaining the stablecoin's mechanism stability is vital so it's important to integrate/accomodate iliquid staking with crucial GEB components like Accounting Engine, Liquidation Engine and Collateral Auction House contracts.

A strategy that is pessimistic about validators exit can be set-up:
- set a higher collateralization ratio for this `collateralType`. 165-200% max. as UX should be taken into account though when selecting risk profile
- the liquidation part of the system would need to be changed as it will require a COIN/COLLATERAL pool. Liquity's stability pool is a good example.   
In the case of illiquid staking, users would be incentivised to deposit ETH and COIN (RAI_V2/HAI/TAI) in the pool, the ETH is staked (by the pool contract) and the iliquid staking token (COLLATERAL) deposited in the pool. Users get the staking yield and profits from liquidations (pool always buys dstETH with a discount). Pool could be rebalanced with incentives (users joining with the token with lowest share get a discount, MEV arbers would do this).  
The system uses the pool to swap COIN for COLLATERAL and liquidate positions in one tx.

>This approach to liquidation, where there is a bounded COIN/COLLATERAL pool, allows the system to liquidate undercollateralized SAFEs atomically, independent of the market, protecting the stability of the stablecoin system, without the need to trigger validators to exit, leaving this process for the pool participants.
However, for extreme situations or for when the pool has insufficient COLLATERAL or COIN, the liquidation would imply validator exit. 
For a new system, like RAI_V2, `surplusBuffer` and `debtQueue` parameters can be reconsidered/recalibrated to offer supplementary safeguard for the system.


### ii. minimal governance RAI V2 as a single collateral HAI fork
It's a strong belief of most of the participants in this thesis, and quite a few of the Money God community members that RAI_V2 should not mean killing the current system.
It should stay active as the purest stablecoin possible (only pure ETH, mostly ungoverned).   
We feel that if RAI_V2 comes to fruition it should be another system, as it will need more governance, and will have more risks (multiple types of LSDs, each having smart contract risks and the risk of validator slashing).
>A RAI_V2 system with iliquid staking as the only collateral could be very similar to current iteration of RAI (minimal governance, only risk in addition to RAI V2 contracts would be smart contract risk of the staking framework, and slashing of validators). It would have considerably lower tail risk than a multi collateral one.
 
This envisioned version of RAI_V2 could be built upon HAI codebase, which is a fork of GEB. Their version of the system is modern and simpler. Also cheaper to deploy, as RAI_V2 would be on mainnet.

Pros and cons *minimal governance RAI V2 as a single collateral HAI fork + iliquid staking*

pros:
+ no liquidity model needed
+ it lowers the risk associated to all the miriades of LSD's  
+ allows a RAI V2 without much governance added than current iteration
+ no external liquidity needed for starting (COIN is systemCOIN) 

cons:  
- harder to integrate, more code adaptation
- could mean lower TVL and/or UX

LINKS:  
[Reflexer forum posts](https://community.reflexer.finance/t/exploring-the-potential-of-liquid-staking-and-rai/455/7)  
[GEB docs](https://docs.reflexer.finance/)  
[HAI docs](https://docs.letsgethai.com)