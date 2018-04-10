# ![](docs/logo-sm.png) *overhide*  

*Over*tly *hide*: a system to store users' clandestine data within their purview and to enable rich collaborative services without the cost of private and proprietary infrastructure.

## About

At it's core *overhide* is solving a people problem.  Consider an app developer who wants to create something, but doesn't want to front time, money, resources, into infrastructure to support the solution.  Further consider a user who wants to use said service, but doesn't want to worry about who they're trusting with their data and information.  Although both likely want the ease of centralized hubs for discovery--e.g. app stores--they might not want the burden of maintaining some centralized relationship: another account/login/network.

For certain solutions--*overhide* isn't universally applicable--we want to free the app developer from the standard deployment model and free the user from trusting yet another provider.

Are users ready for *overhide*?  Although we all enjoy the benefits of centralization for discovery and distribution--e.g. app stores--we do want the freedom of not locking-in, owning our data, and keeping our particulars to ourselves.  

Services built on top of *overhide* can be as decentralized as the service author enables.  An app can be fully decentralized leveraging a distributed implementation of the *overhide* (data) broker, and a blockchain solution for remuneration.  Or said app could start with a simple on-premises instance of an *overhide* broker and no remuneration structure.

---

Within this repo you will find documentation and API specifications relevant to *overhide*.  Implementations of these APIs constitute *overhide* deployments that act together to broker storing of a service's user data with qualities described above.

The heart of *overhide* is a "broker" between a "service" and a "user".  It is a data-store that is completely uninterested in what the "service" is and who the "user" is.

A "service" could be an app who's authors don't want to be in the business of managing a backend for users' data.

A "user" could be a person who wants to own all the data used by the "service" and wants to remain pseudonymous to both the *overhide* "broker" instance and the "service".

For reference, below is a sample model of the above mentioned components.  Green arrows point to the artifacts documented by this repository.

![components](docs/provided.png)

* a user interfaces with a configured blockchain external to *overhide*
* a user uses the "service" to persist application data with *overhide*
* a user uses some "tooling" to import/export their data on their own terms, external to the application.
* both the application service and tooling use the "client library" to interface with an *overhide* broker

The above is one model of possible access patterns.  There is nothing precluding a service or tooling from using the APIs directly, foregoing a client library.

### Qualities of *overhide*

* open source specifications: to enable storing of data in the open
   * broker API for data storage
   * remuneration API to enable payment of brokerage and service subscriptions
* as available and reliable as the technology stack implementing the specification
* provides ability to remunerate brokerage and service provider via blockchain: pseudonymous subscription fees

### Benefits to Service

* no need to administer a backend: outsource reliability and availability to experts
* no need to be responsible for user data: empower user with their data security, no data breaches
* General Data Protection Regulation (GDPR) compliance
* no need to worry about who the users are: they're pseudonymous shadows
* can still collect service subscription fees using APIs via blockchain
* retains option to implement own *overhide* brokerage, own blockchain/tokens (leverage that business model); but *overhide* won't help making it proprietary

### Benefits to User

* user owns their data
* data is as secure as the user's access to it, and service's use of it
* depending on service, user might have flexibility to choose own *overhide* broker

### Expected Use Cases

The above benefits can only be reaped for certain expected use-cases of a "service".

* only suitable for certain data patterns where service's data can be reasonably expected to be fully managed within a rich client application: no use of SQL relational data, database-engine, indices, server-side processing, or server-side third-party integrations
* suitable to make decentralized apps (DApps) regardless of their use of blockchain technology: seems the right fit for DApps' off-chain data and no-chain DApps

## [Glossary & Notation](docs/glossary.md)

Please refer to the [glossary](docs/glossary.md) to get comfortable with terms and notation syntax used within the repo.

## [Identity](docs/identity.md)

Aim of *overhide* is to keep data private.  Anonymity is an important part of privacy.  See how [*overhide* leverages pseudonymous identity](docs/identity.md) to remunerate brokers.

## [Broker API](docs/broker-api/index.html)

An [API](https://jakubner.github.io/overhide/docs/index.html) exposed by *overhide* broker, provides access to broker's services.

## [Remuneration API](docs/remuneration-api/index.md)

An [API](docs/remuneration-api/index.md) to facilitate proof of payment for remunerating an *overhide* broker or a brokered service.  Likely abstracting some blockchain.

## [Client Library](docs/client-lib/index.md)

[JavaScript library](docs/client-lib/index.md) to be used by application (service), wraps/abstracts the APIs.

## [Sample Use Cases](docs/use-cases/index.md)

[Verbiage](docs/use-cases/index.md) documenting sample deployment scenarios and corresponding collaboration flows to demonstrate *overhide*.
