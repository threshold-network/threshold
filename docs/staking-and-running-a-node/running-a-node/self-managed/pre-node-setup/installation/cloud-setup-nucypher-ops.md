---
description: Automatic set up of a TACo node on cloud
---

# Cloud Setup (nucypher-ops)

This tutorial makes use of `nucypher-ops`_:_ an Ansible script that will setup and configure a TACo node for you. The following steps help you launch a node on a DigitalOcean droplet, but `nucypher-ops` supports both Digital Ocean and AWS.&#x20;

{% hint style="warning" %}
This script automates certain steps to launch a TACo node, but **does not implement security measures of any sort**. You alone are responsible for operational security, as well as backing up relevant data. Carefully review your firewall rules and make the necessary adjustments.&#x20;
{% endhint %}

## Local Machine Requirements

{% tabs %}
{% tab title="MacOS" %}
Install _Python_ and _pip_ on your local machine:

1. Visit [Python.org ](https://www.python.org/downloads/macos/)
2. Visit [pypa.io](https://pip.pypa.io/en/stable/getting-started/)
{% endtab %}

{% tab title="Linux" %}
Update your local machine and install dependencies:

{% code overflow="wrap" %}
```bash
sudo apt update
sudo apt upgrade
sudo apt-get install libffi-dev python3-dev python3-pip python3-virtualenv build-essential libssl-dev
```
{% endcode %}
{% endtab %}

{% tab title="Windows" %}
Install Ubuntu 20.04.4 LTS through the Windows Store:

<figure><img src="../../../../../.gitbook/assets/ubuntu-wsl.jpeg" alt=""><figcaption><p>WSL is the most convenient way to run nucypher-ops on a Windows based local machine</p></figcaption></figure>

Update your local machine and install dependencies:

{% code overflow="wrap" %}
```bash
sudo apt update
sudo apt upgrade
sudo apt-get install libffi-dev python3-dev python3-pip python3-virtualenv build-essential libssl-dev
```
{% endcode %}
{% endtab %}
{% endtabs %}

## 1. Connect to DigitalOcean via an SSH Key

Generate an SSH key pair (RSA) and output the **public key**:&#x20;

```
$ ssh-keygen -t rsa
$ cat .ssh/id_rsa.pub
```

<figure><img src="../../../../../.gitbook/assets/ssh-key-gen.jpg" alt=""><figcaption><p>Generating a new SSH Key</p></figcaption></figure>

Copy the highlighted section of the public key:&#x20;

<figure><img src="../../../../../.gitbook/assets/ssh-copy-this.jpg" alt=""><figcaption><p>New SSH Key</p></figcaption></figure>

In your Digital Ocean dashboard, go to **Settings** > **Security** and click the **Add SSH Key** button:

<figure><img src="../../../../../.gitbook/assets/ssh-key-do-arrows.jpg" alt=""><figcaption><p>Adding a new SSH Key</p></figcaption></figure>

Paste the key into the box, give your key a name, and click the **Add SSH Key** button:

<figure><img src="../../../../../.gitbook/assets/ssh-key-do-pasted.jpg" alt=""><figcaption><p>Adding a new SSH Key</p></figcaption></figure>

Open a text file or password manager, and copy the SSH Fingerprint into it:

{% hint style="warning" %}
Do not share the SSH Fingerprint with anyone. Save and store it in a secure place.&#x20;
{% endhint %}

<figure><img src="../../../../../.gitbook/assets/ssh-fingerprint.jpg" alt=""><figcaption><p>Locating the SSH Fingerprint</p></figcaption></figure>

Create an API key by going to **API** > **Tokens/Keys** and click the **Generate New Token** button.

<figure><img src="../../../../../.gitbook/assets/api-generate-arrows.jpg" alt=""><figcaption><p>Generating an API Access Token</p></figcaption></figure>

Name your token and click the **Generate New Token** button:

{% hint style="info" %}
Unless you plan to periodically refresh this token yourself, you can set **Expiration** to 'NEVER'.
{% endhint %}

<figure><img src="../../../../../.gitbook/assets/api-generate-token-arrows.jpg" alt=""><figcaption><p>Generating an API Access Token</p></figcaption></figure>

Copy the API Key into your text file or password manager:

<figure><img src="../../../../../.gitbook/assets/api-generate-token-save-arrows.jpg" alt=""><figcaption><p>Generating an API Access Token</p></figcaption></figure>

## 2. Set up an Web3 Provider

The PRE application requires access to an **Ethereum** and **Polygon** endpoint.&#x20;

{% hint style="info" %}
[Infura](https://infura.io/), [Quicknode](https://www.quicknode.com/), or [Alchemy](https://www.alchemy.com/) are commonly used providers. Each has their own pros and cons – review each carefully before choosing.
{% endhint %}

Sign up, login into your account, and setup an endpoint for Ethereum and Polygon.

Ensure you select **Mainnet** for both endpoints. You should get two addresses resembling a normal web addresses, starting with _https._ Copy each endpoint address to your text file or into your password manager.&#x20;

{% hint style="warning" %}
Do not share your endpoint addresses with anyone.
{% endhint %}

You should now have the following in your text file or password manager:&#x20;

* SSH key pair fingerprint
* API Access token
* Endpoint URL for Ethereum
* Endpoint URL for Polygon

<figure><img src="../../../../../.gitbook/assets/notepad.jpg" alt=""><figcaption><p>Example text file with all required data</p></figcaption></figure>

Keep these items readily available for the next step.

## 3. Install `nucypher-ops`&#x20;

Install the deployment script:&#x20;

```bash
$ pip install nucypher-ops
```

This will download the utility and install required dependencies. The process takes a few minutes.

<figure><img src="../../../../../.gitbook/assets/nucypher-ops-installed.jpg" alt=""><figcaption><p>nucypher-ops installation</p></figcaption></figure>

## 4. Generate a TACo Node&#x20;

Create a node:&#x20;

```bash
$ nucypher-ops nodes create
```

This command connects to your Digital Ocean account, spins up a VPS, configures and secures it, and installs all of the necessary software.&#x20;

The utility will ask which cloud service you are using. Answer accordingly and press ENTER**:**&#x20;

<figure><img src="../../../../../.gitbook/assets/nucypher-ops-do.jpg" alt=""><figcaption><p>Selecting VPS provider in nucypher-ops</p></figcaption></figure>

Input your API Key and press ENTER**:**&#x20;

<figure><img src="../../../../../.gitbook/assets/paste-api-key.jpg" alt=""><figcaption><p>Providing API Key in nucypher-ops</p></figcaption></figure>

Input your SSH Fingerprint and press ENTER:

<figure><img src="../../../../../.gitbook/assets/paste-ssh-fingerprint.jpg" alt=""><figcaption><p>Providing SSH fingerprint in nucypher-ops</p></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/setting-up-node-1.jpg" alt=""><figcaption><p>Creating VPS instance &#x26; PRE node</p></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/setting-up-node-2.jpg" alt=""><figcaption><p>VPS instance &#x26; PRE node created</p></figcaption></figure>

## 5. Deploy the Node&#x20;

Deploy the node (Ursula):&#x20;

```bash
$ nucypher-ops ursula deploy
```

Provide your ETH endpoint when requested and press ENTER.

Provide your Polygon endpoint when requested and press ENTER.

<figure><img src="../../../../../.gitbook/assets/ursula-deploy-1.jpg" alt=""><figcaption><p>Configuring and deploying TACo Node</p></figcaption></figure>

<figure><img src="../../../../../.gitbook/assets/ursula-deploy-2-arrows.jpg" alt=""><figcaption><p>TACo node ready to bond</p></figcaption></figure>

You can now bond your stake to a TACo node. The items at the bottom are your node's IP address and Operator address, which you will need for bonding.&#x20;

{% hint style="info" %}
Make sure you note down your operator address.&#x20;
{% endhint %}

## Nucypher-Ops Configuration

In case you need to make changes to the `nucypher-ops`configuration on your local machine, these are the default directories where config files are stored locally:

{% tabs %}
{% tab title="Linux" %}
```bash
~/.local/share/nucypher-ops
```
{% endtab %}

{% tab title="MacOS" %}
```bash
~/Library/Application Support/nucypher-ops
```
{% endtab %}
{% endtabs %}

{% hint style="danger" %}
Do not make changes to these files unless you know and understand what you are doing. Breaking these files may lead to your node becoming permanently inaccessible.

Do not make changes without making a backup.&#x20;
{% endhint %}

## Update TACo Node

If you need to update your node to a new version of TACo, run the following command:&#x20;

```bash
$ nucypher-ops ursula update
```

To learn about other `nucypher-ops` features:&#x20;

```bash
$ nucypher-ops --help
```
