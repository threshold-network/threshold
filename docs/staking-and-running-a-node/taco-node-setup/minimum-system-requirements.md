# Minimum System Requirements

TACo nodes are very light and can be run inexpensively on a remote instance or local machine. Specs similar to (or greater) than the following will suffice: &#x20;

* **Debian** or **Ubuntu** (recommended distributions)
* **20GB** storage
* **4GB** RAM
* **x86** architecture
* Static IP address
* Exposed TCP port **9151**

TACo can be run on cloud infrastructure. For example, a Digital Ocean [4GB Basic Droplet](https://www.digitalocean.com/pricing/droplets) satisfies the requirements listed above.

Additionally, the operator account (i.e. not the provider) must be funded with at least 15 MATIC (Polygon POS) to connect to the Threshold Network and participate in DKG initialization rituals. Transfer these funds before running your node.

{% hint style="warning" %}
Note that TACo is currently in an invite-only beta phase, which implies a low volume of DKG rituals, and consequently, of MATIC spending, but the volume may increase in the future. Concerning this, DKG ritual costs may be reimbursed to node operators in later versions.
{% endhint %}
