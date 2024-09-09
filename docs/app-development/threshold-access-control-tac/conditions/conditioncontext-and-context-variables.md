# ConditionContext and Context Variables

In this section, we introduce the `ConditionContext`, a container for dynamic values to be used in conditions at decryption time (e.g. decryptor's wallet address). We call those values "context variables" and can recognize them by the `:` prefix. They act as a placeholder to be specified at the encryption time and filled at the decryption time.

Context variables are broken up into two groups:

*   _Reserved context variables_: these have special functionality within `taco`. There are two such context variables:

    * `:userAddress`
    * `:userAddressExternalEIP4361`

    These context variables require the use of [Authentication Providers](conditioncontext-and-context-variables.md#authentication-providers) to provide verifiable proof of values specified by the decryptor. Applications should be cognizant of which reserved context variable they use based on their needs.
* _Custom context variables_:  these are application-specific, simple key-value pairs where the decryptor can directly specify the values without any verification.

## Authentication Providers

Some dynamic conditions require specific information about the decryptor, which needs to be verified/authenticated, such as wallet address or other identity-related information. This verification needs to be done in a way that doesn't allow the decryptor to simply provide \*\*any\*\* value. Instead, the decryptor should provide proof that can be verified so that the validity of the value can be confirmed and the value subsequently used for properly evaluating conditions.

In the case of a wallet address, the decryptor must sign a message with the private key corresponding to the public wallet address. This signature serves as proof of ownership, which can then be verified by nodes before using the corresponding wallet address for decryption condition evaluation. Otherwise, the decryptor could simply specify a wallet address that they do not own but still satisfy the required condition e.g. pretend to be `vitalik.eth` to satisfy an ETH balance condition.

`AuthProvider` is an abstraction provided by `@nucypher/taco-auth` that plays a critical role in generating the necessary proof for verifying information about the decryptor. This proof is then used for condition evaluation during the decryption process.&#x20;

Instead of directly providing the necessary information (e.g., wallet address), which could be falsified, the decryptor uses an `AuthProvider` to generate the requisite proof. At the moment, there are two `AuthProvider` implementations, with more expected to be added in the future:

* `EIP4361AuthProvider`
* `SingleSignOnEIP4361AuthProvider`

### `EIP4361AuthProvider`

This implementation must be used whenever the `:userAddress` context variable is present in a decryption condition. It prompts the user to sign a  `EIP4361` ([Sign-in With Ethereum](https://docs.login.xyz/general-information/siwe-overview/eip-4361)) message to authenticate the decryptor's wallet address at decryption time. This signature is provided to nodes to prove wallet address ownership when evaluating the decryption condition.

```typescript
import { conditions } from '@nucypher/taco';
import { EIP4361AuthProvider, USER_ADDRESS_PARAM_DEFAULT } from '@nucypher/taco-auth';

const conditionContext = conditions.context.ConditionContext.fromMessageKit(messageKit);
  
const web3Provider = new ethers.providers.Web3Provider(window.ethereum);
  
const authProvider = new EIP4361AuthProvider(
  web3Provider,
  web3Provider.getSigner(),
);

// set auth provider for ":userAddress"
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);
```

{% hint style="info" %}
To negate the need for repeated wallet signatures for every decryption request by the same decryptor, the corresponding proof that is generated is cached until an expiry is triggered, after which the decryptor will be prompted again.
{% endhint %}

### `SingleSignOnEIP4361AuthProvider`

This implementation must be used whenever the `:userAddressExternalEIP4361` context variable is present in the decryption condition.&#x20;

It integrates decryptor wallet authentication into a broader identity management system, allowing users to authenticate once and access multiple services.

However, unlike the `EIP4361AuthProvider` it integrates with an underlying application that already uses Sign-in With Ethereum (SIWE) for identity management and requires users to sign in before using the application. Therefore, the existing application-specific sign-in message and signature can be reused with TACo and provide a seamless user experience during TACo decryption, without the need to sign multiple messages.

```typescript
import { conditions } from '@nucypher/taco';
import { 
  SingleSignOnEIP4361AuthProvider,
  USER_ADDRESS_PARAM_EXTERNAL_EIP4361,
} from '@nucypher/taco-auth';

const conditionContext = conditions.context.ConditionContext.fromMessageKit(messageKit);
  
const {messageStr, signature} = ...;  // existing SIWE information from application  

const authProvider = SingleSignOnEIP4361AuthProvider.fromExistingSiweInfo(
  messageStr,
  signature,
);

// set auth provider for ":userAddressExternalEIP4361"
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_EXTERNAL_EIP4361, authProvider);
```

## Illustrative Example

Let's take a look at this `ContractConditon` example:

```typescript
import { conditions, domains, encrypt } from '@nucypher/taco';

const ownsNFTRaw = new conditions.contract.ContractCondition({
  method: 'balanceOf',
  parameters: [':userAddress'], // <- A context variable
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 1,
  returnValueTest: {
    comparator: '>',
    value: ':selectedBalance', // <- Another context variable
  },
});

const message = ...  // message to encrypt

const messageKit = await encrypt(
  web3Provider,
  domains.MAINNET,
  message,
  ownsNFTRaw,
  ritualId,
  web3Provider.getSigner(),
);
```

In this example, we can see two different context variables

* `:userAddress` - A reserved context variable
* `:selectedBalance` - A custom context variable

To replace the `:userAddress` context variable with an actual wallet address during decryption, `taco` needs to be provided with an `AuthProvider` for the user to sign an `EIP4361` ([Sign-in With Ethereum](https://docs.login.xyz/general-information/siwe-overview/eip-4361)) message to confirm wallet ownership at the decryption time.

Additionally, the `:selectedBalance` custom context variable has to be provided to the `decrypt` function by the decryptor.

Both context variables need to be provided to a `ConditionContext` which is then used by the `decrypt` function.

```typescript
import { decrypt, conditions } from '@nucypher/taco';
import { EIP4361AuthProvider, USER_ADDRESS_PARAM_DEFAULT } from '@nucypher/taco-auth';

const web3Provider = new ethers.providers.Web3Provider(window.ethereum);

const conditionContext =
  conditions.context.ConditionContext.fromMessageKit(messageKit);
  
// satisfy ":userAddress"
const authProvider = new EIP4361AuthProvider(
  web3Provider,
  web3Provider.getSigner(),
);
conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);

// satisfy ":selectedBalance"; simple key-value pair
const customParameters: Record<string, conditions.CustomContextParam> = {
  ':selectedBalance': 2,
};
conditionContext.addCustomContextParameterValues(customParameters);

const decryptedMessage = await decrypt(
  web3Provider,
  domains.MAINNET,
  messageKit,
  conditionContext,
);
```

With those context parameters, the condition will be evaluated by nodes at decryption time to be:

```typescript
{
  method: 'balanceOf',
  parameters: ['0x...'], // The address of decryptor's wallet (verified via signature)
  standardContractType: 'ERC721',
  contractAddress: '0x1e988ba4692e52Bc50b375bcC8585b95c48AaD77',
  chain: 1,
  returnValueTest: {
    comparator: '>',
    value: 2, // Concrete value to be used in comparison
  },
}
```

{% hint style="info" %}
This is a contrived example. \
\
For **custom context variables** specifically, time should be taken to think through the use case since these are provided by the decryptor and can be set to any value. In this case the `selectedBalance`  value can simply be set to `-1` by the requester which would grant them access without owning any NFT.\
\
There is a place for custom context variables e.g. providing a Merkle tree root as a parameter to a contract function, but such an example would be overly complex.
{% endhint %}

## Checking required context variables

If your application utilizes many different conditions each with different context variables, the required context variables for decryption can be queried.&#x20;

The `requestedContextParameters` property of the `ConditionContext` object can be used to identify the necessary context variables for decryption. By querying this property, the application can understand the context variables required for the relevant condition.

In this way, the application doesn't need to have prior knowledge of the condition used but can dynamically determine the context variables needed based on the specific condition being handled.

```typescript
// create condition context
const conditionContext = conditions.context.ConditionContext.fromMessageKit(messageKit);

// illustrative optional example of checking what context parameters are required
if (
  conditionContext.requestedContextParameters.has(USER_ADDRESS_PARAM_DEFAULT)
) {
  // add authentication for ":userAddress" in condition
  const authProvider = new EIP4361AuthProvider(
    provider,
    provider.getSigner()
  );
  conditionContext.addAuthProvider(USER_ADDRESS_PARAM_DEFAULT, authProvider);
}
if (
  conditionContext.requestedContextParameters.has(":selectedBalance")
) {  
  // satisfy ":selectedBalance"; simple key-value pair
  const customParameters: Record<string, conditions.CustomContextParam> = {
    ':selectedBalance': 2,
  };
  conditionContext.addCustomContextParameterValues(customParameters);
}
// other context variable checks ...

const decryptedMessage = await decrypt(
  web3Provider,
  domains.MAINNET,
  messageKit,
  conditionContext,
);
```

### Learn more&#x20;

* [references.md](../references.md "mention")
* [advanced-usage](../advanced-usage/ "mention")
