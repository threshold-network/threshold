# Initial Protocol Loan

The Initial Protocol Loan (IPL) is a debt issued against the protocol itself in order to fund the stability pool. Even though it's technically debt, it does not in itself make the protocol a fractional reserve because all funds are accounted for.&#x20;

### **How it works**

thUSD is minted by the PCV and deposited into the stability pool. A freeze on withdraw is initiated until debt is fully repaid. At any time, the Threshold DAO can initiate a withdraw, which will destruct the debt and withdraw exceeds. If the PCV for some reason has less than the debt available, withdraw is denied.&#x20;

Profits from usage of the protocol (loan interest, redemption fee, etc) accrues into the PCV, so in an event where there are less funds than debt the protocol first has to earn back the missing funds.&#x20;

_**Benefits of the hybrid PCV + IPL model:**_

* Bootstrap stability pool for free
* No need for LQTY token = all profits accrue directly to the PCV
* Immediate surplus on first loan drawn / liquidation
* Predictability and (once debt is repaid) high resilience against Black Swans
* No idle capital (it doesn't "really" exist)

_**Disadvantages**_

* Higher risk of under-collateralization during the initial stages
* Poor management of PCV could result in not enough funds in the stability pool

### About the risk

Each liquidation that occurs will draw against the stability pool and result in \~10% profit to the stability pool. This also means that the price can drop up to 10% after a liquidation before the stability pool is at risk of losing money on the liquidation. Our integration with [B.Protocol](../b.-protocol.md) ensures that liquidated tBTC is automatically converted back to thUSD and re-deposited into the stability pool. Under normal circumstances and without other factors, it is expected that the balance of thUSD in the pool will grow over time.&#x20;

But there can be Black Swan events, large drops in the price of BTC in a short period of time or liquidity issues that results in a loss, even at \~10% profit. This is a risk of the protocol becoming undercollateralized, but the same issue would apply even without debt (stability pool not being profitable). The protocol is created to be sustainable, so these types of rare events will be averaged out. In addition, all income streams from Threshold USD also ends up in the PCV which further decrease the risk of undercollaterization.&#x20;

The undercollaterialization is mostly a concern during the initial bootstrapping phase. As interest accrue and repay the initial debt, the funds in the stability pool will be replaced by fully owned PVC, thus eliminating this disadvantage.
