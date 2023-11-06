# Multichain conditions

We can condition access to the state on two different networks. To do that, we create two (or more) conditions that target different `chain` ids and then compose them together using `CompoundCondition`

<pre class="language-typescript"><code class="lang-typescript"><strong>import { conditions } from '@nucypher/taco'
</strong><strong>
</strong><strong>const ownsNFTOnChain5 = new conditions.predefined.ERC721Ownership({
</strong>  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
  chain: 5, // The first network we target
});

const hasAnyNativeAssetOnChain1 = new conditions.base.RpcCondition({
  chain: 1, // The second network we target
  method: 'eth_getBalance',
  parameters: [':userAddress'],
  returnValueTest: {
    comparator: '>',
    value: 0,
  }
});

// Let's combine our conditions
const multichainCondition = conditions.base.CompoundCondition.and([
  ownsNFTOnChain5, hasAnyNativeAssetOnChain1
]);

</code></pre>

### Learn more&#x20;

* [references.md](../../references.md "mention")
* [advanced-usage](../../advanced-usage/ "mention")
