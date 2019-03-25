# Broker-Lookup:  On Targeting Data Hosts with Users' Ledger Addresses

> ## List of *broker-lookup* Nodes
>
> | *host name or IP* | 
> | --- | 
> | https://lookup.overhide.io | 

## Motivation

To use *overhide* data with some service a user needs to tell the service which *overhide* broker hosts the user's data.

Prior to this, the user selected an *overhide* broker to host their data considering criteria which--among others--included the ledger the user wants to work through for [remuneration and authorization](remuneration-api.md).

This choice boiled down to which ledger's public-address the user wants to access their *overhide* data with.  This [user-address](glossary.md#user-address) must be from a ledger supported by the chosen broker as per the broker's available [remuneration keys](https://overhide.github.io/overhide/docs/broker.html#operation--capabilities-remuneration-providers-get).  

These decisions are made once, during onboarding onto a service.

After that--on all subsequent interactions--in order for a user to access their data they must qualify this access with their:

* [user-address](glossary.md#user-address), and the ability to sign for it (*secret-key* in wallet or otherwise)
* the ledger this [user-address](glossary.md#user-address) lives on: the [remuneration key](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey)
* the host name or IP of the broker hosting this data.

The purpose of [broker-lookup](https://overhide.github.io/overhide/docs/broker.html#tag-broker-lookup) is to enable users to login into their *overhide* supporting services with just the [user-address](glossary.md#user-address): without requiring the service to ask for, or the user to have to enter, ledger ([remuneration key](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey)) or broker (host name, IP) information.

It's envisioned that in case of crypto-currencies the [user-address](glossary.md#user-address) is something the user commonly interacts with through their crypto-wallet: the wallet signs on behalf of the [user-address](glossary.md#user-address).  

In case of fiat money--where [overhide-ledger](https://ohledger.com) is used for US dollars--the [user-address](glossary.md#user-address) might not be in a crypto-wallet and as such its *secret-key* is directly handled by the user.  When a [user-address](glossary.md#user-address) is handled directly with its *secret-key*, this can be considered similar to a *username* and *password* in traditional login setups.

Hence a [user-address](glossary.md#user-address) can be thought of as a fairly household concept.  Since [broker-lookup](https://overhide.github.io/overhide/docs/broker.html#tag-broker-lookup) allows users to discover the other information from just the [user-address](glossary.md#user-address), it makes for a streamlined user experience.

## High Level Design Details

Consider an *overhide* broker ecosystem connected to a couple ledgers--here we show Ethereum and BitCoin:

![](images/peer-mesh.svg)

In the figure above *alpha*, *beta*, *gamma*, *psi*, and *omega*, are all *overhide-brokers*.

The *alpha*, *gamma*, *psi*, and *omega* brokers interact with Ethereum's ledger through an Ethereum specific [remuneration provider](remuneration-api.md) via Ethereum specific addresses.

*Alpha* and *omega* also interact with BitCoin, along with *beta*.  These interactions are through some BitCoin specific *remuneration provider* via BitCoin addresses.

Notice that *alpha*, *beta*, and *gamma*, are paired with *broker-lookup peer node* functionality.  Because these are also *broker-lookup peer nodes*--above and beyond being *overhide* brokers--they are able to communicate via the *broker-lookup peer mesh*.  In addition to talking to eachother, they're also in communication with *broker-lookup peer nodes* that are standalone--not paired with *overhide* broker functionality:  *delta* and *eta*.

*Psi* and *omega* do not participate in the *broker-lookup peer mesh*.

Consider some client of some service who [activates](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) their data with *gamma*.  Upon activation--since *gamma* is a *broker-lookup peer node*--*gamma* publishes the fact that it's responsible for handling data for whatever Ethereum [user-address](glossary.md#user-address) the client furnished.  Now the *broker-lookup peer mesh* knows that resolution of the client's [user-address](glossary.md#user-address) involves the Ethereum ledger and that the data is currently handled by *gamma*.  

Going forward, any service on any device that this client accesses only needs the client to provide their [user-address](glossary.md#user-address):  as long as the service is leveraging the [broker-lookup APIs](https://overhide.github.io/overhide/docs/broker.html#tag-broker-lookup).  The service can resolve the remaining details, namely that: (1) it's an Ethereum [user-address](glossary.md#user-address) and (2) the client's data is on *gamma*.

Any such services in use by the client need only be configured with one or more "default" *broker-lookup peer nodes*; they no longer need the client to make them aware of specific brokers.  A node such as *delta* or *eta* would be likely candidates for such "default" lookup nodes; whom services access to resolve brokers in the rest of the mesh.  Such nodes exists with the sole purpose of providing broker resolutions.  They would likely have well maintained DNS entries and be well known in the ecosystem, e.g. [https://lookup.overhide.io](https://lookup.overhide.io).

If the client originally [activated](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) their data on *psi*, the value-add of the *broker-lookup* couldn't be leverged:  *psi* does not participate in the *broker-lookup peer mesh*.  Recall that for *overhide* brokers participation in *broker-lookup* is optional, although it does add a great deal of value to the ecosystem.

## Reference Implementation

Some specifics of the [broker-lookup peer node reference implementation](https://github.com/overhide/overhide-lookup) is touched upon below to give a more concrete foundation of how *broker-lookup* enables the *canonical* ecosystem.

![](images/peer-mesh-detail.svg)

In the figure above we now replaced BitCoin with [overhide-ledger](https://ohledger.com) to reflect the [remuneration keys](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey) available in the initial release.  

*Psi* and *omega* are not shown pointing to either Ethereum nor [overhide-ledger](https://ohledger.com) [remuneration providers](remuneration-api.md); instead they point to some mystery ledger.  The mystery ledger is not part of the explicitly supported [remuneration keys](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey) by the [spec](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey).  As such, [nodes participating in the broker-lookup mesh](https://github.com/overhide/overhide-lookup) do not have knowledge of this ledger.

The take-away here:  only [remuneration keys](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey) explicitly listed are supported in the *broker-lookup* mesh maintained by *overhide*.

The *overhide* *broker-lookup* functionality has additional constraints that must be adhered to by participants:

* the peer-mesh is a distributed hash table (DHT) on top of the Kademlia protocol
* each *broker-lookup peer node* will verify several signatures when new values are provided
* [user-addresses](glossary.md#user-address) will overwrite previous values indiscriminately 

### Payload Signatures and Verifications

When [active stewards](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) forward resolution payloads for [user-addresses](glossary.md#user-address) to the mesh, the payloads must be valid.

A payload constitutes:

```
{
  "address": "..",      
  "remunerationKey": "..",
  "activeBrokerAddress": "..",       
  "activeBrokerAddressSigned": "..", 
  "activeBrokerHost": "..",
  "activeBrokerHostSigned": "..",    
  "drainBrokerHost": "..",
  "drainBrokerHostSigned": "..",
  "timestamp": "..",
  "timestampSignedByAddress": "..",
  "timestampSignedByActiveBrokerAddress": ".."
}
```

The [user-address](glossary.md#user-address) key is included as *address*.

The following constitute a valid payload:

* *address* is the [user-address](glossary.md#user-address).
* *remunerationKey* is one of the supported [remuneration-keys](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey).
* *activeBrokerAddress* is the [broker-address](glossary.md#broker-address) of the [active steward](glossary.md#data-steward).
* *activeBrokerAddressSigned* is *activeBrokerAddress* signed by *address*: with the *secret-key* corresponding to [user-address](glossary.md#user-address).
    * since *address* signs *activeBrokerAddress*, only the owner of this [user-address](glossary.md#user-address) can update their [active steward's](glossary.md#data-steward) [broker-address](glossary.md#broker-address).
* *activeBrokerHost* is the *host name* or *IP* of [active steward](glossary.md#data-steward) broker.
    * this is the *host name* or *IP* to use for [all the API calls](https://overhide.github.io/overhide/docs/broker.html).
* *activeBrokerHostSigned* is *activeBrokerHost* signed by *activeBrokerAddress*
    * since *activeBrokerAddress* signs *activeBrokerHost*, the [active steward](glossary.md#data-steward) can change the *host name* or *IP* without involving the user.
    * this indirection is useful for load-balancing and other operations aspects of the broker.
* *drainBrokerHost* is the *host name* or *IP* of the [drain-host](glossary.md#drain-host), if any.
* *drainBrokerHostSigned* is *drainBrokerHost* signed by *address*
    * since *address* signs *drainBrokerHost*, only the owner of this [user-address](glossary.md#user-address) can update their [drain-host](glossary.md#drain-host).
* *timestamp* when information was published into *broker-lookup peer mesh*
    * [ISO 8601/RFC3339 format](https://xml2rfc.tools.ietf.org/public/rfc/html/rfc3339.html#anchor14)
    * must be recent/current (within last hour)
* *timestampSignedByAddress* is *timestamp* signed by *address*
    * must be present and valid if and only if *timestampSignedByActiveBrokerAddress* is not present and valid.
* *timestampSignedByActiveBrokerAddress* is *timestamp* signed by *activeBrokerAddress*
    * must be present and valid if and only if *timestampSignedByAddress* is not present and valid.

The owner of [user-address](glossary.md#user-address) signs both the *activeBrokerAddress* and (optionally) the *drainBrokerHost* directly controlling those values.  This is all done through the [activation APIs](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) depending on use case.

The *activeBrokerHost* is not signed by the owner of [user-address](glossary.md#user-address), it is instead signed by the [active broker](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship).  This gives the broker flexibility in moving the user's data around its cluster and update its internal references to the data.

The *timestamp* ensures "griefing" through replaying of old payloads is minimized.  Since either the owner of [user-address](glossary.md#user-address) or the [active broker](https://overhide.github.io/overhide/docs/broker.html#tag-data-stewardship) update the *broker-lookup peer mesh*, either one can sign the *timestamp*:  *timestampSignedByAddress* or *timestampSignedByActiveBrokerAddress*.

Every *broker-lookup peer node* is a full member of the Kademlia DHT and as such will be asked to write resolution payloads into its node storage.  No *broker-lookup peer node* will write corrupt resolution payloads into their node storage.  Corrupt payloads are ones that do not contain valid signatures for data indicated.  Such payloads will be unceremoniously dropped.

To verify signatures the payload will leverage the *is-signature-valid* method of the [remuneration API](remuneration-api.md) for the *remuneration provider* indicated by [remunerationKey](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey).

### Overwrite on Collision

A valid resolution payload (as per above) is all that's necessary for a *broker-lookup peer node* to accept and store data.

Should any new data for a [user-address](glossary.md#user-address) overwrite some previous data, the overwrite is allowed to happen without ceremony.

It is possible to contrive a case where two different ledgers using the same PKI scheme (Ethereum and [overhide-ledger](https://ohledger.com)) are used with the same [user-address](glossary.md#user-address) to store data on two different *overhide* brokers with two different [remuneration-keys](https://overhide.github.io/overhide/docs/broker.html#/definitions/RemunerationKey).  In this case one of the brokers will always remain unresolvable through *broker-lookup*.  

Collisions between [user-addresses](glossary.md#user-address) from two different owners, perhaps on different ledgers, are not a concern as these are improbable.
