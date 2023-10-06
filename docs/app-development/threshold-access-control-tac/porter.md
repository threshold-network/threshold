# Porter

## Overview

Porter can be described as the _“Infura for TACo”_. Porter is a web-based service that performs TACo-based protocol operations for applications.

Its goal is to simplify and abstract the complexities surrounding the TACo protocol to negate the need for applications to interact with it via a Python client. Porter introduces the TACo protocol to cross-platform functionality, including web and mobile applications.

<figure><img src="../../.gitbook/assets/porter_diagram.png" alt=""><figcaption></figcaption></figure>

Any publicly available Porter can be used to interface with the Threshold Network, or some application developers opt to [run their own](porter.md#running-a-porter-instance).

## Running a Porter Instance

### Security Considerations

* **HTTPS:** To run the Porter service over HTTPS, it will require a TLS key and a TLS certificate.
* **CORS:** Allowed origins for [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin\_resource\_sharing)
* **Authentication:** Usage restriction via authentication protocols, e.g. basic authentication etc.

{% hint style="info" %}
Ideally, you would run Porter behind a reverse proxy (e.g. [nginx](https://www.nginx.com/)) for additional functionality such as HTTPS, CORS, authentication etc.
{% endhint %}

### Run via Docker

{% hint style="info" %}
By default, Porter runs on port `9155`.
{% endhint %}

1.  Get the latest `porter` image

    ```bash
    $ docker pull nucypher/porter:latest
    ```
2.  Run Porter HTTP Service on port 80

    ```bash
    $ docker run -d --rm \
    --name porter \
    -v ~/.local/share/nucypher/:/root/.local/share/nucypher \
    -p 80:9155 \
    --restart=unless-stopped \
    nucypher/porter:latest \
    nucypher porter run \
    --eth-provider <YOUR WEB3 PROVIDER URI> \
    --network <NETWORK NAME>
    ```

    The command above is for illustrative purposes and can be modified as necessary.&#x20;
3. Porter will be available on default port 80 (HTTP)
4.  View Porter logs

    ```bash
    $ docker logs -f porter
    ```
5.  Stop Porter service

    ```bash
    $ docker stop porter
    ```
