# Simple Overview

TACo is a plug-in service that enables the safe sharing of private or sensitive data within Web3 applications. Private data is encrypted by a data owner and remains encrypted until it reaches the device of a recipient. From a privacy point of view, it is comparable to the end-to-end encryption that a mobile messaging service like Signal offers.&#x20;

Whether or not a recipient qualifies to see a given piece of data depends on whether they are able to show that they fulfill certain conditions. These conditions are chosen in advance by the owner of that data, and often (but not exclusively) involve holding a special-purpose NFT.&#x20;

Here are some real-world examples of TACo in action: \
\
_(1) A graphic novelist wants to sell her work as a digital product, and include some bonus pages for those who pay a little more. She paywalls access to the main novel using NFT-A, and the bonus material with NFT-B._&#x20;

_Those who buy the NFT-A are able to see the main part of the novel, and those who buy and hold both NFTs A & B are able to view the entire thing. Actually, anyone can download the novel in encrypted form, but without one of the two NFTs it can't be decrypted and is therefore useless._ \
\
_(2) A game developer wants to include a special map in their latest version for players who downloaded the original game in 2021, as a thanks for those who helped support the game studio in its early days._&#x20;

_They encrypt the map build file and link it to two conditions; (i) that the player holds an NFT in their wallet that they would have received when they originally purchased the game, and (ii) that the player held this NFT before January 1st 2022. In this case, just holding the NFT is not enough to access the special map._&#x20;

In both these cases, the person (or device) trying to see the private data (the graphic novel and the game map) has to prove to the Threshold network that they are supposed to have access. More specifically, they have to contact a group of Threshold nodes running TACo software, and prove to them that they hold the correct NFTs – and satisfy any other specified conditions, like having held them for a certain time.&#x20;

If a sufficient number of nodes confirm that the requesting person/device qualifies to see the data, they will send them some key fragments. These fragments can be put together by the requestor on their device, which produces a decrypting key. This decrypting key can then be used to decrypt the original private data.
