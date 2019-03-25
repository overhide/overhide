# Remuneration API

Core to the *overhide* system is [ledger-based authorization](https://github.com/overhide/ledgers.js/blob/master/why/why.md); whether for-pay or gratis.

The *overhide* Remuneration API enables this [ledger-based authorization](https://github.com/overhide/ledgers.js/blob/master/why/why.md) and permeates *overhide* features starting with the root concept of [identity](identity.md) (see section on "[subscriptions](identity.md#subscriptions)").

[Ledger-based authorization](https://github.com/overhide/ledgers.js/blob/master/why/why.md) is applied by *overhide* brokers on behalf of apps and services:  by virtue of using an *overhide* broker our apps and services gain full access to this authorization paradigm.

It is an API of a handful of HTTP methods exposed by various ledgers--blockchain and otherwise.

All *remuneration providers* expose an identical API contract: [just two methods](remuneration.md): this is the static version of the [API](remuneration.md) scrubbed from the *overhide* Rinkeby Ethereum *remuneration provider*.

At this moment we have the following *overhide* remuneration providers exposing "live" API documentation:

#### Ethereum:

* [rinkeby](https://rinkeby.ethereum.overhide.io/swagger.html)
* [mainnet](https://ethereum.overhide.io/swagger.html)

#### US Dollars:

* [test *overhide-ledger*](https://test.ohledger.com/swagger.html) with corresponding [application](https://test.ohledger.com)
* [production *overhide-ledger*](https://ohledger.com/swagger.html) with corresponding [application](https://ohledger.com)