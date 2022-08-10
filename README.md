---
description: Trust for Data-in-Motion
---

# What is Ockam?

Ockam is a suite of **open source** tools, programming libraries and cloud services to orchestrate end-to-end encryption, mutual authentication, key management, credential management & authorization policy enforcement — at scale.

Modern applications are distributed and have an unwieldy number of interconnections that must trustfully exchange data. Ockam makes it simple to build **secure by-design** applications that have granular control over every access decision.

#### **Mutual authentication and end-to-end data integrity**

In order to trust information or instructions, that are received over the network, applications must **authenticate** all senders and **verify the integrity of data** **received** to assert what was received is exactly what was sent — free from errors or en-route tampering.

Application layer communication is also usually bi-directional since, at the very least, we have to acknowledge receipt of data to its senders. This means that authentication and the data integrity guarantee within applications must be **mutual** between any two communicating parts.

With Ockam, applications can, in a few lines of code, create mutually authenticated [secure channels](reference/secure-channels.md) that guarantee end-to-end data integrity to senders and receivers of data.

#### **Zero trust in network boundaries,** third-party services, and infrastructure

Applications have moved out of enterprise data centers into multi-tenant cloud and edge environments. The operate in untrusted networks and increasingly rely on third-party managed services and infrastructure. This creates exponential growth in the **vulnerability surface of our application data**.

Data, within applications, routinely flows over complex, multi-hop, multi-protocol routes — across network boundaries, beyond data centers, through queues and caches, via gateways and brokers — before reaching its end destination. The vulnerability surfaces of all these dependencies get added to the vulnerability surface of our application data and make it _unmanageable_.

Ockam [secure channels](reference/secure-channels.md) enable end-to-end **application layer encryption** of **data-in-motion**. The data integrity and confidentiality guarantee, of these channels, creates a deny-by-default security posture that minimizes our vulnerability surface and gives our application true control over every data or service access decision. ****&#x20;

#### Identity driven, least privileged, per-request access and privacy controls





To take back control of the security and privacy properties&#x20;



The number ways our application's data can be&#x20;

Zero trust presents a shift from a location-centric model to a more data-centric approach

#### &#x20;

#### Manage keys, identities and credentials – safely, at scale





&#x20;











### Hello Ockam

Let's create a mutually authenticated and authorized secure channel, in three simple commands and then send an end-to-end encrypted message through this channel.

{% code overflow="wrap" %}
```bash
# Install Ockam Command using Homebrew
> brew install build-trust/ockam/ockam

# Create three Ockam nodes n1, n2 & n3
> for i in {1..3}; do ockam node create "n$i"; done

# Create a mutually authenticated, authorized, end-to-end encrypted secure channel
# from node n1, via node n2, over two tcp hops to api service on node n3.
#
# Then send an end-to-end encrypted message to the uppercase service on n3,
# using this channel.
# 
# n2 cannot see or tamper the onroute message
> ockam secure-channel create --from n1 --to /node/n1/node/n2/node/n3/service/api
    | ockam message send "hello ockam!" --from n1 --to -/service/uppercase
HELLO OCKAM
```
{% endcode %}

