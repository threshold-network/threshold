# Multichain conditions

We can condition access to the state on two different networks. To do that, we create two (or more) conditions that target different `chain` ids and then compose them together using `CompoundCondition`

<pre class="language-typescript"><code class="lang-typescript"><strong>import { conditions } from '@nucypher/taco';
</strong><strong>
</strong><strong>const ownsSpecificNFTOnPolygon = new conditions.predefined.erc721.ERC721Ownership({
</strong>  chain: 137, // The first network we target - polygon mainnet
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  parameters: [3591],
});

const ownsSufficientMainnetETH = new conditions.base.rpc.RpcCondition({
  chain: 1, // The second network we target - eth mainnet
  method: 'eth_getBalance',
  parameters: [':userAddress'],
  returnValueTest: {
    comparator: '>',
    value: 10,
  }
});

// Let's combine our conditions
const multichainCondition = conditions.compound.CompoundCondition.and([
  ownsSpecificNFTOnPolygon, ownsSufficientMainnetETH
]);

</code></pre>

### Learn more&#x20;

* [references.md](../../references.md "mention")
* [advanced-usage](../../advanced-usage/ "mention")
